+++
Description = "Learning how to unit test serverless applications with moto and Pytest."
Tags = [
  "Architecture",
  "Serverless",
  "Python",
  "AWS",
  "AWS Testing",
  "Testing",
]
Categories = [
  "AWS",
]
title = "Serverless Unit Testing with Moto"
publishdate = "2020-05-28T16:26:50-07:00"
date = "2020-05-28T16:26:50-07:00"
[image]
    feature = "/images/abstract-6-short.png"
+++

Serverless development can speed up your development process a lot by moving infrastructure responsibilities over to cloud providers. However, when testing your application, it can be a challenge to mimic all the cloud services you're relying on. 

One useful tool to help do this when you're writing Python code is `moto`. In this post, I'll show you how to use the `moto` module in combination with `pytest` to help test your Python code when it interacts with AWS services like DynamoDB, S3 and others.
<!--more-->

## Why Use Moto?

First, why do we need some other library to help us test our code here? Well, one common scenario when testing code that interacts with cloud services is determining how you're going to mimic the functionality of those services. You can't always replicate them completely locally, so how do you do it? Well there are two approaches I want to briefly mention here that I think have some merit.

### Using a `testing` Stage That's in the Cloud

Because serverless infrastructure is so cheap, you can usually spin up an entire stack of your application in a test environment in order to run unit and integrations tests against live AWS resources. For example, you can create a DynamoDB table, an API Gateway endpoint, a series of Lambda Functions, and some SQS queues or SNS Topics for a few cents or less. This means that you can create a very similar architecture to your production environments to run your tests against. Now this approach has some benefits and drawbacks.

**Benefits:**

- You don't have to worry about if you're emulating the cloud correctly, you're actually testing in the cloud already!
- You can avoid issues that might be symptomatic of your personal machines
- It's fairly cheap to take this approach in many cases

**Drawbacks:**

- Provisioning your infrastructure can take a few minutes
- Keeping your infrastructure clean of test data can be a challenge
- You can't run these tests without connecting to the cloud

### Using Locally Emulated Testing Fixtures for Cloud Resources

Rather than creating cloud resources, you can use testing libraries that enable you to mock entire cloud services locally. You might create a mocked DynamoDB table or S3 Bucket locally that you then run your application code against, helping you avoid interacting with any cloud services at all. As with the previous option, there are some trade offs here.

**Benefits:**

- It's completely free to take this approach as you don't even need to touch the cloud
- 'Provisioning' your mocked infrastructure is faster than waiting for the cloud
- Creating clean new test environments for your tests is easy
- You can run your tests with no connection to your cloud

**Drawbacks:**

- The testing library might not always perfectly emulate the cloud service, especially as services change over time
- It can be easy to misconfigure these local setups

### Which Strategy to Use?

Both of these two options are completely valid options and depending on the project at hand you might want to choose one over the other. As you move up the testing ladder from unit tests to integration tests you will probably end up wanting or having to use real infrastructure over mocked services.

For unit testing however, mocking services locally is a perfectly good option. Let's learn how to do this with Python, `pytest` and `moto`!

## Getting Started

### Dependencies 
For this demo project, I'll be showing you how to use the Python testing framework `pytest` in combination with the `moto` library to test a simple [Serverless Framework](http://serverless.com/) service on AWS. Here are the prerequisites you'll need:

