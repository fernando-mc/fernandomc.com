+++
Description = "A look at using schema validation with AWS API Gateway using the Serverless Framework."
Tags = [
  "Serverless",
  "Python",
  "AWS API Gateway",
  "API Gateway",
  "Schema Validation",
  "JSON Schema",
]
Categories = [
  "AWS",
  "Projects",
]
title = "Using JSON Schema Validation with the AWS API Gateway"
publishdate = "2020-05-26T21:33:56-07:00"
date = "2020-05-26T21:33:56-07:00"
[image]
    feature = "/images/abstract-10-short.png"
+++

Imagine you're writing a serverless API using AWS Lambda and API Gateway. If you want to accept JSON payloads from POST requests that contain data created by your frontend client you may end up needing to validate that data to make sure it conforms to your expectations before you process it. Let's look at a simple way of doing this with API Gateway's JSON Schema validation.
<!--more-->

## The Problem

Imagine you have an HTTP endpoint that processes incoming data to create a survey. Let's say these surveys all need to have a required `questions` array containing all the questions to be shown to a user. This array must have:

1. A `question_number` so we know what order to show the question in.
2. A `question_type` of either "text" or a "rating" so we know to collect either text responses or numeric ratings for the question
3. And a `question` which contains the actual text to display to a user taking this survey.

To put this differently, we'd expect that the JSON data coming in looks like this:

```json
{
    "questions": [
      {
        "question_number": "1",
        "question": "How great is this blog (5=awesome and 1=boring)",
        "question_type": "rating"
      },
      {
        "question_number": "2",
        "question": "What suggestions do you have for this blog?",
        "question_type": "text"
      },
    ]
}
```

But what if someone completely defies our standards and submits data that looks like this:

```json
{
  "question": "How great is this blog (5=awesome and 1=boring)",
  "type_question": "rating"
}
```

Well, this could really mess up our backend API if we're not prepared for it. We might fail outright, or worse, we might add this data to a backend database and only see errors when we load it back to the frontend. By then it will be too late to catch the issue.

So how could we avoid this? We could write an API that checks all the individual fields before processing the data. Maybe some Python code like the below pseudocode to try and validate the fields:

```py
def process_data(event, context):
    incoming_event_data = json.loads(event["body"]) # The JSON object from above
    expected_fields = ["question_number", "question", "question_type"]
    # Check that the questions are there
    if not incoming_event_data.get("questions"):
        return "Some error"
    # Check if the expected fields are there
    for field in expected_fields:
        if not incoming_event_data["questions"].get(field):
          return "Some error"
    # Do some stuff now that we validated things
    somedatabase.save(incoming_event_data)
```

This starts to get at what we want. But what happens when we change the way we want data structured? Also, what about all the edge cases we missed? For example, should items be unique? Are there a minimum and maximum number of items? Are there max lengths to fields we might see in the data? Didn't we want the "question_type" to be limited to a few specific properties?

A million questions like this start to appear as soon as we take a second look at this code. And this is before we want to try to get more sophisticated with our testing or make any changes as part of the development process.

How do we solve this? Well, there's lots of tools in different languages for enforcing a structure of data coming in through an API like this. Many of them are language specific and have their own benefits and pitfalls. In this case, one easy option when using AWS API Gateway is JSON schema validation. 

## Solving the Problem with JSON Schema Validation

