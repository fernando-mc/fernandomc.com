+++
menu = "main"
publishdate = "2017-03-01T23:04:33-05:00"
date = "2017-03-01T23:04:33-05:00"
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

This post was originally published for the [Pluralsight Blog](https://www.pluralsight.com/blog/software-development/data-aws-lambda). Be sure to check out my Pluralsight course that can introduce you to [AWS Lambda](https://www.pluralsight.com/courses/aws-developer-introduction-aws-lambda)!

Maintaining data warehouses can be a difficult undertaking for any organization. Not only do you have to establish processes and procedures for regularly loading flowing data, you also have to ensure you’re doing it in a way that’s resistant to failure and future errors. In this post, we’ll take a quick look at some of the biggest challenges of maintaining large scale data warehouses, and how AWS Lambda can help.

*Building and maintaining a data warehouse*
Let’s say you work at an organization that wants to bring organizational data to bear in management decisions. To support the effort of data analysts, your team is tasked with building and maintaining a data warehouse that will serve as the primary source of data used by analysts to provide guidance to management. 

In the past this would have been a massive ordeal. You likely would have had to manage (and pay for) every piece of infrastructure from the hardware to the proprietary database powering your warehouse. Fortunately, there’s been a lot of progress in managed services over the last few years that allows you to outsource some of that complexity to a third party. 

If you’re also responsible for the visualization and analysis layer of your data warehouse, you might also want to add machine learning and visualization components into this architecture. But for now let’s ignore those other elements and, instead, focus on maintaining the flow of data.

To do this, you’ll want to have the following things in place:

- A Data Warehouse Solution
- A backing data store that allows you to rebuild or fallback on prior data
- Data pipeline tooling to get your data into your data warehouse

**AWS Redshift**

The first thing to do is pick a top-tier data warehousing solution -- like AWS Redshift -- that can suit the needs of virtually any industry. Redshift allows you to hit the ground running by uploading data from a [variety of formats](http://docs.aws.amazon.com/redshift/latest/dg/tutorial-loading-data.html). It also gives you access to the suite of other services offered by AWS including the AWS [Data Pipeline](https://aws.amazon.com/datapipeline/), which can assist you in managing your infrastructure. 

Let’s also say that we stick with AWS and, at least where we feel it’s warranted, we regularly backup data into the AWS Simple Storage Service (S3). The beauty of this is that we can cheaply store vast amounts of data in S3, and [regularly run UNLOAD operations](http://docs.aws.amazon.com/redshift/latest/dg/t_Unloading_tables.html) on any Redshift table to keep a backup there. Then, if needed, we can [run a COPY operation](http://docs.aws.amazon.com/redshift/latest/dg/tutorial-loading-data.html) to load it back into Redshift. By doing this we have a backing store for our critical data and can quickly load from our backups.

Next, we’ll want some data pipeline tooling to get data into Redshift itself. There are several potential solutions for this. But despite all the available tooling, sometimes you need something without a cost or complexity burden.

So, what can you do if you have a simple data pipeline process that you need running regularly, but want to avoid complexity, cost and additional vendors? Here are a few examples of things you might want to do to data before it ends up in S3 and Redshift:

1. Preprocessing or flattening JSON from a third-party webhook
2. Verifying a standard CSV download format
3. Running quick comparative queries on incoming data to check for discrepancies
4. A variety of other quick checks to clean up, verify or alert on incoming bits of data

**AWS Lambda**

For these types of processes you can use something like AWS Lambda. Lambda is AWS’s event-driven compute service. It runs code in response to events that trigger it. In the above cases you could write your own Lambda functions (the code triggered by an event) to perform anything from data validation to COPY jobs.

Let’s say we’re processing incoming data that we get as a CSV downloaded directly into S3. We’ll skip a bunch of the Lambda setup steps (that you can learn in my [Introductory Lambda Course](https://app.pluralsight.com/library/courses/aws-developer-introduction-aws-lambda/)) and assume we’re writing the body of some function code for these examples. Here’s some psuedo code on how this would work every time one of these CSVs is downloaded into S3:

```python
# Validates Uploaded CSVs to S3 
import boto3
import csv
import pg8000

EXPECTED_HEADERS = ['header_one', 'header_two', 'header_three']

def get_csv_from_s3(bucket_name, key_name):
    """Download CSV from s3 to local temp storage"""
    # Use boto3 to connect to S3 and download the file to Lambda tmp storage
    # This allows Lambda to access and use the file
    
def validate_csv():
    """Validates that CSVs match a certain format"""
    with open(csv_name, 'rb') as csv_to_test:
        reader = csv.reader(csv_to_test)
        headers = reader.next()
        # Return True if headers match what's expected
        return headers == EXPECTED_HEADERS

def load_valid_data():
    """Loads validated data to Redshift from S3"""
    # Add code to run an COPY command from S3
    # http://docs.aws.amazon.com/redshift/latest/dg/tutorial-loading-data.html
    print 'Data loaded to Redshift. Yay!'
    
def trigger_alarm():
    """Send email to someone important using AWS SES to warn them about invalid datae"""
    
def handler(event, context):
    # Use the event object to get the location of the csv in S3
    # Then create bucket_name and key_name
    get_csv_from_s3(bucket_name, key_name)
    # Make sure you're giving validate_csv the right location of the file!
    if validate_csv():
        # if the data is valid:
        load_valid_data()
    else: 
        trigger_alarm()
```

As you can see, this code only does a simple check for the header values of the CSV being the same. But Lambda can do that and more if you create your own validation libraries or use external dependencies to help process your data. 

Now, let’s be clear: This won't replace the many other wonderful tools in your data pipeline arsenal, but it can be an asset for frequent event-driven processing.

What other challenges do you face in managing your data pipeline? Feel free to reach out to me on [Twitter](https://twitter.com/fmc_sea) or take a look over my blog for more fun tips.