- [Python 3](https://www.python.org/downloads/)
- [The Serverless Framework](https://www.serverless.com/framework/docs/getting-started/)
- An AWS Account with the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) installed
- Clone the code for this project: `git clone https://github.com/fernando-mc/serverless-testing-moto-pytest.git` 

### Overview of Our Application

For the purposes of this project, we'll be creating an application that creates a "survey" item inside of a DynamoDB Table. To do this, we'll create a `Survey` class in Python and a Lambda Function handler. We'll want to test both of these. Here's what our project's structure looks like:

```
.
├── conftest.py
├── requirements.txt
├── serverless.yml
├── src
│   ├── __init__.py
│   ├── data
│   │   ├── __init__.py
│   │   ├── create_survey.py
│   │   └── tests
│   │       └── test_create_survey.py
│   ├── entities
│   │   ├── __init__.py
│   │   ├── surveys.py
│   │   └── tests
│   │       └── test_surveys.py
│   └── handlers
│       ├── __init__.py
│       ├── create_survey_handler.py
│       └── tests
│           └── test_create_survey_handler.py
└── test-requirements.txt
```

Now if this seems a bit overwhelming, stay with me! It's structured like this to make it easier to add more entities, more handlers, and more tests later on. If you'd like a look at a more complete version of this project you can view it [here](https://github.com/fernando-mc/serverless-surveys). For now, let's look at each of the components above. 

The `conftest.py`, and `test-requirements.txt` files are related to getting out testing set up.

The `serverless.yml` file will help us deploy the project to AWS and the `requirements.txt` will contain the production requirements for the app.

The `src` folder contains all the Python code we'll be running for our application. Inside of it we have the `handlers` file which will contain the Python handlers that will process the incoming data from the API Gateway. These handlers will use the functions in the `data` folder to help take specific actions on the data. Those will in turn rely on the entities in the `entities` folder to properly serialize the data.

All the `__init__.py` files allow Python to `import` code from these folders. This is needed to allow our files to talk to one another. It will also allow the tests inside our `tests` folders to access the code they need to test.

Finally, we have our actual code in `surveys.py`, `create_survey.py`, and `create_survey_handler.py` as well as the files that will help us test it inside of the corresponding `tests` folders. The `test_` prefixes on each of the files in those folders will allow `pytest` to automatically discover our tests with these names.

### Initial Setup

In order to run these tests, we'll need to create a Python virtual environment with `venv` to install the dependencies in `test-requirements.txt`. To do this we can follow these steps:

- To create a virtual environment called `venv` run: `python3 -m venv venv` 
- To activate the environment (on a Mac and most Linux machines): `source venv/bin/activate`
- To activate the environment (on Windows with cmd.exe): `venv\Scripts\activate.bat`
- To activate the environment (on Windows with PowerShell): `venv\Scripts\Activate.ps1`
- Then, install the dependencies: `pip3 install -r test-requirements.txt`

That should get our test dependencies setup and ready to use to run tests. If you want to try running the tests now to see if they work, run `pytest`.

## Reviewing the Code

Now let's take a closer look at our code and our tests for that code. To get things started, we'll need to look at some of the setup we're doing in `conftest.py`.

**`conftest.py`**

This file is used by `pytest` to setup configuration we'll use throughout the testing of this project. We'll create something called "fixtures" in this file that we can use to help setup the mocked infrastructure inside of our tests.

First, we import our dependencies and setup some environment variables that we'll want set when these tests are being run.

```py
import os

import boto3
import pytest

from moto import mock_dynamodb2

os.environ['DYNAMODB_TABLE'] = 'surveys'
os.environ['AWS_DEFAULT_REGION'] = 'us-east-1'
```

The `moto` library will be used to mock the Amazon DynamoDB service later in this file. The two environment variables run in this part of the file will then setup the environment variables we'll want for the entire testing process.

Nest, we setup our first Pytest fixture:

```py
@pytest.fixture(scope='function')
def aws_credentials():
    """Mocked AWS Credentials for moto."""
    os.environ['AWS_ACCESS_KEY_ID'] = 'testing'
    os.environ['AWS_SECRET_ACCESS_KEY'] = 'testing'
    os.environ['AWS_SECURITY_TOKEN'] = 'testing'
    os.environ['AWS_SESSION_TOKEN'] = 'testing'
```

Fixtures allow us to reuse bits of code in our tests. In this case, this fixture sets up fake AWS credentials for when we run our tests. This will help us avoid inadvertently running tests with real AWS credentials. 

After this, we set up a `dynamodb` fixture which will use our `aws_credentials` to create a mocked DynamoDB service resource:

```py

@pytest.fixture(scope='function')
def dynamodb(aws_credentials):
    with mock_dynamodb2():
        yield boto3.resource('dynamodb', region_name='us-east-1')
```

This resource will yield the resource so it can be used in other fixtures like the `dynamodb_table` fixture:

```py
@pytest.fixture(scope='function')
def dynamodb_table(dynamodb):
    """Create a DynamoDB surveys table fixture."""
    table = dynamodb.create_table(
        TableName='surveys',
        KeySchema=[
            {
                'AttributeName': 'PK',
                'KeyType': 'HASH'
            },
            {
                'AttributeName': 'SK',
                'KeyType': 'HASH'
            }
        ],
        AttributeDefinitions=[
            {
                'AttributeName': 'PK',
                'AttributeType': 'S'
            },
            {
                'AttributeName': 'SK',
                'AttributeType': 'S'
            }
        ],
        ProvisionedThroughput={
            'ReadCapacityUnits': 1,
            'WriteCapacityUnits': 1
        }
    )
    table.meta.client.get_waiter('table_exists').wait(TableName='surveys')
    yield
```

This one is a bit longer, but essentially it uses the mocked `dynamodb` service resource fixture in order to create a new mocked DynamoDB `surveys` table. It then waits on the existence of the table before yielding. We'll use this fixture inside of our testing code later on to rely on the mocked table when running our tests.

Now let's look at some of the code we're trying to test.

**`surveys.py`**

Inside of the `entities` folder we have our only entity, `surveys.py`. It will be responsible for instantiating survey class instances and then generating DynamoDB keys and items from there:

```py
import uuid


class NoCustomerIdException(Exception):
    pass
```

It starts by importing the built-in `uuid` library which allow us to generate unique ids for surveys. It will also create a custom `NoCustomerIdException` which will be used when we lack a customer id value when trying to create a survey. Next, we define the `Survey` class: 

```py
class Survey:
    """Deals with customer created surveys"""

    def __init__(self, customer_id=None, survey_id=None, survey_data=None):
        if customer_id is None:
            raise NoCustomerIdException("Surveys require a customer_id")

        self.customer_id = customer_id

        if survey_id:
            self.survey_id = survey_id
        else:
            self.survey_id = str(uuid.uuid4())
        if isinstance(survey_data, dict):
            self.survey_data = survey_data
```

The `__init__` function will take a few inputs in order to create the properties used by the class instance. After the class is instantiated, it also has some helper methods to create an item key for DynamoDB and a method to create item data in the format it's needed for DynamoDB:

```py
class Survey:
    # ...

    def key(self):
        return {
            'PK': f'CUSTOMER#{self.customer_id}',
            'SK': f'SURVEY#{self.survey_id}',
        }

    def to_item(self):
        return {
            **self.key(),
            "customer_id": self.customer_id,
            "survey_id": self.survey_id,
            "survey_data": self.survey_data
        }
```

So how do we test all this? First, let's review some things we might want to test.

When instantiating the function with `__init__` we probably want to make sure that:

- It raises a `NoCustomerIdException` if it doesn't get a customer id.
- We can access all the properties if it's instantiated correctly
- When a survey id is not provided that it falls back to a uuid

We might also want to test that:

- The `key()` method returns a correctly formatted key
- The `to_item()` method returns a correctly formatted item

Fortunately for you, I've already written these tests in `test_surveys.py` in the `src/entities/tests` folder. Let's take a look!

**`test_surveys.py`**

First, we're going to need to use a few imports. We'll use the `pytest.raises()` method later on to make sure an exception is throw so we need to import `pytest` now. We also will use the Python regular expression library `re` in order to validate the uuid format we expect later.

Finally, we need to import the code we want to test and the Exception we'll expect to be thrown when part of it fails:

```py
import pytest
import re
from src.entities.surveys import Survey, NoCustomerIdException
```

From here, we have a little helper function that's not actually running as a test called `valid_uuid()`. This will just return `True` or `False` depending on if the value passed into it matches the regex for a uuid.

```py
def valid_uuid(uuid):
    regex = re.compile(
        '^[a-f0-9]{8}-?[a-f0-9]{4}-?4[a-f0-9]{3}-?[89ab][a-f0-9]{3}-?[a-f0-9]{12}\\Z',
        re.I
    )
    match = regex.match(uuid)
    return bool(match)
```

After that, we can get to our tests! Remember, here's what we wanted to test from above:

- It raises a `NoCustomerIdException` if it doesn't get a customer id.
- We can access all the properties if it's instantiated correctly
- When a survey id is not provided that it falls back to a uuid
- The `key()` method returns a correctly formatted key
- The `to_item()` method returns a correctly formatted item

When checking for exceptions, it's usually a good idea to create a custom exception, which is why in `surveys.py` we had a custom exception class created. In order to validate if a particular exception is thrown, we use the `pytest.raises()` context manager:

```py
def test_instantiating_survey_class_with_no_data_fails():
    with pytest.raises(NoCustomerIdException):
        Survey()
```

The code above essentially says: "I expect this next chunk of code to raise the `NoCustomerIdException`, if it doesn't, we have a problem!"

Next, we want to make sure our data is able to be accessed correctly. This is a bit simpler of a test to write:

```py
def test_instantiating_survey_class_with_valid_data():
    customer_id = '1'
    survey_id = '2'
    survey_data = {'key': 'value'}
    survey = Survey(customer_id, survey_id, survey_data)

    assert survey.customer_id == customer_id
    assert survey.survey_id == survey_id
    assert survey.survey_data == survey_data
```

All we're doing is providing some initial values and making sure we can access those same values later on.

Next, we do a somewhat similar test that checks to make sure that when a survey_id is not provided that we generate a uuid for it instead:

```py
def test_instantiating_survey_with_blank_survey_id_uses_uuid_string_fallback():
    customer_id = '1'
    survey_id = None
    survey = Survey(customer_id, survey_id)

    assert survey.customer_id == customer_id
    assert survey.survey_id is not None
    assert isinstance(survey.survey_id, str)
    assert valid_uuid(survey.survey_id)
```

Finally, we get into testing the other methods on the class. We can provide some initial values to the `Survey` class instance on initialization and then we test that it returns the key as we expect.

```py
def test_survey_key():
    customer_id = 'TESTID'
    survey_id = 'TESTID'
    survey = Survey(
        customer_id=customer_id,
        survey_id=survey_id,
        survey_data=None
    )
    test_key = {
        'PK': 'CUSTOMER#TESTID',
        'SK': 'SURVEY#TESTID'
    }
    assert isinstance(survey.key(), dict)
    assert survey.key() == test_key
```

After that, we'll also want to check that the items are initialized properly by the class going forward:

```py
def test_to_item_serialization():
    customer_id = 'TESTID'
    survey_id = 'TESTID'
    survey = Survey(
        customer_id=customer_id,
        survey_id=survey_id,
        survey_data={'survey': 'data'}
    )
    test_item = {
        'PK': 'CUSTOMER#TESTID',
        'SK': 'SURVEY#TESTID',
        'customer_id': 'TESTID',
        'survey_id': 'TESTID',
        'survey_data': {'survey': 'data'}
    }
    assert isinstance(survey.to_item(), dict)
    assert survey.to_item() == test_item
```

**`create_survey.py`**

This file will be used to create a survey item in a DynamoDB table. To do this, it will first need to have a way to create a DynamoDB table that we can inject a mocked DynamoDB table into. It starts by creating a `get_table()` function to create a DynamoDB table service resource and calling it directly in the file:

```py
import boto3
import os


def get_table():
    dynamodb = boto3.resource("dynamodb", region_name='us-east-1')
    table = dynamodb.Table(os.environ["DYNAMODB_TABLE"])
    return table

# This will run in the Lambda environment and be reused across invocations
default_table = get_table()
```

While we don't technically need to do this outside of the scope of a containing function, it allows us to create this resource in a way that will optimize the runtime of our Lambda functions later on. Next, we have the `create_survey()` function which will take in a survey instance object and use the `default_table` resource we just created:

```py
def create_survey(survey=None, table=default_table):
    try:
        table.put_item(
            Item=survey.to_item()
        )
        return survey
    except Exception as e:
        print("Error creating survey")
        print(e)
        error_message = "Could not create survey"
        return {
            "error": error_message
        }
```

Critically, this syntax here: `create_survey(survey=None, table=default_table)` allows us the option to override the `table` argument later on if we want to replace it when testing with a mocked table. 

If we didn't construct it like this, then we would have no mechanism to override the table used when testing. Let's look at how we might want to test this.

**`test_create_survey.py`**

In order to keep our tested code independent from other parts of the codebase we'll need to create a stub class to stand in for the actual `Surveys` class:

```py
class StubSurvey:

    def __init__(self):
        pass
    
    def to_item(self):
        return {
            "PK": "CUSTOMER#TEST1",
            "SK": "SURVEY#TEST1",
            "customer_id": "TEST1",
            "survey_id": "TEST1",
            "survey_data": {"TEST": "DATA"}
        }
```

We'll also want to isolate our tests from live infrastructure. To do this, we'll create a function to return a DynamoDB table:

```py
def mocked_table():
    import boto3
    import os
    dynamodb = boto3.resource("dynamodb", region_name='us-east-1')
    table = dynamodb.Table(os.environ["DYNAMODB_TABLE"])
    return table
```

Importantly, we wont run this function or it's imports outside of the context of a test. This is because our tests can rely on the fixtures we created earlier in `conftest.py`. In the test we write here, we're relying on the `dynamodb_table` fixture which makes sure that a mocked DynamoDB table exists first. At this point we can import the function we want to test, leverage our `StubSurvey` and create the table with `mocked_table()`.

```py

def test_create_survey(dynamodb_table):
    from src.data.create_survey import create_survey
    survey_instance = StubSurvey()
    table = mocked_table()
    assert create_survey(survey=survey_instance, table=table) == survey_instance
```

This test mainly just checks that the function behaves as we expect. But we could also assert that an item has been added to the table or add additional tests we might think are necessary.

**`create_survey_handler.py`**

In this application, my handler has a lot of work done for it by the [Lambda Decorators module](https://lambda-decorators.readthedocs.io/en/latest/). It's a great way to hand off a lot of the tiresome tasks you might need to do with AWS Lambda like loading and dumping JSON data, and adding the appropriate status code messages or CORS headers.

You'll see this file bring in a lot of those decorators along with using the `Survey` class and the `create_survey` function.

```py
from lambda_decorators import (
    load_json_body, json_schema_validator,
    cors_headers, json_http_resp)
from src.entities.surveys import Survey
from src.data.create_survey import create_survey
```

Next, we'll create a `request_schema` that one of the lambda decorators will use to validate incoming payload data:

```py
request_schema = {
    'type': 'object',
    'properties': {
        'body': {
            'type': 'object',
            'properties': {
                'customer_id': {'type': 'string'},
                'survey_id': {'type:': 'string'},
                'survey_data': {'type': 'object'}
            },
            'required': ['customer_id', 'survey_data']
        }
    },
    'required': ['body'],
}
```

After that, we define out handler and use a bunch of these decorators to load JSON data from the body, validate the schema of the payload after that, and insert CORS headers and proper response codes.

```py
@load_json_body  # Doing this first is required for the schema to validate
@json_schema_validator(request_schema=request_schema)
@cors_headers
@json_http_resp
def handler(event, context):
    survey = Survey(**event['body'])
    create_survey(survey)
    if event.get('error'):
        raise Exception(event['error'])
    else:
        return event['body']
```

You'll notice the actual content of the handler is pretty short and leans heavily on our dependencies and decorators. This makes it a little more awkward to test in isolation, but to do this we can try something called "monkeypatching". Let's see how now.

**`test_create_survey_handler.py`**

In the tests for this handler we'll need to monkeypatch the handlers dependencies. In order to do this, we'll need to import pytests and those dependencies.

```py
import pytest
import json
import src.entities.surveys
import src.data.create_survey
```

From there, we'll create some stub classes that will basically do nothing, this admittedly doesn't get at testing the functionality of those classes, but we have the other unit tests (or some integration tests) for that purpose.

```py
class MockSurvey:
    pass

class Context:
    pass
```

Next, we'll create a test event to send into the handler: 

```py
event = {
    "body": json.dumps({
        "customer_id": "1",
        "survey_id": "1",
        "survey_data": {
            "question1": "sup?"
        }
    })
}
```

From here, we'll need to set up our monkeypatching to use in our tests. We can do this with a new pytest fixture:

```py
@pytest.fixture(scope='function')
def setup_handler_monkeypatching(dynamodb_table, monkeypatch):
    def mock_survey(*args, **kwargs):
        return MockSurvey()

    def mock_create_survey(*args, **kwargs):
        pass
    monkeypatch.setattr(src.entities.surveys, "Survey", mock_survey)
    monkeypatch.setattr(src.data.create_survey, "create_survey", mock_create_survey)
```

Essentially, this allows us to replace the `Survey` class and `create_survey` function that will be imported into our handler with our own mocked values. Right now, these basically do nothing and allow us to skip over those lines and test that the return value will be constructed as we expect from the incoming body.

With this setup, we can test that we have CORS values returned properly and that we return a JSON body:

```py
def test_create_survey_handler_has_cors_handlers(setup_handler_monkeypatching):
    from src.handlers.create_survey_handler import handler
    result = handler(good_event, Context())
    assert result['headers'] == {'Access-Control-Allow-Origin': '*'}


def test_create_survey_handler_has_json_body(setup_handler_monkeypatching):
    from src.handlers.create_survey_handler import handler
    result = handler(good_event, Context)
    assert isinstance(json.loads(result['body']), dict)
```

We can also test that the handler returns a 400 status code and a body that contains the string 'RequestValidationError` when we give it a bad event:

```py
def test_create_survey_handler_returns_schema_validation_error(setup_handler_monkeypatching):
    from src.handlers.create_survey_handler import handler
    result = handler({"bad_key": "bad_value"}, Context)
    assert result['statusCode'] == 400
    assert 'RequestValidationError' in result['body']
```

Is there more we could test for? Probably! But this should get us started with a few good unit tests and some strategies for common situations we might encounter! 

## What Next?

If you want to keep an eye on future projects and things I'm working on make sure to sign up for my [mailing list](/mailing-list).

You can also keep an eye on [this repository](https://github.com/fernando-mc/serverless-surveys) as I continue to develop it and add more endpoints and integrations!