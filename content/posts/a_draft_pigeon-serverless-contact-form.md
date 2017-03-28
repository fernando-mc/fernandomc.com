+++
Tags = [
  "",
  "Serverless",
  "Python",
  "AWS",
  "AWS Workspaces",
  "Windows 10",
  "Testing",
]
Categories = [
  "AWS",
]
menu = "main"
date = "2017-03-26T16:06:48-07:00"
title = "Pigeon - The Serverless Contact Form"
draft = true
Description = ""

+++


I recently moved my personal website to a static site that runs on AWS S3 and a few other AWS services. While static sites are awesome, this setup forces me to get creative to get dynamic functionality on my site. Here's how I worked around these limitations and created Pigeon - a serverless contact form based on AWS services.
<!--more-->

We'll start be going through required setup steps for this project then look at the actual [Pigeon code](https://github.com/fernando-mc/pigeon) and walk through some AWS configuration and common issues you might see implementing Pigeon.

**Setup**

I'll be assuming that you have a static website setup somewhere already ([Make one here](http://docs.aws.amazon.com/gettingstarted/latest/swh/website-hosting-intro.html)) and that you know some HTML, JavaScript, and Python (though technically you _could_ replace the Python with JavaScript).

1. Sign up for Google's [reCAPTCHA](https://www.google.com/recaptcha/intro/invisible.html). Pick reCAPTCHA V2, which validates users with the "I'm not a robot" checkbox you might have seen elsewhere. You'll want to setup your domain and localhost within the configuration steps if you'll be testing things locally.

![Google reCAPTCHA](/images/google_recaptcha.png)

2. Make an AWS Account - [https://aws.amazon.com/free/](https://aws.amazon.com/free/)

3. Clone the [Pigeon code](https://github.com/fernando-mc/pigeon.git) 

```bash
git clone https://github.com/fernando-mc/pigeon.git
```

4. 




**Let's Look at Some Code**

First is the client side HTML form that handles the input
![Pigeon HTML](/images/pigeon_html_form.png)

Any questions? Feel free to use my (you guessed it!) serverless contact form! You can also say hi to me on Twitter. If you’re interested in learning more about AWS Lambda check out my course on Pluralsight.com https://www.pluralsight.com/courses/aws-developer-introduction-aws-lambda



If you’d like a free 30 day trial just follow me on twitter and shoot me a message.

**Potential Problems**

Here's a few common problems you might encounter when implementing Pigeon.

_CORS Issues_ 

It's possible that by using API gateway you'll encounter several issues with Cross Origin Resource Sharing. You'll need to explicitly enable this in order to POST to the AWS API Gateway endpoint. On an AWS static site you can usually do this within the Route 53 or Cloudfront Settings. 


3. Upload and add tags and categories
4. Check publish date and date
5. Add all the other front matter
6. Check summary
