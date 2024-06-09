+++
Description = "A look at using the AWS parameter store to handle secrets securely."
Tags = [
  "Architecture",
  "AWS",
  "Serverless",
  "Python",
  "Secrets",
  "Parameter Store",
]
Categories = [
  "Serverless",
  "Security",
  "AWS",
]
title = "Secrets in Parameter Store"
publishdate = "2024-06-10T16:16:07-07:00"
date = "2024-06-10T16:16:07-07:00"
type = "blog"
sponsor_message = "Then you might like my book! This post is an excerpt from The Serverless Cookbook."
sponsor_link = "https://theserverlesscookbook.com/"
[image]
    feature = "/images/secrets-in-parameter-store/secrets-in-parameter-store.png"
    postheader = "/images/abstract-6-short.png"
+++

Protecting the API Keys, database passwords, and other secrets used by your applications is a critically important task for any developer. Fortunately, there are excellent options for this when developing serverless applications. We'll look at one of the most common: The AWS Systems Manager Parameter Store.

<!--more-->

Parameter Store is an AWS service that allows you to store and fetch secrets and other data. Typically, it's used to store things like API Keys, passwords, configuration strings, and other secrets that your application might need.

Here's a diagram of how we'll use the Parameter Store in this project:

![Diagram of Dog API key access to dogs API](/images/secrets-in-parameter-store/ssm-parameter-store.png)

Our project will deploy a Lambda-based API that uses the API key stored in Parameter Store to access a protected API and fetch the image URLs of dog photos that we can return to the browser.

**Prerequisites**

Just like most of these projects, you'll need Python 3 installed and the Serverless Framework installed and configured along with AWS credentials.

You'll also want to create an account with one of these APIs that issues free, but limited-use API Keys:

- https://www.thedogapi.com/signup
- https://www.omdbapi.com/

I've played with both of these APIs but I don't control them, so I can't guarantee they'll be around forever. This project will use the Dog API by default. But, just in case the Dog API is no longer working at a future date, I've also included commented out code that you can use with the OMDB API.

After you've signed up for API keys, you can store them encrypted and securely in the AWS Parameter store by including your API Key in the code below and running the code snippet below:

```bash
aws ssm put-parameter \
--name DOGS_API_KEY \
--value REPLACE_ME \
--type SecureString
```

Or, if you're using the OMDB API:

```bash
aws ssm put-parameter \
--name OMDB_API_KEY \
--value REPLACE_ME \
--type SecureString
```

This process is all it takes to setup a securely encrypted API key in the Parameter store - that's half the job done already!

**Project Code**

Next, open the code for this project from the `secrets-parameter-store` demo folder. See Figure RefNum{secrets-in-parameter-store-tree} for the folder structure.

![A screenshot of the folder structure](/images/secrets-in-parameter-store/secrets-in-parameter-store-tree.png)

When you have the project code, open it up in your text editor or IDE.

**Deep Dive**

### `serverless.yml`

First, let's see how the project is structured inside of the `serverless.yml` file.

```yaml
service: ssm-with-dogs-and-movies
frameworkVersion: '3'

provider:
  name: aws
  runtime: python3.10
  environment:
    DOGS_API_KEY: ${ssm:/DOGS_API_KEY}
```

The first half of the file is boilerplate, with the exception of the `environment` section. This is where all the good stuff with our Parameter Store secrets happens. But it's so simple it's easy to miss!

Because we've already pushed our `DOGS_API_KEY` to AWS with the CLI, it's now available to automatically be injected into our service at deploy time. The `${ssm:DOGS_API_KEY}` is what allows us to reference a parameter, in this case `DOGS_API_KEY` directly from the Parameter Store.

Next up, we have our Lambda function, configured with a single HTTP API route and the GET method:

```yaml
functions:
  funDogOrMovie:
    handler: fun.handler
    events:
      - httpApi:
          path: /
          method: get
```

This means, that whenever we access the URL for this endpoint in the browser we'll be able to load up what the Lambda function returns (an image of a cute doggo).

