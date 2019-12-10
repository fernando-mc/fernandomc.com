+++
Description = "I've recently published a new Python package called awsmailman to help you update Amazon Route53 domains!"
Tags = [
  "AWS",
  "Amazon Web Services",
  "Python",
  "Route 53",
  "Amazon Route 53",
]
Categories = [
  "AWS",
]
title = "Updating Route 53 Domains with awsmailman"
publishdate = "2019-12-19T22:49:14-08:00"
date = "2019-12-19T22:49:14-08:00"
[image]
    feature = "/images/awsmailman/awsmailman.png"
    credit = "Bogdan Suditu"
    creditlink = "https://www.flickr.com/photos/bogdansuditu/2607404802"
+++

One of my few possessions is an abundance of poorly-chosen Amazon Route 53 domain names with outdated contact addresses from moving between overpriced apartments - I am what you might call a tech typical millennial. 

So I wrote a Python package called `awsmailman` to help do this for me and now you can use it too.
<!--more-->

## Requirements for `awsmailman`

- You'll need [Python](https://www.python.org/downloads/) and pip.
- You'll need the [AWS CLI](https://aws.amazon.com/cli/)
- You'll need an AWS CLI Profile setup with appropriate permissions to manage Route53 Domains

## Install `awsmailman`

Just run `pip install awsmailman`.

## Using `awsmailman`

Just run `awsmailman` and follow the prompts!

You'll select domains from a list:

![A screenshot of listed domains from the CLI tool](/images/awsmailman/domain_list.png)

And fill out the new contact details:
![A screenshot of the prompts of the CLI tool](/images/awsmailman/prompts.png)

And when you're done entering information the tool will have you review it and then update all the domains you selected at once!

A few quirks to note:
  - You will need to match the first and last name of the contact exactly
  - If you have multiple AWS accounts you'll need to switch AWS profiles and then run the command again (try [my script](https://www.fernandomc.com/posts/script-change-aws-profiles/) to switch AWS profiles!)

Hopefully this helps you to keep your domain contact addresses recent!
