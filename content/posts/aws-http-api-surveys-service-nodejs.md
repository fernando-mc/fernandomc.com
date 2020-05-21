+++
draft = true
Description = ""
Tags = [
  "Architecture",
  "Serverless",
  "Python",
  "AWS",
  "AWS Workspaces",
  "Windows 10",
  "Testing",
]
Categories = [
  "AWS",
]
title = ""
publishdate = "2020-05-21T13:33:53-07:00"
date = "2020-05-21T13:33:53-07:00"
[image]
    feature = "/images/abstract-1.jpg"
+++

As project fourteen of my [Twenty Projects in Twenty Days](/posts/twenty-projects-in-twenty-days/) series I'll show you how to create an AWS HTTP API with Node.js. We'll design it around the same serverless survey service that I've previously shown [using Express.js](/posts/developing-expressjs-serverless-framework-apis/) and [using Flask](/posts/developing-flask-based-serverless-framework-apis/). It'll be used to track three entities - customers, customer surveys, and survey responses. Let's get started!
<!--more-->

## Prerequisites

First, let's make sure you have everything you'll need:

1. An AWS Account
2. The [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html) installed and configured
3. [The Serverless Framework](http://serverless.com/) installed
4. The code cloned from GitHub [here](https://github.com/fernando-mc/http-api-surveys-service-node).
5. [Node.js](https://nodejs.org/en/download/)

## Our Code 

With all that setup, we can take a look at the application code! Here's what the directory looks like:

```
.
├── backend
│   ├── customers.js
│   ├── responses.js
│   └── surveys.js
├── package.json
└── serverless.yml
```

Let's look at some of these in more detail!

### `serverless.yml`

We'll start with the `serverless.yml` file because it's the core of any Serverless Framework service and determines how our API will be structured and the AWS resources we'll use.

First, you'll see three values at the top of the file to setup our service:

```yml
org: fernando # optional, change to your org 
app: http-api # optional
service: surveys-node
```

You'll only use the `org` and `app` values if you're using Serverless Framework Pro, if you're not at the moment you can delete those two lines and everything should work fine.

Next, you'll setup the runtime as Node.js and environment details including a `DYNAMODB_TABLE` variable that will be used in a moment when you create your DynamoDVB table in the Resources section of the file:

```yml
provider:
  name: aws
  runtime: nodejs12.x
  environment:
    DYNAMODB_TABLE: surveys-${opt:stage, self:provider.stage}
  # continued...
```

In the same `provider` section you'll also specify that the AWS HTTP API you're using will have Cross Origin Resource Sharing enabled so you can use it on other domains.

```yml
  httpApi:
    cors: true
```

And then you'll setup the AWS Identity and Access Management permissions you might need to take action on the DynamoDB table used by this service.

```yml
  iamRoleStatements:
    - Effect: Allow
      Action:
        - dynamodb:Scan
        - dynamodb:Query
        - dynamodb:PutItem
        - dynamodb:UpdateItem
        - dynamodb:GetItem
      Resource: 
        - "arn:aws:dynamodb:${opt:region, self:provider.region}:*:table/${self:provider.environment.DYNAMODB_TABLE}"
        - "arn:aws:dynamodb:${opt:region, self:provider.region}:*:table/${self:provider.environment.DYNAMODB_TABLE}/index/sk-pk-index"
```

You'll notice that you have two resources in the `Resource` section. The first is the DynamoDB table itself, the second is an index on that table.

Next, you'll create all the API endpoints that will be present in your HTTP API. Because we have endpoints to create, get, and list multiple kinds of entities there's a decent number of these:

```yml
functions:
  createCustomer:
    handler: backend/customers.create
    events:
      - httpApi:
          path: /customer
          method: post
  getCustomer:
    handler: backend/customers.get
    events:
      - httpApi:
          path: /customer/{id}
          method: get
  createSurvey:
    handler: backend/surveys.create
    events:
      - httpApi:
          path: /survey
          method: post
  getSurvey:
    handler: backend/surveys.get
    events:
      - httpApi:
          path: /survey/{id}
          method: get
  getAllCustomerSurveys:
    handler: backend/surveys.get_all
    events:
      - httpApi:
          path: /customer/{id}/surveys
          method: get
  createResponse:
    handler: backend/responses.create
    events:
      - httpApi:
          path: /response
          method: post
  getResponse:
    handler: backend/responses.get
    events:
      - httpApi:
          path: /response/{id}
          method: get
  getAllResponses:
    handler: backend/responses.get_all
    events:
      - httpApi:
          path: /survey/{id}/responses
          method: get
```

If we want to generalize it though, there are two kinds of endpoints above. 

- GET endpoints that have a path parameter like the `getAllResponses` endpoint: `/survey/{id}/responses`
- POST endpoints like `createResponse` that will be accepting JSON payloads to generate the entities we're working with.

Each these endpoints is powered by a Lambda handler function with code that lives in the `backend` directory. For example, this configuration:

`handler: backend/responses.get_all`

Would point to the `responses.js` file in the `backend` directory. The logic for what to do when this API endpoint is hit would be inside the `get_all` function in that file.

After all these functions are configured, we need a place to store all the data! We're using a DynamoDB table with a partition key of `pk` and sort key of `sk` and a Global Secondary Index that inverts these two keys. I've also included a provisioned capacity on the table and index of 1 Read and Write capacity unit so it's as cheap as possible to run this demo.

```yml
resources:
  Resources:
    surveysTable: 
      Type: AWS::DynamoDB::Table
      Properties:
        TableName: ${self:provider.environment.DYNAMODB_TABLE}
        AttributeDefinitions:
          - AttributeName: pk
            AttributeType: S
          - AttributeName: sk
            AttributeType: S
        KeySchema:
          - AttributeName: pk
            KeyType: HASH
          - AttributeName: sk
            KeyType: RANGE
        ProvisionedThroughput:
          ReadCapacityUnits: 1
          WriteCapacityUnits: 1
        GlobalSecondaryIndexes:
        - IndexName: sk-pk-index
          KeySchema:
          - AttributeName: sk
            KeyType: HASH
          - AttributeName: pk
            KeyType: RANGE
          Projection:
            ProjectionType: ALL
          ProvisionedThroughput: 
            ReadCapacityUnits: 1
            WriteCapacityUnits: 1
```

And that's all the infrastructure and configuration we need to do in about 100 lines. Let's swap over to one of the entities files now. Because these are all somewhat repetitive we'll focus on a single entity file - `customers.js`.

### `customers.js`

In this file, we'll be using many of the same dependencies as all the other files. We'll start by setting up the AWS SDK, creating a DynamoDB document client, and getting the DynamoDB table's name from the `DYNAMODB_TABLE` environment variable:

```js
// Load the AWS SDK for JS
var AWS = require("aws-sdk");
AWS.config.update({region: 'us-east-1'});

// Create the DynamoDB Document Client
var dynamodb = new AWS.DynamoDB.DocumentClient();
var tableName = process.env.DYNAMODB_TABLE
```

From there, the `create` function will process incoming JSON data and look for a `customer_id` and `profile_data` element in order to structure an item to send into DynamoDB.

```js
module.exports.create = async function(event, context) {
    const body = JSON.parse(event['body'])
    const customer_id = body['customer_id']
    const profile_data = body['profile_data']
    const putParams = {
        TableName: tableName,
        Item: {
            'pk': 'CUSTOMER#' + customer_id,
            'sk': 'PROFILE#' + customer_id,
            'profile_data': profile_data
        }
    }
```

Then, it will put the item into DynamoDB and return a success response and the Item that was put in DynamoDB:

```js
    try {
        await dynamodb.put(putParams).promise()
        return  {
            "statusCode": 200,
            "body": JSON.stringify(putParams.Item)
        }
    } catch (error) {
        console.log(error)
        throw new Error(error)
    }
}
```

Next, we have the `get` function which is used to go out and grab the profile data of our customers. It will parse an incoming path parameter as the customer id and then use that to structure a DynamoDB `get` operation to retrieve the customer information and return it back to whatever called the API:

```js
module.exports.get = async function(event, context) {
    const customer_id = event['pathParameters']['id']
    const getParams = {
        TableName: tableName,
        Key: {
            'pk': 'CUSTOMER#' + customer_id,
            'sk': 'PROFILE#' + customer_id,
        }
    }
    let getResult
    try {
        getResult = await dynamodb.get(getParams).promise()
        return {
            "statusCode": 200,
            "body": JSON.stringify(getResult)
        }
    } catch (error) {
        console.log(error)
        throw new Error(error)
    }
}
```

This structure is used pretty consistently throughout the project, the main difference is that for other entities, it will use `uuid` to create a unique identifier for the entity and then store the incoming data under that unique id. 

## Deploy and Test Our Application

To deploy the project, you can run `serverless deploy`. In the output you should see something like this:

```
Service Information
service: surveys-node
stage: dev
region: us-east-1
stack: surveys-node-dev
resources: 65
api keys:
  None
endpoints:
  POST - https://cn5amwd7j3.execute-api.us-east-1.amazonaws.com/customer
  GET - https://cn5amwd7j3.execute-api.us-east-1.amazonaws.com/customer/{id}
  POST - https://cn5amwd7j3.execute-api.us-east-1.amazonaws.com/survey
  GET - https://cn5amwd7j3.execute-api.us-east-1.amazonaws.com/survey/{id}
  GET - https://cn5amwd7j3.execute-api.us-east-1.amazonaws.com/customer/{id}/surveys
  POST - https://cn5amwd7j3.execute-api.us-east-1.amazonaws.com/response
  GET - https://cn5amwd7j3.execute-api.us-east-1.amazonaws.com/response/{id}
  GET - https://cn5amwd7j3.execute-api.us-east-1.amazonaws.com/survey/{id}/responses
functions:
  createCustomer: surveys-node-dev-createCustomer
  getCustomer: surveys-node-dev-getCustomer
  createSurvey: surveys-node-dev-createSurvey
  getSurvey: surveys-node-dev-getSurvey
  getAllCustomerSurveys: surveys-node-dev-getAllCustomerSurveys
  createResponse: surveys-node-dev-createResponse
  getResponse: surveys-node-dev-getResponse
  getAllResponses: surveys-node-dev-getAllResponses
```

To test our API you can run a `curl` command or use something like Postman. Remember that you'll need to replace the API URL below with your own URL:

```bash
curl --request POST 'https://cn5amwd7j3.execute-api.us-east-1.amazonaws.com/customer' \
--header 'Content-Type: application/json' \
--data-raw '{
	"customer_id": "1",
	"profile_data": {
		"cool": "things"
	}
}'
```

After you do this, you should see a result with details on the item you just sent into your DynamoDB table. To test it, you can run another `curl` request to get the data back out:

`curl --request GET 'https://cn5amwd7j3.execute-api.us-east-1.amazonaws.com/customer/1'`

From there, you can test out all the other endpoints!

## What Next?

Now that you've deployed the API and tested out the first you endpoints you might want to learn more about how you can modify this sort of application to work for you.

If you want to learn more about DynamoDB you can [sign up for a free trial](pluralsight.pxf.io/RW5Bb) to Pluralsight where you can take my course on Connecting DynamoDB to your applications.

If you want more exposure to more ways you could design an API like this you can view some of my previous posts on creating serverless APIs with AWS: 

- [Developing Serverless Express.js APIs on AWS with DynamoDB](/posts/developing-expressjs-serverless-framework-apis/)
- [Developing Serverless Flask APIs on AWS with DynamoDB](/posts/developing-flask-based-serverless-framework-apis/)

If you're a Python fan, stay tuned for tomorrow's post where I'll cover how to deploy this same API with Python! Keep an eye on my [Twenty Projects in Twenty Days series](https://fernandomc.com/posts/twenty-projects-in-twenty-days/) for more.