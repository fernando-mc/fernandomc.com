+++
Description = "In this tutorial, you'll learn how to create a Surveys service in Python with the AWS HTTP API, AWS Lambda, and Amazon DynamoDB."
Tags = [
  "Architecture",
  "Serverless",
  "DynamoDB",
  "AWS HTTP API",
  "API Gateway",
  "Lambda",
  "AWS Lambda",
  "Python",
  "AWS",
]
Categories = [
  "AWS",
  "Projects",
]
title = "Creating a Surveys API with the AWS HTTP API and Python"
publishdate = "2020-05-22T09:33:53-07:00"
date = "2020-05-22T09:33:53-07:00"
type = "blog"
[image]
    feature = "/images/abstract-5-short.png"
    postheader = "/images/abstract-5.jpg"
+++

This is project 15 in my [Twenty Projects in Twenty Days](/posts/twenty-projects-in-twenty-days/) series! This time, we're looking at how to create an AWS HTTP API with Python. This will be the Python version of [yesterday's project](/posts/aws-http-api-surveys-service-nodejs/) where we created a Surveys service API to track three entities - customers, customer surveys, and survey responses. Let's get started!
<!--more-->

## Prerequisites

Make sure that you get all the requirements setup first:

1. Your AWS Account
2. The [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html) installed and configured
3. [The Serverless Framework](http://serverless.com/)
4. [The code](https://github.com/fernando-mc/http-api-surveys-service)

## Our Code 

After getting all these dependencies, you can look at the project code:

```
.
├── backend
│   ├── customers.py
│   ├── responses.py
│   └── surveys.py
└── serverless.yml
```

For everything in the `serverless.yml` except for the `runtime: python3.8` it is the same as yesterday's project! This is because I've kept the same structure between both projects and just kept the JavaScript file names, moved the code to Python, and changed the extension. For a full explanation of the `serverless.yml` file just take a look at my description of it in [yesterday's post](/posts/aws-http-api-surveys-service-nodejs/).

The main thing you need to recognize is that you might need to change or remove the first two lines with the `org` and `app` value on them depending on if you're using Serverless Framework Pro or not.

So let's look at how we handle our entities inside our Python files.

### `customers.py`

First, we import some dependencies, including `boto3` which will be used to interact with AWS services like DynamoDB.

```py
import boto3
import json
import os
```

Then, we create the DynamoDB client, and import the name of the table from the `DYNAMODB_TABLE` environment variable we defined in `serverless.yml`. We use both of these to create a table resource in order to easily interact with the DynamoDB table.

```py
dynamodb = boto3.resource('dynamodb')
TABLE_NAME = os.environ['DYNAMODB_TABLE']
table = dynamodb.Table(TABLE_NAME)
```

Next, we write our first function to create customers. It starts by loading in a JSON payload coming from the API and then extracting the `customer_id` and `profile_data` from that JSON object.

```py

def create(event, context):
    body = json.loads(event['body'])
    customer_id = body['customer_id']
    profile_data = body['profile_data']
    # ...
```

Then, we create the item we'll be storing in DynamoDB and use the `put_item()` operation to send it in.

```py
    # ...
    item = {
        'pk': 'CUSTOMER#' + customer_id,
        'sk': 'PROFILE#' + customer_id,
        'profile_data': profile_data
    }
    table.put_item(Item=item)
```

Finally, we return JSON of the item we put into DynamoDB.

```py
    return {
        'statusCode': 200,
        'body': json.dumps(item)
    }
```

This sort of structure is basically what we're doing with all the other entities when we create them. The only other difference is sometimes we create a unique UUID for them when we create them (like surveys or responses).

The other way we use to process requests can be seen in the `get()` function which we use to get a customer's information out of our table. We start by getting a path parameter from the URL by looking at the `pathParameters` part of the incoming event:

```py
def get(event, body):
    print(event)
    customer_id = event['pathParameters']['id']
    # ...
```

Then, we fetch the item with the `get_item()` operation using the `customer_id` we pulled from the URL path parameters:
```py
    # ...
    item = table.get_item(
        Key={
            'pk': 'CUSTOMER#' + customer_id,
            'sk': 'PROFILE#' + customer_id
        }
    )['Item']
    # ...
```

Finally, we return the item as a JSON object back through the API:

```py
    # ... 
    return {
        'statusCode': 200,
        'body': json.dumps(item)
    }
```

And that's it! That's how the majority of these requests work.

## Deploying and Testing the Application

We deploy and test this the same way as [yesterday's project](/posts/aws-http-api-surveys-service-nodejs/). First, we run `serverless deploy` to get the project deployed.

If you see an error like this: `An error occurred: surveysTable`

You may have already deployed yesterday's project and need to either remove it first or to change the name of the DynamoDB table in this project in the `serverless.yml` file first.

Then, we should see something like this:

```
Service Information
service: surveys
stage: dev
region: us-east-1
stack: surveys-dev
resources: 54
api keys:
  None
endpoints:
  POST - https://hxa0poe5g0.execute-api.us-east-1.amazonaws.com/customer
  GET - https://hxa0poe5g0.execute-api.us-east-1.amazonaws.com/customer/{id}
  POST - https://hxa0poe5g0.execute-api.us-east-1.amazonaws.com/survey
  GET - https://hxa0poe5g0.execute-api.us-east-1.amazonaws.com/survey/{id}
  GET - https://hxa0poe5g0.execute-api.us-east-1.amazonaws.com/customer/{id}/surveys
  POST - https://hxa0poe5g0.execute-api.us-east-1.amazonaws.com/response
  GET - https://hxa0poe5g0.execute-api.us-east-1.amazonaws.com/response/{id}
  GET - https://hxa0poe5g0.execute-api.us-east-1.amazonaws.com/survey/{id}/responses
functions:
  createCustomer: surveys-dev-createCustomer
  getCustomer: surveys-dev-getCustomer
  createSurvey: surveys-dev-createSurvey
  getSurvey: surveys-dev-getSurvey
  getAllCustomerSurveys: surveys-dev-getAllCustomerSurveys
  createResponse: surveys-dev-createResponse
  getResponse: surveys-dev-getResponse
  getAllResponses: surveys-dev-getAllResponses
layers:
  None
```

In order to test the API we can use `curl` or something like Postman. Make sure to change the API URL from my URL to whatever comes out when you deploy your service. Here's the command I used:

```bash
curl --request POST 'https://hxa0poe5g0.execute-api.us-east-1.amazonaws.com/customer' \
--header 'Content-Type: application/json' \
--data-raw '{
	"customer_id": "1",
	"profile_data": {
		"cool": "things"
	}
}'
```

When you do this, you should see a response that looks like this:

```json
{"pk": "CUSTOMER#1", "sk": "PROFILE#1", "profile_data": {"cool": "things"}}
```

To make sure it worked, run another `curl` request to get the data out of the API:

`curl --request GET 'https://hxa0poe5g0.execute-api.us-east-1.amazonaws.com/customer/1'`

You should see the same response:

```json
{"pk": "CUSTOMER#1", "sk": "PROFILE#1", "profile_data": {"cool": "things"}}%  
```

Now you can go through and test the other endpoints out if you'd like!

## What Next?

With a newly deployed API that you've confirmed works properly, you might want to learn more about some of these concepts!

To learn more about DynamoDB you can [sign up for a free trial](https://pluralsight.pxf.io/RW5Bb) to Pluralsight where I have a course on connecting DynamoDB to your applications.

You can also learn about other ways to design serverless APIs like this in some of my previous posts: 

- [Developing Serverless Express.js APIs on AWS with DynamoDB](/posts/developing-expressjs-serverless-framework-apis/)
- [Developing Serverless Flask APIs on AWS with DynamoDB](/posts/developing-flask-based-serverless-framework-apis/)

If you want more projects to play with then keep an eye on my [Twenty Projects in Twenty Days series](https://fernandomc.com/posts/twenty-projects-in-twenty-days/) as I keep adding more!