In our example above, we can actually describe the specifications of the data structure we want using something called JSON Schema Validation. [JSON Schema](https://json-schema.org/) is a "vocabulary that allows you to annotate and validate JSON documents". As of the writing of this post, AWS API Gateway supports Draft 4 of JSON Schema. While the latest versions of JSON Schema have many new notable benefits and features there's still a lot you can do just with Draft 4. Here's an example of how I might describe the schema for this sort of incoming survey question data:

```json
{
    "$schema": "http://json-schema.org/draft-04/schema#",
  
    "definitions": {
      "question": {
        "type": "object",
        "properties": {
          "question_number": {
            "type": "string",
            "pattern": "^[0-9]+$"
          },
          "question_type": {
            "type": "string",
            "enum": ["text", "rating"]
          },
          "question": {
            "type": "string",
            "minLength": 10,
            "maxLength": 500
          }
        },
        "required": ["question_number", "question_type", "question"]
      }
    },
  
    "type": "object",
  
    "title": "The Root Schema",
    "required": [
      "questions"
    ],
    "properties": {
      "questions": {
        "type": "array",
        "minItems": 1,
        "maxItems": 15,
        "uniqueItems": true,
        "title": "The question array holding questions",
        "items": { "$ref": "#/definitions/question" }
      }
    }
  }
```

The Schema above describes what the incoming data should look like. AWS API Gateway can then take this document and plug it into an API endpoint in order to reject any requests that don't conform to the schema. Inside this schema you'll see a `definitions` section which is an easy way for us to create part of a JSON object that we want to later reference. In this case, we create what a `question` should look like. The `question` definition in this case describes this part of our earlier correctly-formed data:

```json
{
  "question_number": "2",
  "question": "What suggestions do you have for this blog?",
  "question_type": "text"
}
```

In the definition we provide all of the different properties like `question_number`, `question` and `question_type` and mark them as required. We also make sure they each have data types and other restrictions they must adhere to. For example, `question_number` must be a number. The `question` must be a string of a set length, and the `question_type` must match one of two enumerated options.

The overall data we're submitting though also has it's own restrictions. The `"type": "object"` you see in the code above is applied to the entire JSON payload coming in. We also give it a single required property of `questions` which is an array of one to fifteen items that must be unique. Also, with this line:

```json
"items": { "$ref": "#/definitions/question" }
```

We specify that the items of the array must all match the earlier `question` definition. There's a lot more we could do with JSON Schema if we want to set additional limitations or continue to iterate on our structure. This method is also a much less haphazard way of describing the data we want than my Python pseudocode earlier.

## Implementing JSON Schema Validation

So how do we actually get this to work in an AWS API Gateway API? I'll show you how to do this with the Serverless Framework.

**Prerequisites**

Here's what you'll need if you'd like to deploy an API and test out schema validation:

1. [This Code](https://github.com/fernando-mc/schema-validation-demo)
2. [The AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)
3. [The Serverless Framework](https://serverless.com/)
4. [Python 3](https://www.python.org/downloads/)

Once you've got all that setup let's take a look at the code. Here's what's in the repository:

```
├── handler.py
├── schema.json
└── serverless.yml
```

**`handler.py`**

The `handler.py` file would be where all our API logic would live, but in this case all it does is load the JSON body and return it back to the API:

```py
import json


def hello(event, context):
    print(event["body"])
    data = json.loads(event["body"])
    response = {
        "statusCode": 200,
        "headers": {"Access-Control-Allow-Origin":"*"},
        "body": json.dumps(data)
    }
    return response
```

**`schema.json`**

The `schema.json` file is where the JSON schema I just showed you earlier lives. If we had multiple schemas we'd want to name it something more specific like `questionSchema.json` or restructure our code so that the schema lives with the endpoint's code perhaps in a separate folder to keep things more streamlined.

**`serverless.yml`**

Finally, we have the `serverless.yml` file. This is where everything is tied together. We use it and the Serverless Framework to automatically create the AWS API Gateway endpoints and tie them to Lambda Functions running the code from `handler.py`. The first section specifies our service name and AWS as the cloud we're using along with the runtime of `python3.8`:

```yml
service: schema-validation-demo

provider:
  name: aws
  runtime: python3.8

# ...
```

From there, we create a `functions` section to define the functions we're using. This also allows us to create an API Gateway endpoint to trigger our functions using the `http` event.

```yml
functions:
  hello:
    handler: handler.hello
    events:
      - http:
          path: hello
          method: post
          cors: true
          request:
            schema:
              application/json: ${file(schema.json)}
```

Included in this `http` event configuration we have the `request` section which is where we configure the JSON Schema. The last three lines of the file configure our `schema.json` file as the validation for JSON request bodies coming into that API Gateway endpoint.

## Deploying and testing the validation

In order to see how this works, just run `serverless deploy`. You should see something like this:

```
Service Information
service: schema-validation-demo
stage: dev
region: us-east-1
stack: schema-validation-demo-dev
resources: 14
api keys:
  None
endpoints:
  POST - https://g0gd4wa3l0.execute-api.us-east-1.amazonaws.com/dev/hello
functions:
  hello: schema-validation-demo-dev-hello
layers:
  None
```

With this POST endpoint we can now test the validation out! Keep in mind, your POST endpoint will be different than mine so be sure to replace it in the next commands.

First, try POSTing an invalid body with `curl`:

```bash
curl --request POST 'https://g0gd4wa3l0.execute-api.us-east-1.amazonaws.com/dev/hello' \
--header 'Content-Type: application/json' \
--data-raw '{
	"stuff": "more"
}'
```

You should get back: `{"message": "Invalid request body"}`.

But, when you POST a correctly-formatted request body like this:

```bash
curl --request POST 'https://g0gd4wa3l0.execute-api.us-east-1.amazonaws.com/dev/hello' \
--header 'Content-Type: application/json' \
--data-raw '{
    "questions": [
      {
        "question": "How great is this blog?",
        "question_type": "rating",
        "question_number": "1"
      }
    ]
}'
```

You should get back a successful response that returns the structure of the data you sent in:

`{"questions": [{"question": "How great is this blog?", "question_type": "rating", "question_number": "1"}]}`

If you get to this point, you've successfully implemented schema validation! This should serve as a great way of minimizing the validation code you have to write in your AWS Lambda handlers to parse incoming JSON event data. If you can assume that the data is structured correctly you can process it with simpler and cleaner code.

## Now What?

With schema validation under your belt, you might be interested in deploying some more interesting APIs that leverage serverless database tools like DynamoDB. If you want to learn more about DynamoDB you can [sign up for a free trial](https://pluralsight.pxf.io/RW5Bb) to Pluralsight where you can take my course on DynamoDB or my other courses on serverless topics.

I also have a few other posts on creating serverless APIs with AWS that you might be interested in: 

- [Developing Serverless Express.js APIs on AWS with DynamoDB](/posts/developing-expressjs-serverless-framework-apis/)
- [Developing Serverless Flask APIs on AWS with DynamoDB](/posts/developing-flask-based-serverless-framework-apis/)

If you haven't already, consider checking out my [Twenty Projects in Twenty Days series](https://fernandomc.com/posts/twenty-projects-in-twenty-days/) for more projects!
