+++
Description = "An argument for abandoning NGINX and Apache for simple static websites"
Tags = [
  "Serverless",
  "Static Sites",
  "S3",
  "AWS",
  "Savings",
  "NGINX",
  "Apache",
  "Hosting"
]
Categories = [
  "Hosting",
  "AWS",
]
draft = true
date = "2017-07-30T08:38:27-07:00"
publishdate = "2017-07-30T08:38:27-07:00"
title = "Stop using NGINX and Apache for Simple Websites"
menu = "main"

+++

Running your own webserves with NGINX or Apache is a more expensive and riskier option for simple websites and I'll proove it.

<!--more-->

When I moved my personal website to a static site hosted on AWS I was running an experiment. How cheap could I be and still get a high-performance personal site that I was happy with?

I thought about using a virtual machine on AWS, Azure, or somewhere else, but the cheapest I could find was around $5 a month for performance that I was worried wouldn't scale if, for example, I posted something to Hacker News. 

Because my website is a Static Site build with [Hugo](http://gohugo.io/) I really didn't need anything too fancy. The main things that I wanted were performance and cost-savings.

Some content for a post


The rest of the content