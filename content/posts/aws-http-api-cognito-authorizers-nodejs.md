+++
Description = "Learn how to use AWS HTTP APIs with Amazon Cognito Authorizers using Node.js."
Tags = [
  "Architecture",
  "Serverless",
  "AWS",
  "API",
  "Lambda",
  "AWS Lambda",
  "Serverless Framework",
]
Categories = [
  "AWS",
  "Projects",
]
title = "Creating AWS HTTP APIs with Cognito Authorizers Using Node.js"
publishdate = "2020-05-19T11:00:51-07:00"
date = "2020-05-19T11:00:51-07:00"
type = "blog"
[image]
    feature = "/images/20-projects-20-days/authorizers.png"
    postheader = "/images/abstract-1.jpg"
    credit = "Janne Räkköläinen"
    creditlink = "https://www.flickr.com/photos/jannes_shootings/48800150962/"
+++

Today is project twelve from my [Twenty Projects in Twenty Days](https://fernandomc.com/posts/twenty-projects-in-twenty-days/) series! Yesterday, I published  [Voices of COVID](posts/voicesofcovid/) which is a project aimed at hearing the voices of people impacted by COVID-19. Today, I'm looking at how to create an AWS HTTP API that has JWT authorizers with Amazon Cognito and Lambda handlers written in Node.js. If you want a more in-depth look at this you can take a look back at how I did this with the Serverless Framework in [this blog post](https://www.serverless.com/blog/serverless-auth-with-aws-http-apis/).

Let's get started!
<!--more-->

## Prerequisites 

1. You'll need to get the code from GitHub [here](https://github.com/fernando-mc/aws-http-api-node-cognito)
2. You'll also need the AWS CLI [installed and configured](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)
3. Then you'll need to install the [Serverless Framework](https://serverless.com/)
4. For this demo, I'll also be using [Node.js](https://nodejs.org/en/download/) but you technically don't need it locally to deploy the service

## Setting up the HTTP API

In order to get an AWS HTTP API setup in AWS we could manually configure it in the AWS console or with the AWS CLIs. Instead, I opted to use the Serverless Framework to take care of this for us. We can do this by setting up an HTTP API event for a Lambda Function in the `serverless.yml` file. 

First, we need to setup a the service details at the top with a service name and potentially an `org` and `app` if we're using Framework Pro. 

```yml
org: yourorg # optional
app: yourapp # optional
service: http-api-node
```

From there, we have a `provider` section where we setup the details for our cloud (AWS), our runtime (Node.js), and any environment variables we're using.

```yml
provider:
  name: aws
  runtime: nodejs12.x
  environment:
    DOMAIN_SUFFIX: your-unique-suffix
  # ...
```

In this case, the `DOMAIN_SUFFIX` environment variable should be replaced with something unique so that when you try to create Cognito resources later the names that you use for them won't conflict with resources other people like me have already created. The next part of this document is specifying that our HTTP API will be using Cognito Authorizers:

```yml
  # ...
  httpApi:
    authorizers:
      serviceAuthorizer:
        identitySource: $request.header.Authorization
        issuerUrl: 
          Fn::Join:
          - ''
          - - 'https://cognito-idp.'
            - '${opt:region, self:provider.region}'
            - '.amazonaws.com/'
            - Ref: serviceUserPool
        audience:
          - Ref: serviceUserPoolClient
```

In this section, we provide more details about the `httpApi` resource that will be used in our service. We give this API an authorizer that has an identity source of the `Authorization` header. 

Then we provide it an `issuerUrl` to verify against. In this case, the `issuerUrl` is a combination of the ID of a `serviceUserPool` resource that we'll create in a moment and a standard formula for creating these URLs that starts with `https://cognito-idp.` and then has an AWS region like `us-east-1` followed by `.amazonaws.com/` and finally the ID of the `serviceUserPool`. The `Fn:Join:` and the extra `-` characters and all that are just messy YAML needed to make all those values come together properly.

Finally, we have the `audience`, which in this case is the id of the `serviceUserPoolClient` which we create in a moment too.

Next up, we actually configure the HTTP API as an event trigger for a Lambda Function:

```yml
functions:
  getProfileInfo:
    handler: profile.get
    events:
      - httpApi:
            method: GET
            path: /user/profile
            authorizer: serviceAuthorizer
  createProfileInfo:
    handler: profile.post
    events:
      - httpApi:
            method: POST
            path: /user/profile
            authorizer: serviceAuthorizer
```

In this case, we create an HTTP API with the `httpApi` event for each function. We then provide the functions with a method - GET and POST and a path they're tied to with the API. These functions live inside the `profile.js` file but aren't much to look at as they don't actually take an action on user profiles in this example. Now let's look at the last part of the `serverless.yml` file!

## Setting up the Cognito Authorizer

There are a few options for setting up a Cognito Authorizer. Often, I'll be lazy and just do this in the AWS console and copy and paste the details I need manually. But you can also use a tool like CloudFormation to create your Cognito User Pool and related resources for you. In this case, I do this inside of the `serverless.yml` file's `resources` section. Let's take a look!

In the latter half of my `serverless.yml` file you should see this:

```yml
resources:
  Resources:
    HttpApi:
      DependsOn: serviceUserPool
    serviceUserPool:
      Type: AWS::Cognito::UserPool
      Properties:
        UserPoolName: service-user-pool-${opt:stage, self:provider.stage}
        UsernameAttributes:
          - email
        AutoVerifiedAttributes:
          - email
    serviceUserPoolClient:
      Type: AWS::Cognito::UserPoolClient
      Properties:
        ClientName: service-user-pool-client-${opt:stage, self:provider.stage}
        AllowedOAuthFlows:
          - implicit
        AllowedOAuthFlowsUserPoolClient: true
        AllowedOAuthScopes:
          - phone
          - email
          - openid
          - profile
          - aws.cognito.signin.user.admin
        UserPoolId:
          Ref: serviceUserPool
        CallbackURLs: 
          - https://localhost:3000
        ExplicitAuthFlows:
          - ALLOW_USER_SRP_AUTH
          - ALLOW_REFRESH_TOKEN_AUTH
        GenerateSecret: false
        SupportedIdentityProviders: 
          - COGNITO
    serviceUserPoolDomain:
      Type: AWS::Cognito::UserPoolDomain 
      Properties:
        UserPoolId: 
          Ref: serviceUserPool
        Domain: service-user-pool-domain-${opt:stage, self:provider.stage}-${self:provider.environment.DOMAIN_SUFFIX}
```

This section near the top allows me to make sure that my Cognito User Pool will be created before the HTTP API so that I can use it when creating my HTTP API:

```yml
    HttpApi:
      DependsOn: serviceUserPool
```

After that, I create a few more AWS resources: 

- The `serviceUserPool`
- The `serviceUserPoolClient`
- And the `serviceUserPoolDomain`

Each of these will be required in order to get the details I need to register users in my application and then to allow them to get JSON Web Tokens on sign-in that they can use to authenticate to my API.

You'll notice that there is also some syntax in there that looks like this: `${stuff-in-here}`. This syntax is pulling variables from the rest of the `serverless.yml` file in order to create unique stage-specific AWS resources. In this case, they pull from the stage of the application or the `DOMAIN_SUFFIX` defined earlier in the file.

With all these things in `serverless.yml` explained, we can now deploy our service with `serverless deploy`!

This should go through the process of deploying our HTTP API service out and creating the Cognito resources we need to add authorization and authentication to it! If you'd like to learn more about how to test it out using the Cognito Hosted UI check out my post on this [here](https://www.serverless.com/blog/serverless-auth-with-aws-http-apis/)!

Tomorrow, I'll show you how to write a more complex Lambda handler using Python that actually gets and creates some profile data for each user!
