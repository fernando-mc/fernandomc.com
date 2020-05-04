+++
Description = "A look back on techpayrates.com and how it's put together."
Tags = [
  "Twenty Projects Twenty Days",
  "Serverless",
  "Python",
  "AWS",
  "Pay",
  "Pay Transparency",
  "Salary",
  "Tech",
  "Salary Transparency",
]
Categories = [
  "Projects",
]
title = "Tech Pay Rates revisited."
publishdate = "2020-05-04T13:20:38-07:00"
date = "2020-05-04T13:20:38-07:00"
[image]
    feature = "/images/20-projects-20-days/techpayrates.png"
    creditlink = "https://www.flickr.com/photos/bantam10/16958703915/"
    credit = "Ted Van Pelt"
+++

For the first installment of my [Twenty Projects in Twenty Days](https://fernandomc.com/posts/twenty-projects-in-twenty-days/) series I wanted to take a look back at Tech Pay Rates. I started this project around the same time I started looking for fulltime work about a year ago and I think it's a classic example of an Minimum Viable Project (not product, as I'm not selling anything here!).

The goal of the project was to help promote the trend of disclosing industry information surrounding salary in order to promote pay transparency and pay equity. To do this, I built a project to let workers post their salary information and search through other salaries. If you'd like a look, go ahead and check out [Tech Pay Rates](https://techpayrates.com/). Full disclosure, it's been a while since I've advertised for it so the salaries are out of date - feel free to add your own!

So let's look at how it was put together.

<!--more-->

## How does Tech Pay Rates work?

Tech Pay Rates was put together in under 500 lines of developer-written code. That means without counting dependencies, imported libraries and other things I didn't write, it took under 500 lines of code to create the website.

It's currently hosted in AWS and leverages a lot of AWS services to help serve up the website and all the functionality it offers. It also uses a third party tool called Algolia to make the search functionality available. If you'd like to see what the code looks like for yourself, you can take a look [here](https://github.com/fernando-mc/techpayrates).

Here is a visual of how it's all put together:

![A visual demonstrating the use of several AWS Services and Algolia being used to make the website. The contents of the diagram are described below.](/images/20-projects-20-days/techpayrates-diagram.png)

Let's break this up into two different sections, the frontend, and the backend functionality.

**The Frontend**

The frontend of the site is hosted in Amazon S3 as a static website. This contains all the HTML, CSS, and client side JavaScript used to interact with the backend APIs. This S3 static hosting then sits behind Amazon CloudFront which is configured to act as a cache for the frontend.

With this configuration setup, you can connect Amazon Route 53 as the DNS provider for the CloudFront domain to get a custom domain. Further, when using CloudFront and Route 53 you can generate an SSL certificate for free using the Amazon Certificate Manager (ACM) in order to secure traffic to the site.

**The Backend**

In order to power the functionality of the website, there are a few different interactions with backend services. 

The first backend service, is the API endpoint used to create new postings. This endpoint is created with the Serverless Framework and in the code for the endpoint [here](https://github.com/fernando-mc/techpayrates/blob/master/post.py). You can see how it takes incoming requests, validates their structure, and confirms the Google Recpatcha has been filled out correctly. From there, it records the new information in both an Amazon DynamoDB table (for long-term storage and to enable other processes) and an Algolia index (to power the search functionality).

The writes to the DynamoDB table also allow for something called DynamoDB streams to be used. Essentially, whenever a new item is written, deleted, or changes in DynamoDB you can then trigger other code to run in response to that to do something like send a new Tweet out informing folks of a new salary. We eventually adopted this design in what become the second version of the project - [Upfront Jobs](https://upfrontjobs.io/).

Because the job postings are also sent to Algolia, the frontend can leverage Algolia frontend libraries to load in the latest job data and easily make different facets of that information available for searching through. 

Have more questions about this project or want to request your own? Sign up for my [mailing list](https://fernandomc.com/mailing-list) and reply to the welcome email! You can also leave your comments below!
