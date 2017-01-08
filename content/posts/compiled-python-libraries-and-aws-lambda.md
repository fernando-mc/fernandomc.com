+++
draft = false
title = "Compiled Python Libraries and AWS Lambda"
Description = "Options for using compiled Python libraries with AWS Lambda"
Tags = [
  "Serverless",
  "Python",
  "AWS",
  "Lambda",
]
Categories = [
  "Serverless",
  "AWS",
  "Lambda",
]
menu = "main"
date = "2017-01-09T14:08:12-05:00"
publishdate = "2017-01-09T14:08:12-05:00"
+++

Recently I was trying to use the psycopg2 libraries for Python in combination with AWS Lambda. My first hint that this was probably overkill was that the function package, when zipped, started exceeding 50MB. AWS console errors quickly reminded me that 50MB is the size limit for Lambda packages.
<!--more-->
After correcting a few mistakes (my virtualenv bundled in with my dependencies - Whoops!) and trimming some dependencies I started testing again. This time with a function package just a few bytes under the limit.

```Unable to import module 'handler_ ': No module named psycopg2```

A new error - Progress!

The error informed me that I hadn't included  my libraries weren't able to load. What? I'd tested locally so I knew the libraries were working properly on my OS X Machine when I'd installed them within the virtual environemnt and even when I had the zip package unzipped and tested locally in a new folder. Strange...

Let's see what the internet has to say.

Turns out that a few other folks had experienced the error. The issue stems from psycopg2 being a compiled module. Because I compiled psycopg2 on OS X my compailed version was incompatible with the Amazon Linux machine that lambda functions run on. To solve this you have a few options:

- You can use an EC2 Instance to install dependencies for your serverless function and zip the file up there before uploading it to Lambda. But using a server in order to go serverless felt ikky. It might be the easiest solution if you don't have to update the function frequently, and you really need the compiled dependency. 

- You could use non-compiled libraries. I didn't really need psycopg2 specifically so I opted to substitute these for another library - pg8000. It sounded like something from a sci-fi saga so I figured I had to try it.

- You could also theoretically use your own linux machine or virtual machine and hope that the code you compile is compatible with an actual Amazon Linux Machine. But I thought this would actually be more work than spinning up a tiny EC2 instance so I didn't try it myself.

- Lastly, you can abandon the Lambda function completely and run your regular job on cron directly in a small EC2 instance. But that's not really a 'serverless' solution. 

Do you have a better solution? Let me know on [Twitter]({{% my_twitter %}}) and I'd love to hear it.
