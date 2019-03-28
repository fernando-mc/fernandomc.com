+++
Description = "Details on how and why I made Tech Pay Rates."
Tags = [
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
title = "Creating Tech Pay Rates."
publishdate = "2019-03-27T17:20:38-07:00"
date = "2019-03-27T17:20:38-07:00"
[image]
    feature = "/images/techpayrates/payrates.png"
+++

In the past several months I've seen a lot of buzz surrounding salary, pay transparency, and pay equity. So I decided to build a project that empowers workers to post their salary information and search over other salaries - introducing [Tech Pay Rates](https://techpayrates.com/).

Let's take a look at the tool's purpose and how it's put together.

<!--more-->

## What is Tech Pay Rates?

With this tool you can search through pay rates on different tech jobs or submit your own information. You can search by several different attributes including job title, location, or the company. Additionally, people who submit information can choose to add various compensation details as well as demographic information.

If you'd like to learn more about the tool and the reasons I built it, check out the Upfront Jobs blog post on it [here](https://blog.upfrontjobs.io/blog/announcing-tech-pay-rates).

## How does Tech Pay Rates work?

Tech Pay Rates uses the Serverless Framework to manage AWS infrastructure and to deploy code changes. It also uses Algolia for search functionality. The Tech Pay Rates codebase is currently under 500 lines of code (excluding dependency related files) so it's tiny! Here are the highlights:

1. A static website is hosted in Amazon S3
2. Amazon CloudFront points to that S3 website
3. Route 53 provides the DNS for that CloudFront domain
4. A Serverless Framework service creates an API endpoint that is used in the website to validate and submit new job postings
5. The service sends the data both to DynamoDB (for long-term storage) and to Algolia to be indexed for search purposes.

And whenever changes are made to the S3 site or the backend, the code is redeployed with the Serverless Framework or a plugin for the framework called Serverless Finch. 

## Have suggestions or want to contribute?

I hope this tool will help people in a variety of different situations randing from job seekers to those evaluating their current environments. But if you have feedback or a feature suggestion on how to make it better please open an issue or put in a PR [on GitHub](https://github.com/fernando-mc/techpayrates)!