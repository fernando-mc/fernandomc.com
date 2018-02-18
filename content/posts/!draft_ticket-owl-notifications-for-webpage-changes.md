+++
draft=true
Description = "Learn how to build your Ticket Owl to get notified when a webpage you're monitoring"
Tags = [
  "Projects",
  "Serverless",
  "Python",
  "AWS",
  "Ticket Owl"
]
Categories = [
  "AWS",
  "Serverless",
  "Projects"
]
title = "Ticket Owl - Website Monitoring"
menu = "main"
date = "2018-02-07T14:08:12-05:00"
publishdate = "2018-02-07T14:08:12-05:00"
[image]
    feature = "/images/ticket_owl/ticket-owl.png"
    credit = "Image Credit | Chris Parker"
    creditlink = "https://www.flickr.com/photos/chrisparker2012/14963399105/"

+++

With Valentine's Day coming up I needed to plan a nice weekend dinner. So I decided I wanted to buy some tickets for a dinner and show. Just one problem, the site I was looking at ran out of tickets for currently scheduled shows. When I called the resturaunt they told me to check the website - they were going to add more tickets at a later time.

But I didn't want to constantly check the page to see if new times and dates were added, so I wrote a new project called Ticket Owl to do it for me.

<!--more-->

Ticket Owl is a project I put together to make periodically checking in on a website a lot easier. I've written it so that it is flexible enough to do a few different things:

1. To check a website with varying frequency 
2. To allow you to check for text on a page
3. To allow you to check for text _not_ on a page
4. To notify you via email and SMS when your condition is met
5. To be deployed to AWS with the [Serverless Framework](https://www.serverless.com)

Now in addition to Valentine's Day-esque uses you could probably use this to regularly monitor the status of different websites. But, realistically, you just want to know the moment that those Taylor Swift tickets are on sale.

Here's how you can use this project.

First,