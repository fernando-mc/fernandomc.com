---
title: Contact Me
comments: false
type: page
---

To get in touch with me you can:

- Email me - [fernandomc.sea@gmail.com](mailto:fernandomc.sea@gmail.com)
- Tweet to me - [https://www.twitter.com/fmc_sea](https://www.twitter.com/fmc_sea)
- Contact me on [LinkedIn](www.linkedin.com/in/fmc-sea/)

<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.1.1/jquery.min.js"></script>
<script src='https://www.google.com/recaptcha/api.js'></script>

<div id='#form-div'>
  <p>Or fill out this form:</p>
  <div class="g-recaptcha" data-sitekey="6LcXNhAUAAAAAD5LX_MTjJC7cNDuDPesw2NKljjH"></div>
  <br>
  <form id="contact-form">
    <label for="name">Name:</label>
    <input type="text" id="name" placeholder="Your name" />
    <br><br>
    <label for="email">Email:</label>
    <input type="email" id="email" placeholder="Your email"/>
    <br><br>
    <label for="message">Message:</label>
    <textarea id="message" rows="3" placeholder="Your message"></textarea>
    <br><br>
    <button type="submit">Submit</button>
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