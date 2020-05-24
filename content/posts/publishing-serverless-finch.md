+++
Description = ""
Tags = [
  "Serverless Framework",
  "Serverless",
  "Serverless Plugins",
  "npm",
  "Serverless Finch"
]
Categories = [
  "serverless",
]
menu = "main"
publishdate = "2017-10-24T10:55:49-07:00"
date = "2017-10-24T10:55:49-07:00"
title = "Publishing Serverless Finch"
[image]
    feature = "/images/publishing_serverless_finch/serverless-finch-img.jpg"
    credit = "Image Credit | patrickkavanagh"
    creditlink = "https://www.flickr.com/photos/patrick_k59/33835513152/"
+++

A little while ago I was working on a [Serverless Framework](https://serverless.com) project that needed an easy way to deploy and configure files in S3 as a static website. I didn't want to do this manually and was hoping to keep the scope of everything within the Serverless Framework. After some back and forth I ended up republishing an outdated npm package as `serverless-finch` and you're now welcome to use and contribute to it.

<!--more-->

Serverless finch wasn't my plugin initially. I found a serverless [plugin](https://github.com/serverless/serverless-client-s3) for this purpose that Serverless Inc. hadn't updated in awhile. I tried to use it but ran into a few errors. After reading over a few resolved issues it looked like the problems were fixed on the Github repo but never pushed to npm. So I checked in with the Serverless team:

![Discussion with serverless maintainers](/images/serverless_finch/serverless-finch-was-born.png)

And eventually I decided to publish a new package myself. Turns out they wanted to update but apparently lost the npm credentials to do so. So, through absolutely no credit of my own you can now use Serverless Finch.

`npm install serverless-finch`

Feel free to take a closer look [on npm](https://www.npmjs.com/package/serverless-finch)

After I published it, the serverless.com folks decided to retire their version: 

![Old package repo with a message saying to use serverless-finch](/images/serverless_finch/deprecataion.png)

Looks like I'll have to keep this one updated!
