+++
date = "2017-05-17T16:22:18-07:00"
title = "Serverless Domain Forwarding with AWS"
publishdate = ""
draft = false
Description = ""
Tags = [
  "AWS",
  "Serverless",
  "DNS",
  "Typo Domains",
  "Hosting",
  "Domain Forwarding"
]
Categories = [
  "AWS",
  "Serverless"
]
menu = "main"

+++

I recently splurged on a vanity domain (serverless.ly) that I realized I didn't have time or interest in developing into a full-blown blog or microsite. Because of this I decided it would just be best to redirect it to this blog.

The problem is that I'm too cheap and lazy to pay for and manage a small server running Apache or Nginx. This can be a perfectly good option, but the last thing I want is to waste time on server management/config just to forward a vanity domain.

Instead of paying for a dedicated server for this task I opted to use AWS S3, and Route 53 to forward my domain for me. These cost me a fraction of the price of the smallest rentable EC2 instance. It also means that after I get it setup I never have to deal with the pains of configuration or server management.

Here's how you can setup your own vanity or typo domain forwarding without paying for a webserver.
<!--more-->

1. Register your domain 
2. Create S3 buckets to forward from
3. Create an AWS Hosted Zone
4. [Set Nameservers to the ones listed in AWS Hosted Zone]
5. Debug everything in-between 

**Registering Your Domain**

The first step is to register your domain. While I suggest just bundling everything together and using AWS as your domain registrar too, sometimes you'll need to use an alternate registrar. This usually happens if you want a top level domain that AWS doesn't offer or if you don't want to transfer a pre-existing registration to AWS. We'll cover both options below.

If you're registering your domain through AWS the next few steps are somewhat simplified for you.

- ADD AWS PROCESS

If you're using a alternate domain registrar you can either [transfer registration to AWS]() or if that isn't an option you can configure DNS settings on your registrar to point to AWS DNS (we'll cover that in another step).

- ADD TRANSFER PROCESS LINK

**Create S3 Buckets**

Check if this is true - https://aws.amazon.com/premiumsupport/knowledge-center/redirect-domain-route-53/
Why can't you use another S3 bucket name?

**Create an AWS Hosted Zone**

We'll also need to create an AWS Hosted Zone. Hosted 
-- Ramble on Hosted Zones

When you have yur Hosted Zone setup you should see three entries. 

- A record for www.domain.com
- A record for domain.com
- SOA? Something Add it here.

Click on either of the A records and see what DNS your hosted zone has in the right side panel. If you registered your domain outside of AWS you'll need to copy these down for the next step and set them as the DNS for your domain.

If you registered your domain via AWS you can actually

**Configuring Name Servers**

With those hurdles out of the way, the key thing you need to do when registering a domain is to set your nameservers to the AWS nameservers you ceated in the previous step.

**Debugging DNS**

When I registered serverless.ly I used http://register.ly/. This in iteself was not ideal because:

1. I had to use something outside of AWS infrastructure
2. The registrar itself had internal issues and threw 500 errors when I tried to change my DNS
3. I later found out they had two separate places to set nameservers. One was in the DNS entries NS/A/CNAME Etc. and the other was a specialized page for setting nameservers.

- CHECK NS entries VS. NAMESERVERS (Same thing?)

dig +trace domain.com
Shows what nameservers you have setup



3. Upload and add tags and categories
4. Check publish date and date
5. Add all the other front matter
6. Check summary
