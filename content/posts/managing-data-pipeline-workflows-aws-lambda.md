+++
menu = "main"
publishdate = "2017-02-28T23:04:33-05:00"
date = "2017-02-28T23:04:33-05:00"
title = "Managing Data Pipeline Workflows in AWS Lambda"
draft = false
Description = ""
Tags = [
  "Python",
  "AWS",
  "AWS Lambda",
  "Lambda",
  "Data Pipelines",
  "Data Engineering",
  "Data Warehouses",
  "Data Processing",
  "Data",
]
Categories = [
  "AWS",
  "Data Engineering",
  "AWS Lambda",
]

+++

This post was originally appeared on the [Pluralsight Blog](https://www.pluralsight.com/blog/software-development/data-aws-lambda) on February 28, 2017. Be sure to check out my Pluralsight course that can introduce you to [AWS Lambda](https://www.pluralsight.com/courses/aws-developer-introduction-aws-lambda)!

Maintaining data warehouses can be a difficult undertaking for any organization. Not only do you have to establish processes and procedures for regularly loading flowing data, you also have to ensure you’re doing it in a way that’s resistant to failure and future errors. In this post, we’ll take a quick look at some of the biggest challenges of maintaining large scale data warehouses, and how AWS Lambda can help.<!--more-->

*Building and maintaining a data warehouse*
Let’s say you work at an organization that wants to bring organizational data to bear in management decisions. To support the effort of data analysts, your team is tasked with building and maintaining a data warehouse that will serve as the primary source of data used by analysts to provide guidance to management. 

In the past this would have been a massive ordeal. You likely would have had to manage (and pay for) every piece of infrastructure from the hardware to the proprietary database powering your warehouse. Fortunately, there’s been a lot of progress in managed services over the last few years that allows you to outsource some of that complexity to a third party. 

If you’re also responsible for the visualization and analysis layer of your data warehouse, you might also want to add machine learning and visualization components into this architecture. But for now let’s ignore those other elements and, instead, focus on maintaining the flow of data.

To do this, you’ll want to have the following things in place:

You can read the full article over at the [Pluralsight Blog](https://www.pluralsight.com/blog/software-development/data-aws-lambda), where you can also check out my [introductory course](https://www.pluralsight.com/courses/aws-developer-introduction-aws-lambda) on AWS Lambda.