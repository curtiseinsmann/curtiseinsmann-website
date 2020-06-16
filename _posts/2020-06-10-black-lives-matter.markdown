---
layout: single
title:  "Black Lives Matter"
date:   2020-06-10 09:00:00 -0400
categories: donations
tags: blacklivesmatter
---

<script src="{{ base.url | prepend: site.url }}/assets/js/blm.js"></script>

My employer, Amazon, will 100% match employee contributions to the following organizations, up to $10,000 per employee. I am accepting donations through my personal Venmo or PayPal in an attempt to maximize this opportunity. I will message you the receipt after I submit your donation through an Amazon internal website.

***Please submit donations by 11:59pm EST on June 30, 2020.  I will send you a receipt within 72 hours of your donation. Thanks for your donation!***

<p>
<div id="form-surrounding-section" style="padding-bottom: 20px">
  <div id="step-0" style="height: 500px">
    <h2>Choose your organization. Then click Next.</h2>

    <div style="white-space:nowrap; padding-left: 20px">
      <label for="all">
        <input type="radio" id="all" name="organization" value="all organizations split equally" 
          onclick="updateChosenOrganization(this.value);"
          style="display: inline"/>
        <strong>All below organizations, split equally</strong>
      
      </label>
    </div>

    <div style="white-space:nowrap; padding-left: 20px">
      <label for="aclu">
        <input type="radio" id="aclu" name="organization" value="ACLU Foundation" 
          onclick="updateChosenOrganization(this.value);"
          style="display: inline"/>
          ACLU Foundation <a href="https://www.aclu.org/" target="_blank">link</a>
      
      </label>
    </div>

    <div style="white-space:nowrap; padding-left: 20px">
      <label for="blm">
        <input type="radio" id="blm" name="organization" value="Black Lives Matter"
          onclick="updateChosenOrganization(this.value);"
          style="display: inline"/>
        Black Lives Matter <a href="https://blacklivesmatter.com/" target="_blank">link</a>
      
      </label>
    </div>

    <div style="white-space:nowrap; padding-left: 20px">
      <label for="brennancenter">
        <input type="radio" id="brennancenter" name="organization" value="Brennan Center for Justice"
          onclick="updateChosenOrganization(this.value);"
          style="display: inline"/>
        Brennan Center for Justice <a href="https://www.brennancenter.org/" target="_blank">link</a>
      
      </label>
    </div>

    <div style="white-space:nowrap; padding-left: 20px">
      <label for="eji">
        <input type="radio" id="eji" name="organization" value="Equal Justice Initiative"
          onclick="updateChosenOrganization(this.value);"
          style="display: inline"/>
        Equal Justice Initiative <a href="https://eji.org/" target="_blank">link</a>
      
      </label>
    </div>

    <div style="white-space:nowrap; padding-left: 20px">
      <label for="lawyerscommittee">
        <input type="radio" id="lawyerscommittee" name="organization" 
          value="Lawyers’ Committee for Civil Rights Under Law" 
          onclick="updateChosenOrganization(this.value);"
          style="display: inline"/>
        Lawyers’ Committee for Civil Rights Under Law <a href="https://www.lawyerscommittee.org/" 
          target="_blank">link</a>
      
      </label>
    </div>

    <div style="white-space:nowrap; padding-left: 20px">
      <label for="naacp">
        <input type="radio" id="naacp" name="organization" value="NAACP" 
          onclick="updateChosenOrganization(this.value);"
          style="display: inline"/>
        NAACP <a href="https://www.naacp.org/" target="_blank">link</a>
      
      </label>
    </div>

    <div style="white-space:nowrap; padding-left: 20px">
      <label for="nationalbar">
        <input type="radio" id="nationalbar" name="organization" value="National Bar Association"
          onclick="updateChosenOrganization(this.value);"
          style="display: inline"/>
        National Bar Association <a href="https://www.nationalbar.org/" target="_blank">link</a>
      
      </label>
    </div>

    <div style="white-space:nowrap; padding-left: 20px">
      <label for="nmaahc">
        <input type="radio" id="nmaahc" name="organization" 
          value="National Museum of African American History and Culture" 
          onclick="updateChosenOrganization(this.value);"
          style="display: inline"/>
        National Museum of African American History and Culture <a href="https://nmaahc.si.edu/" target="_blank">
          link
        </a>
      
      </label>
    </div>

    <div style="white-space:nowrap; padding-left: 20px">
      <label for="nul">
        <input type="radio" id="nul" name="organization" value="National Urban League" 
          onclick="updateChosenOrganization(this.value);"
          style="display: inline"/>
        National Urban League <a href="https://nul.org/" target="_blank">link</a>
      
      </label>
    </div>

    <div style="white-space:nowrap; padding-left: 20px">
      <label for="tmcf">
        <input type="radio" id="tmcf" name="organization" value="Thurgood Marshall College Fund" 
          onclick="updateChosenOrganization(this.value);"
          style="display: inline"/>
        Thurgood Marshall College Fund <a href="https://www.tmcf.org/" target="_blank">link</a>
      
      </label>
    </div>

    <div style="white-space:nowrap; padding-left: 20px">
      <label for="uncf">
        <input type="radio" id="uncf" name="organization" value="UNCF (United Negro College Fund)" 
          onclick="updateChosenOrganization(this.value);"
          style="display: inline"/>
        UNCF (United Negro College Fund) <a href="https://uncf.org/" target="_blank">link</a>
      
      </label>
    </div>

    <div style="white-space:nowrap; padding-left: 20px">
      <label for="yearup">
        <input type="radio" id="yearup" name="organization" value="Year Up" 
          onclick="updateChosenOrganization(this.value);"
          style="display: inline"/>
        Year Up <a href="https://www.yearup.org/" target="_blank">link</a>
      
      </label>
    </div>
  </div> <!-- step-0 -->

  <div id="step-1" style="display: none">
    <h2>Enter your instagram or name (so I can send you a receipt). Then click Next.</h2>
    <input type="text" id="insta-name" placeholder="Instagram/Name" style="width: 50%;" 
      oninput="updateInstaSlashName(this.value);"/>
  </div>

  <div id="step-2" style="display: none">
    <h2>Click "Copy message to clipboard!" Then click Next.</h2>
    <button onclick="copyPromptToClipboard();">Copy message to clipboard!</button> 
    <span id="info-copied-span" style="display: none">Copied!</span>

    <textarea readonly id="donation-prompt" rows="3" cols="50">Please send my donation to (org). Please send the receipt to (insta).</textarea>
  </div> <!-- step-2 -->

  <div id="step-3" style="display: none">

    <h2>Open <a href="https://venmo.com" target="_blank">Venmo</a> and send your donation to <strong>@Curtis-Einsmann</strong>. Use the message you've already copied to your clipboard.</h2>

    If you <strong>don't</strong> have Venmo, click Next.

  </div> <!-- step-3 -->

  <div id="step-4" style="display: none">

    <h2>Don't have Venmo? Use the below button to donate with PayPal. Use the message you've already copied to your clipboard, when prompted (see the screenshot). You may have to go through some annoying captcha on mobile.</h2>

    <form action="https://www.paypal.com/cgi-bin/webscr" method="post" 
      target="_blank" style="background-color: inherit">
      <input type="hidden" name="cmd" value="_donations" />
      <input type="hidden" name="business" value="6R67F98LS379E" />
      <input type="hidden" name="item_name" value="Black Lives Matter Curtis Einsmann Match" />
      <input type="hidden" name="currency_code" value="USD" />
      <input type="image" src="https://www.paypalobjects.com/en_US/i/btn/btn_donate_LG.gif" 
        border="0" name="submit" title="Donate to Black Lives Matter Curtis Einsmann Match via PayPal" 
        alt="Donate with PayPal button" style="height: 50px"/>
      <img alt="" border="0" src="https://www.paypal.com/en_US/i/scr/pixel.gif" width="1" height="1" />
    </form>

    <img src="/assets/images/donate-paypal-blm.png" style="width: 60%;"/>
  </div> <!-- step 4 -->
  
</div>
</p>

<div style="padding-top: 5px; padding-bottom: 5px;">
<button id="prev-button" type="button" class="btn btn--inverse btn--x-large" onclick="updateState('prev')" disabled>
  Previous
</button>
<button id="next-button" type="button" class="btn btn--success btn--x-large" onclick="updateState('next');">
  Next
</button>
</div>
