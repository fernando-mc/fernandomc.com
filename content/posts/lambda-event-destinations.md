+++
Description = "A look at using Lambda event destinations as an failure alerting method with SNS."
Tags = [
  "Architecture",
  "Serverless",
  "AWS",
  "AWS Lambda",
  "Lambda Event Destinations",
]
Categories = [
  "AWS",
]
title = "Adding SNS Event Destinations as Alerts for your Serverless Applications"
publishdate = "2020-05-11T20:13:47-08:00"
date = "2020-05-11T20:13:47-08:00"
[image]
    feature = "/images/20-projects-20-days/destinations.png"
    credit = "Christina Ablinus"
    creditlink = "https://www.flickr.com/photos/7630310@N04/30106781332"
+++

Welcome to project 7 of my [twenty projects in twenty days series](https://fernandomc.com/posts/twenty-projects-in-twenty-days/) series! 

This project will look at an easy way to setup some failure notifications on your Lambda functions. When you're trying to do this there are a lot of options. You can use CloudWatch Metrics and Alarms tied to SNS topics, CloudWatch Log Subscriptions, and a variety of other custom third party tools. But if you want a relatively simple way to get a log of failures in your inbox, you can hook your functions up with Event Destinations for failures.

<!--more-->

In order to create the SNS topic and deploy your Lambda function in this tutorial you'll need:

1. The Serverless Framework installed
2. The [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html) installed and configured with your AWS credentials

You'll want to start by creating the SNS topic. You can do this easily with the AWS CLI:

```bash
aws sns create-topic --name myEventDestinationTopic
```

From there, you should get a response that contains the ARN of the SNS Topic that should look something like this: `arn:aws:sns:us-east-1:666777888889:myEventDestinationTopic` - keep that around for later. 

Next, you'll want to subscribe any emails you need failure notifications sent to. You can also do this with the AWS CLI:

```bash
aws sns subscribe \
--topic-arn arn:aws:sns:us-east-1:666777888889:myEventDestinationTopic \
--protocol email \
--notification-endpoint "your.email@gmail.com"
```

This should send that email a request to confirm the SNS subscription, go check your email inbox and then confirm the subscription.

From there, you can create a Lambda function that has the SNS topic as a destination. I did this with the [Serverless Framework](http://serverless.com). Here's [my code](https://github.com/fernando-mc/lambda-event-destination-alerts).

Let's take a look at it. First, I created a `serverless.yml` file that looked like this:

```yml
service: event-destinations

provider:
  name: aws
  runtime: python3.8

functions:
  importantFunction:
    handler: handler.starting
    destinations:
      onFailure: "arn:aws:sns:us-east-1:666777888889:myEventDestinationTopic"
```

This references the ARN of the myEventDestinationTopic SNS Topic which we just created earlier. You'll need to use your topic ARN. It also references the Python handler in `handler.py` which looks like this:

```py
import json


def starting(event, context):
    if event["Success"] == True:
        response = {
            "statusCode": 200,
            "body": json.dumps(event)
        }
        return response
    else:
        print("Time to fail!")
        raise Exception("Some serious failure")
```

Basically, this just allows us to send a test payload in to trigger the function and either have it succeed if we sent it a true "Success" event or fail otherwise.

From there, you can run `serverless deploy` to deploy our your function. You should see something like this:

```
Service Information
service: event-destinations
stage: dev
region: us-east-1
stack: event-destinations-dev
resources: 7
api keys:
  None
endpoints:
  None
functions:
  importantFunction: event-destinations-dev-importantFunction
layers:
  None
```

In order to test your function, you'll need the name of the function that was created from the deployment. In the service information above that came out of my deployment this is: `event-destinations-dev-importantFunction`.

To test the event destination we can run the following AWS CLI command to make sure the `Event` type invocation will trigger the function and if it fails send an email notification to us:

```bash
aws lambda invoke \
--function-name event-destinations-dev-importantFunction \
--invocation-type Event \
--payload '{ "Success": false }' response.json
```

This should cause a failure for our function and result in an email notification in our inbox.

**A quick caveat**

This might not be the best solution for immediate alerting and notifications. From my initial tests with event destinations, these failure messages can sometimes take a few minutes to appear. As such, they aren't the best for real-time alerting and debugging. 

If you're not already, consider signing up for my [mailing list](/mailing-list) to get the latest updates about the [new projects](https://fernandomc.com/posts/twenty-projects-in-twenty-days/) and courses I'm working on. If you reply to the initial welcome email I'll also shoot you a 30-day free trial to all my Pluralsight.com courses!