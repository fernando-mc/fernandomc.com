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
publishdate = "2017-03-26T16:06:48-07:00"
title = "Pigeon - The Serverless Contact Form"
Description = ""

+++


A few months ago I moved my personal website to a static site that runs on AWS S3 and a few other AWS services. While static sites are awesome, this setup forces me to get creative to get dynamic functionality on my site. If you're in a similar situation you might be interested in Pigeon - a competely serverless contact form I created using AWS services and Google reCAPTCHA V2.

<!--more-->

In this post I'll be assuming that you have a static website setup somewhere already ([You can make one here](http://docs.aws.amazon.com/gettingstarted/latest/swh/website-hosting-intro.html)) and that you know some HTML, JavaScript and Python.

Here's what you'll need to do:

1. Go through a few required setup steps for this project
2. Look at the actual [Pigeon code](https://github.com/fernando-mc/pigeon) 3. Walk through the AWS configuration process
4. Review common issues you could see while implementing Pigeon

**Setup**

1. First, sign up for Google's [reCAPTCHA](https://www.google.com/recaptcha/intro/invisible.html). Pick reCAPTCHA V2, which validates users with the "I'm not a robot" checkbox you might have seen elsewhere. You'll want to setup your website domain and localhost within the configuration steps.

![Google reCAPTCHA](/images/google_recaptcha.png)

2. Make an AWS Account if you don't have one already - [https://aws.amazon.com/free/](https://aws.amazon.com/free/)

3. Clone the [Pigeon code](https://github.com/fernando-mc/pigeon.git) 

```bash
git clone https://github.com/fernando-mc/pigeon.git
```

**Let's Look at Some Pigeon Code**

First is the client side HTML form that handles the input:

![Pigeon HTML](/images/pigeon_html_form.png)

**Potential Problems**

Here's a few common problems you might encounter when implementing Pigeon.

_CORS Issues_ 

It's possible that by using API gateway you'll encounter several issues with Cross Origin Resource Sharing. You'll need to explicitly enable this in order to POST to the AWS API Gateway endpoint. On an AWS static site you can usually do this within the Route 53 or Cloudfront Settings. 

Any questions? Feel free to use my (you guessed it!) [serverless contact form](https://www.fernandomc.com/contact/)! You can also say hi to me on [Twitter]({{% my_twitter %}}). If you’re interested in learning more about AWS Lambda check out my course on Pluralsight.com https://www.pluralsight.com/courses/aws-developer-introduction-aws-lambda