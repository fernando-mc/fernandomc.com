+++
draft = false
Description = "Learn how to deploy your website to AWS S3 using Serverless Finch and the Serverless Framework"
Tags = [
  "Serverless",
  "Static Sites",
  "AWS",
  "Serverless Framework",
  "Serverless Finch",
  "S3",
]
Categories = [
  "AWS",
  "Serverless"
]
title = "Deploy Your Static Site to AWS S3 with Serverless Finch and the Serverless Framework"
menu = "main"
date = "2018-01-23T14:08:12-05:00"
publishdate = "2018-01-23T14:08:12-05:00"

+++

A frequent issue I ran into when working with the [Serverless Framework](https://www.serverless.com) on smaller projects was that when I wanted to deploy a static website frontend that integrated with my Serverless backend I wasn't quite sure how to do it. After some searching, I [adopted and republished](https://www.fernandomc.com/posts/publishing-serverless-finch/) a broken Serverless Framework plugin for that exact purpose.

And so Serverless Finch was born.

Now it's relatively easy to deploy your own website to AWS S3 while using the Serverless Framework and Serverless Finch. Here's how you can do it.

<!--more-->

**Some Assumptions**

I'm going to assume that you have used the Serverless Framework before to some extent. If you have questions with anything in that realm you can always head over to [their website](https://www.serverless.com) to read up on their documentation.

I'm also going to assume that you're all setup with the Serverless Framework and the AWS CLI and credentials you need for it to work. There are various permissions that Serverless Finch (and the Serverless Framework itself) require to function properly so make sure you are good to go there too.

**Let's Get Started**

Starting in a directory with your Serverless Framework project go ahead and run:

```
npm install --save serverless-finch
``` 

This will install the plugin for you so that you can use it in combination with the Serverless Framework.

Next, update your `serverless.yml` configuration file so that it includes some configuration values that  

Let's say you want to deploy your static website to AWS 

