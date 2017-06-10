---
title: Contact Me
comments: false
type: page
---

To get in touch you can:

- [Email me]({{% contact_email %}})
- [Tweet to me]({{% my_twitter %}})
- [Connect with me on LinkedIn]({{% my_linkedin %}})

<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.1.1/jquery.min.js"></script>
<script src='https://www.google.com/recaptcha/api.js'></script>

<div id='#form-div'>
  <p>Or fill out this form:</p>
  <form id="contact-form">
    <label for="name">Name:</label>
    <br>
    <input type="text" id="name" placeholder="Your name" />
    <br>
    <label for="email">Email:</label>
    <br>
    <input type="email" id="email" placeholder="Your email"/>
    <br>
    <label for="message">Message:</label>
    <br>
    <textarea style="margin-bottom: 20px" id="message" rows="3" placeholder="Your message"></textarea>
    <div class="g-recaptcha" data-sitekey="6LcXNhAUAAAAAD5LX_MTjJC7cNDuDPesw2NKljjH"></div>
    <button style="margin-top: 20px" type="submit">Submit</button>
  </form>
</div>

<script>
var URL = 'https://1r3pcfbnq6.execute-api.us-east-1.amazonaws.com/prod/contact';

$('#contact-form').submit(function (event) {
  event.preventDefault();
  var captchta_response = grecaptcha.getResponse();
  console.log(captchta_response);
  var json_post_data = {
    name: $('#name').val(),
    email: $('#email').val(),
    message: $('#message').val(),
    captcha: captchta_response
  };
 

  $.ajax({
    type: 'POST',
    url: URL,
    dataType: 'json',
    crossDomain: true,
    contentType: 'application/json',
    data: JSON.stringify(json_post_data),
    success: function (responseData, textStatus, jqXHR) {
      console.log('in success message')
      if(responseData.status == 'success'){
        console.log('success!');
        document.getElementById("#form-div").innerHTML = "<p> Your form was successfully submitted!</p>"
      };
      if(responseData.message == 'Captcha Invalid'){
        console.log('Captcha FAIL. TRY AGAIN!')
      };
    },
  })
})


</script>