::: infotext
If you're confused why the shorthand for injecting variables is "ssm" instead of "ps" or "params," that's understandable. Basically, AWS is terrible at naming things and the naming for the parent service of the Parameter store has [changed many times](https://docs.aws.amazon.com/systems-manager/latest/userguide/service-naming-history.html). It used to be the "Simple Systems Manager" or "SSM" and, because it's the parent service of the Parameter Store, that's where this abbreviation came from. Also, it's the naming convention for most of the relevant API calls related to Parameter store in the AWS SDKs [like boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ssm.html#ssm).

### `fun.py`

Now let's look at the file that fetches photos of dogs and returns them back to us. Inside `fun.py` we start by loading up our Python libraries:

```py
import os
import json
import random
from urllib.request import Request, urlopen
```

We use the `os` library to import the `DOGS_API_KEY` that we set in `serverless.yml`. This environment variable is automatically loaded from the parameter store and included as a Lambda environment variable when this project is deployed:

```py
try:
    DOGS_API_KEY = os.environ['DOGS_API_KEY']
except KeyError:
    print('DOGS_API_KEY environment variable not set')
```

Next, we have the function that loads the image of a random dog from the Dog API:

```py
def random_dog_image():
    req = Request('https://api.thedogapi.com/v1/images/search')
    req.add_header('x-api-key', DOGS_API_KEY)
    json_content = urlopen(req).read().decode()
    content = json.loads(json_content)
    url = content[0]['url']
    return url
```

This function uses the `DOGS_API_KEY` to authorize the request to the Dog API by setting it as the value of the `x-api-key` header. Then we parse the JSON from the response and return the URL of an adorable doggo.

This function is used in the handler to get a random image:

```py
def handler(event, context):
    url = random_dog_image()
    html = f'<img style="max-width: 600px" src="{url}">'
    response = {
        "statusCode": 200,
        "body": html,
        "headers": {
            'Content-Type': 'text/html',
        }
    }
    return response
```

In the handler, we return an HTML response with an `img` tag pointing to the URL of the image we retrieved with our API keys so that it loads in the browser when the response is returned:

**Deployment**

To deploy the project, make sure you've already pushed your API key to the Parameter store with the AWS CLI command from the prerequisites section. If it's not already accessible when deploying the project, the deployment will fail.

Next, run `serverless deploy` and copy down the API endpoint of the result:

```yaml
endpoint: GET - https://example123.execute-api.us-east-1.amazonaws.com/
```

**Testing**

To test whether the deployment worked, open up your browser to the URL of the GET endpoint and you should be able to refresh the page to see a doggo! SeeFig{doggo}

![Screenshot of a dog image being rendered in the browser as a result of our API](/images/secrets-in-parameter-store/doggo.png)

If it's *not* working, try checking the logs for errors with `serverless logs --function funDogOrMovie`.

**Considerations**

### Deployment Time Environment Variables or Calling the SSM API?

One important thing to realize here is that this project stores the key securely in Parameter Store and then includes it as an environment variable in our Lambda Function at *deployment time*.

But, if we ever need to rotate the key in SSM we also need to redeploy the service so it picks up the new key as a new environment variable. This is also why we don't need any special permissions granted to the Lambda function in this project to access the SSM parameter.

This is a great way to avoid calling the SSM API each time our API runs. However, it does mean the API key is stored as a Lambda environment variable. These environment variables are encrypted but you might want to take another approach at securing them. You can have your project correctly pick up new API keys whenever you rotate them by calling the SSM API directly each time the function needs the API key. This will always get the most recent value but you would have to think about the limits on the Parameter Store APIs.

### When to use Parameter Store

Parameter Store is cheap and secure option for managing your secrets when using its Secure String encryption features. It's most common alternative within AWS is the AWS Secrets Manager. When using Parameter Store, your parameters are considered "Standard" parameters by default. But, in some cases you might want to supercharge them into "Advanced Parameters" or even start using the AWS Secrets Manager.

So let's review some of the most common limitations and feature requirements that would prompt and upgrade between these options by looking at features that you don't get from the 'Standard' parameter type of the Parameter Store.

You need to upgrade from Standard Parameters if you need:

- To add policies to your parameters directly which can be done either using Advanced Parameters or Secrets Manager
- To use secrets over the 4 Kb limit for Standard Parameters. Advanced Parameters have an 8 Kb size limit and Secrets Manager has a 10 Kb limit

And the following features only Secrets Manager supports natively:

- Automated key rotation
- Cross region secret replication
- Cross account secret usage: for example from one 'security' AWS account to another 'development' or 'production' account (Secrets Manager only)

While "Advanced Parameters" cost slightly more, and have a charge per API call, you can get access to those additional features. AWS Secrets Manager will charge you a significantly higher cost per secret versus the Parameter Store Parameters, but if you need the added functionality using AWS Secrets Manager is the best choice.

For a comparison of throughput capabilities in transactions per second or number of parameters or secrets you can review the [Parameter Store Quotas](https://docs.aws.amazon.com/general/latest/gr/ssm.html) and [Secrets Manager Quotas](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_limits.html).

### Another Option: KMS

Another slightly more complicated option is to encrypt a secret manually with the AWS Key Management Service (KMS). Then you can save the encrypted text as an environment variable and decrypt that at runtime. At scale this is somewhat cheaper than either of the other options but it's also more complicated to set up.

### Rendering HTML with Lambda

This example is designed to show you how to securely deploy and use secrets within your applications. But to do this, I also use an API that returns raw HTML. While you *could* do this on a project, it's probably not a good idea. This is because it makes the distinction between the backend and frontend of your application less clear. In the long run this will make managing changes to your application more difficult and potentially introduce security or rendering issues that might come from manually working with HTML from the backend.

If you want to see more options for creating APIs and serving websites, then make sure to look at the section on serverless APIs and the chapter on Static Websites with Serverless Finch.

**Next Steps**

This example post is an excerpt from my new book: [The Serverless Cookbook](https://theserverlesscookbook.com/). If you want to see all of the code for this project along with detailed video tutorials on each project in the book, take a look!
