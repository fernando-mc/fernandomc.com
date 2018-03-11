+++
draft = true
Description = ""
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
title = "Nandolytics - My Own Visits Analysis Tool"
menu = "main"
publishdate = "2017-02-28T23:04:33-05:00"
date = "2017-02-28T23:04:33-05:00"

+++

I was using my website analytics recently to review my site traffic and I was curious how difficult it would be to build my own web analytics that could count hits on my site. So I did - sort of: I present to you _Nandolytics_.

Now before I continue, let me without question let you know that this entire thing is super unreliable/easily exploitable and suggest that you never use it in a real production environment. With that said... Let's have some fun!
<!--more-->

While working on a course that touches on AWS DynamoDB I wrote an external blog post on using DynamoDB atomic counters. Atomic counters essentially allow you to increment numeric values in a single write operation rather than doing a read and a subsequent write. This avoids issues like accidentally overwriting previous data and generally adds some fault-tolerance. You can technically increment _or_ decrement the values you're evaluating so I figured what the heck - I'll try to make a website 'hits' counter that keeps track of pageloads.

There are two endpoints for Nandolytics:

1. The `nandolytics/record` endpoint - this is used to tabulate visit counts internally.
2. The `nandolytics/{id}` endpoint - this is used to check the internal visit counts to see if you're a winner when you view my post.

If you're a winner, you'll get a special surprise! In fact, here's an implementation of the final product on this page itself:

**CODE HERE TO DISPLAY CURRENT HITCOUNT ON THIS POST AND A 'CLICKHERE' button to check if they are a winner**

0. GET SOME IMAGES in (Feedly, RSS, etc use them and need em in there at first publish)
3. Upload and add tags and categories
4. Check publish date and date
5. Add all the other front matter
6. Check summary


The first thing I did was, of course, create a new [Serverless Framework](https://www.fernandomc.com) project. The code and configuration can be found [on Github](https://github.com/fernando-mc/nandolytics).

For this project I created a DynamoDB table that has a simple primary key consiting of a `siteUrl` atribute as a partition key. In addition to the `siteUrl` all these items have a number type attribute - `siteHits`. Now `siteHits` needs to be a number type because that's required for using an atomic counter with DynamoDB. So, a sample item in the table might initialize to this for my blog's homepage.


```json
{
  "siteUrl": "https://www.fernandomc.com/",
  "visits": "1"
}

```

All I had to do to make this work was use the deployment url

https://hz9ap21u7a.execute-api.us-east-1.amazonaws.com/dev/nandolytics/https%3A%2F%2Fwww.fernandomc.com

Now whenever the page loads it will add a site hit. It will also display the update hits count so you can see it. 
