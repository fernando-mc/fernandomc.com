+++
Description = ""
Tags = [
  "AWS",
  "Hugo",
]
Categories = [
  "AWS",
  "Severless",
  "Deployment",
]
menu = "main"
date = "2016-12-24T22:48:49-05:00"
title = "Hugo - Deploy Blog to s3"

+++

Howdy! I've moved my site to [Hugo](https://gohugo.io)! I've also deployed it using AWS' static site hosting.

Here's a few easy aliases to deploy your static hugo blog to AWS

```bash
alias myblog="cd ~/Documents/myblogfolder"
alias deployblog="myblog && hugo && aws s3 sync ./public s3://your-aws-website-bucketn && rm -r ./public"
```

This also assumes you've got that bucket configured to use your website.
