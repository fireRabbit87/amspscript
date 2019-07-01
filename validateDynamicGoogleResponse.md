## Validate a customer from Google Captcha 

If the client is very picky on making sure that the there are no robots that randomly sign up to their page and making their marketing cloud instance have unnecessary users signed up, they may ask you to use some sort of captcha program. In the past, I've written my own captcha program, but it's easier to use one created by others, and [Google](https://developers.google.com/recaptcha/) is most well known and most used.

You can get the secret and key once you have signed up to a the Google Recaptcha program. The best option would be to use a client's emal rather than you're company's generic email.

### Part 1 : Add the code to your page
```html
<div class="g-recaptcha" data-sitekey="YOUR_KEY"></div>
```

```javascript
$(document).ready(function() {
  var contactForm = $("#contact_form");
  contactForm.on("submit", function(e) {
    //Prevent the default behavior of a form
    //Our AJAX POST
    $.ajax({
      type: "POST",
      data: {
        //THIS WILL TELL THE FORM IF THE USER IS CAPTCHA VERIFIED.
        captcha: grecaptcha.getResponse()
      },
      success: function() {
        // you can remove this line if you like, it's not necessary, more for testing purposes
        console.log("YOUR FORM SUBMITTED CORRECTLY");
        
      }
    })
  });
});
```

### Part 2 : Validate the information on your processing page

Place this code at the top of your processing page. It will make sue that the verification from the customer is valid. Further it will give you a response.
```html
%%[
SET @response = QUERYPARAMETER("g-recaptcha-response")
SET @captchaSecret = "YOUR_SECRET"
SET @url = CONCAT("https://www.google.com/recaptcha/api/siteverify?secret=", @captchaSecret, "&response=", @response)
SET @content = TREATASCONTENT(@url)
SET @verify = HTTPGET(@url, false, 1, @CallStatus))
]%%
```

This code comes directly below the response code above. You will need to "check" if the customer has made a valid entry.
```javascript
<script runat=server>
  Platform.Load("Core","1");
  var myCaptcha = Variable.GetValue("@verify");
  var def = Platform.Function.ParseJSON(myCaptcha);
  var res = def.success;
  Variable.SetValue("@success", res);
</script>
```
From the javascript code above, the `res` variable will give you a true/false value and from there you can use that variable; re-assigned as an AMPscript variable to do your checks and further process the data from your form. 
```html
IF @success == "true" THEN
  //go to success page
ELSE
  //go to failure page
ENDIF
```
