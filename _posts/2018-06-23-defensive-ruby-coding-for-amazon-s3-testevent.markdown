---
layout: single
title:  "Defensive Ruby Coding for Amazon S3's TestEvent"
date:   2018-06-23 09:00:00 -0400
categories: technical
tags: ruby aws s3 sns sqs
---

The `s3:TestEvent` notification from [Amazon S3][S3] may come as an unpleasant surprise.  It depends on how you've written code to parse your [Amazon SNS][SNS] notifications.  In this post, we'll do a deep dive into how we can code defensively against these kinds of anomalies in Ruby.

### Initial Architecture

Let's consider a system with some basic AWS architecture.

![Initial AWS Architecture]({{ "/assets/images/inital-aws-architecture.001.jpeg" }})
*Figure 1 -- processing S3 objects with key prefix foo/*

In *Figure 1*, we've set up an S3 bucket, called `my-s3-bucket`.  S3 supports event-driven messages to be sent whenever objects (files) are uploaded to the bucket.  These event-driven messages are called SNS topics. We've configured the S3 bucket to publish an SNS topic, called `my-foo-sns-topic`, for any S3 object uploaded to `my-s3-bucket` under the key `foo/`.  

We also have an [Amazon SQS][SQS] queue, `my-sqs-queue` which is subscribed to this SNS topic.  We're polling this queue for messages using the [AWS SDK for Ruby][SDK] to download the file and do some processing on it.

Since we're good engineers and follow security best practices, we've put strict permissions on `my-sqs-queue`: the SNS topic `my-foo-sns-topic` is the only entity that can send messages to the queue.  Thus, we're pretty confident that we'll only be receiving predictable SNS JSON messages into `my-sqs-queue` that look something like this:

{% highlight json %}
{
  "Type" : "Notification",
  "MessageId" : "4bb76612-bdb9-59d1-bc73-35b11721b127",
  "TopicArn" : "arn:aws:sns:us-east-1:123123123123:FooNotificationTopic",
  "Subject" : "Amazon S3 Notification",
  "Message" : "{\"Records\":[{\"eventVersion\":\"2.0\",\"eventSource\":\"aws:s3\",\"awsRegion\":\"us-east-1\",\"eventName\":\"ObjectCreated:Put\",\"requestParameters\":\"responseElements\":\"s3\":{\"s3SchemaVersion\":\"1.0\",\"bucket\":{\"name\":\"my-s3-bucket\",\"ownerIdentity\":\"arn\":\"arn:aws:s3:::my-s3-bucket\"},\"object\":{\"key\":\"foo/obj.json\",\"size\":2388}}}]}",
  ...
}
{% endhighlight %}

We want to parse the above messages to get the S3 bucket and key name, so that we can download the file from S3 and do some processing on it.  Since we're expecting a predictable message format, we've written our parsing code like the following:

{% highlight ruby %}
@sqs = Aws::SQS::Client.new(
  region: 'us-east-1', 
  credentials: Aws::Credentials.new(
    creds['access_key_id'], creds['secret_access_key']
  )
)

sqs_response = @sqs.receive_message(
  queue_url: QUEUE_URL,
  max_number_of_messages: 1,
  visibility_timeout: 10,
  wait_time_seconds: 10
)

sqs_response.messages.each do |sqs_message|
  # Notice the assumptions about the 
  # message format here...
  s3 = JSON.parse(
    JSON.parse(sqs_message.body)['Message']
  )['Records'].first['s3']

  bucket = s3['bucket']['name']
  key = s3['object']['key']

  # Download the object and do some processing...

  # Delete the message after we're done with it
  @sqs.delete_message(
    queue_url: QUEUE_URL,
    receipt_handle: sqs_message.receipt_handle
  )
end
{% endhighlight %}

### Adding a second SNS Topic

Now, let's say we want to publish an SNS topic, not only for objects under the `foo/` key, but also for objects under the `bar/` key.  We're likely to make these modifications in the following order:

1. Create a new SNS topic, called `my-bar-sns-topic`
2. Subscribe `my-sqs-queue` to the new `my-bar-sns-topic`
3. Modify the S3 bucket, `my-s3-bucket` to publish to `my-bar-sns-topic` whenever objects are uploaded under the `bar/` key

The architecture would now look like this:

![Subsequent AWS Architecture]({{ "/assets/images/subsequent-aws-architecture.001.jpeg" }})
*Figure 2 -- Processing S3 objects with key prefixes foo/ and bar/*

Sometime after performing step 3, we notice that our Ruby code has failed, due to the following error message:

```
undefined method `first' for nil:NilClass (NoMethodError)
```

We check the message that failed, and it looks like this:

{% highlight json %}
{
  "Type" : "Notification",
  "MessageId" : "3f9f4bea-a0df-58b4-9a44-184915e7c3da",
  "TopicArn" : "arn:aws:sns:us-east-1:123123123123:FooNotificationTopic",
  "Subject" : "Amazon S3 Notification",
  "Message" : "{\"Service\":\"Amazon S3\",\"Event\":\"s3:TestEvent\",\"Bucket\":\"my-unique-s3-bucket-name\"}",
  ...
}
{% endhighlight %}

*What?!*  How did this message end up in our queue?  This isn't the message format we've come to know and love...

### Coding against the s3:TestEvent

It turns out that the `s3:TestEvent` is an SNS notification that is sent whenever the notification configuration on the S3 bucket is established.  Most of the time, this test event goes unnoticed, because upon initial creation of these resources, there usually aren't any active processors of the messages.  You're likely to only run into the `s3:TestEvent` when making modifications on resources that are already consuming the data.

As you can see from the JSON message above, the embedded JSON string for the "Message" value does not have the "Records" key.  Thus, our code fails when trying to call `['Records']` on a `nil` value.  We can utilize [Ruby's `fetch` method][fetch] to clean our code up and avoid these errors (I've left out the intialization and `receive_message` blocks).

{% highlight ruby %}
def delete_sqs_message(sqs_message)
  @sqs.delete_message(
    queue_url: QUEUE_URL,
    receipt_handle: sqs_message.receipt_handle
  )
end  

sqs_response.messages.each do |sqs_message|
  unless (message = JSON.parse(sqs_message.body).fetch('Message', false))
    delete_sqs_message(sqs_message)
    next
  end

  unless (records = JSON.parse(message).fetch('Records', false))
    delete_sqs_message(sqs_message)
    next
  end

  s3 = records.first['s3']

  bucket = s3['bucket']['name']
  key = s3['object']['key']

  # Download the object and do some processing...

  # Delete the message after we're done with it
  delete_sqs_message(sqs_message)
end
{% endhighlight %}

I think we can all agree that the `s3:TestEvent` is somewhat inconvenient in these situations.  However, if we write our code defensively, we can protect ourselves from this, or other unexpected messages, in our SQS queue.  Coding defensively is a good habit to get into, especially when making calls to external dependencies.

[S3]: https://aws.amazon.com/documentation/s3/
[SNS]: https://aws.amazon.com/documentation/sns/
[SQS]: https://aws.amazon.com/documentation/sqs/
[SDK]: https://aws.amazon.com/sdk-for-ruby/
[fetch]: https://docs.ruby-lang.org/en/2.3.0/Hash.html#method-i-fetch
