+++
Description = "Reviewing some of the configuration required to integrate CloudFront with Amazon S3 static sites."
Tags = [
  "S3",
  "Serverless",
  "Static Sites",
  "Amazon S3",
  "AWS ",
  "CloudFront",
  "Amazon CloudFront",
  "CDN",
  "Configuration",
]
Categories = [
  "AWS",
]
title = "Amazon S3 Static Sites and CloudFront Configuration"
publishdate = "2019-07-17T08:55:26-07:00"
date = "2019-07-17T08:55:26-07:00"
[image]
    feature = "/images/amazon-s3-static-sites-and-cloudfront-configuration/clouds.png"
    credit = "Zach Dischner"
    creditlink = "https://www.flickr.com/photos/zachd1_618/7451988874/"
+++

Recently, I was going back over an Amazon S3 static site I maintain and noticed that I couldn't navigate directly to subpages without using a trailing `/index.html`. It turns out there's a few quirks of the Amazon CloudFront console settings that might lead to this. Let's take a look at how to fix it.

<!--more-->

## The Setup

For context, the basic structure of [the site](serverlessfoo.com) I was working with minus a few asset folders looks like this:

```
.
├── index.html
└── projects
    ├── ben-frankly
    │   ├── index.html
    │   └── main.css
    ├── chameleon
    │   ├── index.html
    │   └── main.css
    ├── index.html
    ├── pigeon
    │   ├── index.html
    │   └── main.css
    └── state-sales-tax-api
        └── index.html
```

There is a homepage (http://serverlessfoo.com) that has links to individuals project pages under the /projects directory. 

## The Issue

Essentially, I wanted to host these different HTML pages on the same site as subpages without having to pay for different domains. Now with a typical webserver, you could configure the plain URLs without an `index.html` to assume when you enter `https://serverlessfoo.com/projects/ben-frankly` into your browser you actually want to see the file located at `https://serverlessfoo.com/projects/ben-frankly/index.html`.

However, with the configuration I was using under the CloudFront distribution for this site, the plain no-`index.html` pages were displaying this error when I navigated to them.

This was odd for me, because in the past I'd setup sites with what I thought was the exact same services with the same configuration and the behavior was the one I wanted (with the plain no-`index.html` loading the `index.html` file). Now, instead of this behavior, when I navigated to one of my subfolder projects at `https://serverlessfoo.com/projects/chameleon/` I got the "No such key" error:

![Screenshot of a no such key error](/images/amazon-s3-static-sites-and-cloudfront-configuration/no-such-key.png)

But when I visited `https://serverlessfoo.com/projects/chameleon/index.html` it loaded just fine:

![Screenshot of chameleon site](/images/amazon-s3-static-sites-and-cloudfront-configuration/chameleon-site.png)

# The Fix

After a little bit of digging I realized that there are two different possible CloudFront origins for S3 Static sites:

1. Bucket Origins
2. Static Site Origins

Bucket origins are the default origin that CloudFront autofills for you when you start typing the name of your bucket into the CloudFront Origin configuration:

![Origin autofilling as a bucket origin](/images/amazon-s3-static-sites-and-cloudfront-configuration/origin-autofill.png)


The issue turned out to be that I had used the bucket origin in the CloudFront origin configuration (because it was the only autofill option I saw for this site!):

![Screenshot of the bucket origin](/images/amazon-s3-static-sites-and-cloudfront-configuration/bucket-origin.png)

The bucket origin is the same as the name of your bucket with a `.s3.amazonaws.com` at the end of it. These origins *can* work to host a static site and have it cached by CloudFront. However, they lead directly to the issue discussed above for any subpages.

In order to fix this issue, we need to use a static site origin with CloudFront. Static site origins reference the hosted static site instead of S3 bucket files. This enables navigating to pages without the trailing /index.html because the static site itself has that configuration taken care of.

Static site origins will differ depending on the AWS region they deployed in so be sure to review your static site URL to get the correct value. They should look like a version of your static site URL without the initial `http://`. In my case in us-east-1, I added `.s3-website-us-east-1.amazonaws.com` to the end of my bucket name to create the new value for my origin: `www.serverlessfoo.com.s3-website-us-east-1.amazonaws.com`. 

This will *not* currently autofill inside of CloudFront so it is less intuitive:

![Screenshot of the static site origin](/images/amazon-s3-static-sites-and-cloudfront-configuration/static-site-origin.png)

With this configuration applied, you should be able to either wait for CloudFront to update or run an invalidation on the cache and then try navigating to the plain no-`index.html` url. After that, all your subpages should work as expected.

Still having problems? Leave a comment below!
