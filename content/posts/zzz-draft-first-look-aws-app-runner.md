+++
draft = true
Description = ""
Tags = [
  "Architecture",
  "Azure",
  "AWS",
  "Serverless",
  "Python",
]
Categories = [
  "Architecture",
  "Azure",
  "AWS",
]
title = ""
publishdate = "2022-05-18T21:50:40-07:00"
date = "2022-05-18T21:50:40-07:00"
type = "blog"
[image]
    feature = "/images/postfolder/og-card-name.png"
    postheader = "/images/abstract-6-short.png"
+++

In the last few days I noticed that AWS released a new service to deploy and run containerized applications called "AppRunner". While name is as  ambiguous as many other services I wanted to see how the service itself held up. Especially because when reviewing Azure Container Instances for the first time I was fairly critical of AWS for having no equivalent service. At the time, the nearest proxy was [AWS ECS and Fargate](https://aws.amazon.com/fargate/?whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc&fargate-blogs.sort-by=item.additionalFields.createdDate&fargate-blogs.sort-order=desc) which in my opinion are needlessly complicated services for an average lazy serverless developer like myself.

Today, I'd like to do the same first look review of AppRunner as it seems to be a direct attempt to compete for that space. Let's see how it holds up!

# Prerequisites

1. Make sure you have the latest version of the AWS SDK of your choice. I'll be using Boto3 for Python which you can install/upgrade with: `pip install --upgrade boto3`. 
2. Make sure you have AWS credentials setup on your machine. Usually I do this by installing the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) and using the `aws config` command.

From there, let's look at how to use the AppRunner service.

# Initial Thoughts

My "Is this serverless service designed well?" test after using [three Azure Container Instances commands](/posts/first-look-azure-container-instances/) to spin up a container is how many API methods and entities I need to understand to start using it and how confused I am by them.

For example, EC2 (which is not a serverless service) is correctly viewed as an abomination by serverless developers because its *table of contents* contains 27 different sections and over 450 methods of the client alone. It's basically the atomic-level assembly kit of application development. But serverless developers at least want some Legos.

AWS ECS and AWS Fargate, the awkward forerunners of AppRunner, [share an SDK client](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ecs.html#client) with  56 API methods. This sounds nice until you realize that you still have to understand the underlying EC2 infrastructure and concepts to work with ECS. Networking? Subnets? Security Groups? Gross.

So what do you need to understand to use AppRunner?

# Getting Started 

We could try to use the console and poke around the UI. But, given that the AWS UI seems to change with every employee they [fire](https://www.nytimes.com/2021/04/05/technology/amazon-nlrb-activist-workers.html) for advocating better conditions in Amazon warehouses... We're probably better off sticking to the APIs/SDKs.

'CodeRepository': 
OR
'ImageRepository':


ECS/Fargate which 

https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/apprunner.html#apprunner

https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ecs.html

First off, there are only 26 methods in the Boto3 AppRunner client. This is **excellent**. For comparison, 

# Dire3ct C Started 


# Debugging Tips

Make sure to upgrade your version of boto3 you're using with `pip install --upgrade boto3`. Otherwise you might see something like this: 

```python
>>> import boto3
>>> client = boto3.client('apprunner')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/Library/Frameworks/Python.framework/Versions/3.8/lib/python3.8/site-packages/boto3/__init__.py", line 91, in client
    return _get_default_session().client(*args, **kwargs)
  File "/Library/Frameworks/Python.framework/Versions/3.8/lib/python3.8/site-packages/boto3/session.py", line 258, in client
    return self._session.create_client(
  File "/Library/Frameworks/Python.framework/Versions/3.8/lib/python3.8/site-packages/botocore/session.py", line 834, in create_client
    client = client_creator.create_client(
  File "/Library/Frameworks/Python.framework/Versions/3.8/lib/python3.8/site-packages/botocore/client.py", line 80, in create_client
    service_model = self._load_service_model(service_name, api_version)
  File "/Library/Frameworks/Python.framework/Versions/3.8/lib/python3.8/site-packages/botocore/client.py", line 120, in _load_service_model
    json_model = self._loader.load_service_model(service_name, 'service-2',
  File "/Library/Frameworks/Python.framework/Versions/3.8/lib/python3.8/site-packages/botocore/loaders.py", line 132, in _wrapper
    data = func(self, *args, **kwargs)
  File "/Library/Frameworks/Python.framework/Versions/3.8/lib/python3.8/site-packages/botocore/loaders.py", line 376, in load_service_model
    raise UnknownServiceError(
botocore.exceptions.UnknownServiceError: Unknown service: 'apprunner'. Valid service names are: accessanalyzer, acm . . .
```



0. Create Figma OG card - https://www.figma.com/file/vb6MJuvxbPSvivurKpWTpl/Social-Branding-Templates-(Community)?node-id=45%3A344
1. Update tags and categories
2. Check publish date and date
3. Add all the other front matter
4. Check summary


Some content for a post
<!--more-->

The rest of the content


```python

import boto3 

client = boto3.client('apprunner')
response = client.create_service(
    ServiceName='fernando',
    SourceConfiguration={
        'ImageRepository': {
            'ImageIdentifier': 'public.ecr.aws/aws-containers/hello-app-runner:latest',
            'ImageConfiguration': {
                'Port': '8000'
            },
            'ImageRepositoryType': 'ECR_PUBLIC'
        }
    }
)



{'Service': {'ServiceName': 'fernando', 'ServiceId': '81367710325945d8b76f2f4389b2a76e', 'ServiceArn': 'arn:aws:apprunner:us-east-1:247276349155:service/fernando/81367710325945d8b76f2f4389b2a76e', 'ServiceUrl': '6vsmqcryda.us-east-1.awsapprunner.com', 'CreatedAt': datetime.datetime(2021, 5, 19, 11, 45, 32, 319000, tzinfo=tzlocal()), 'UpdatedAt': datetime.datetime(2021, 5, 19, 11, 45, 32, 319000, tzinfo=tzlocal()), 'Status': 'OPERATION_IN_PROGRESS', 'SourceConfiguration': {'ImageRepository': {'ImageIdentifier': 'public.ecr.aws/aws-containers/hello-app-runner:latest', 'ImageConfiguration': {'Port': '8000'}, 'ImageRepositoryType': 'ECR_PUBLIC'}, 'AutoDeploymentsEnabled': True}, 'InstanceConfiguration': {'Cpu': '1024', 'Memory': '2048'}, 'HealthCheckConfiguration': {'Protocol': 'TCP', 'Path': '/', 'Interval': 5, 'Timeout': 2, 'HealthyThreshold': 1, 'UnhealthyThreshold': 5}, 'AutoScalingConfigurationSummary': {'AutoScalingConfigurationArn': 'arn:aws:apprunner:us-east-1:247276349155:autoscalingconfiguration/DefaultConfiguration/1/00000000000000000000000000000001', 'AutoScalingConfigurationName': 'DefaultConfiguration', 'AutoScalingConfigurationRevision': 1}}, 'OperationId': 'f601a8ada74a4681a11b9b113fdd2aa3', 'ResponseMetadata': {'RequestId': 'd8f4560f-6664-4dbc-b55c-d6a47e48f7fa', 'HTTPStatusCode': 200, 'HTTPHeaders': {'date': 'Wed, 19 May 2021 18:45:32 GMT', 'content-type': 'application/x-amz-json-1.0', 'content-length': '1066', 'connection': 'keep-alive', 'x-amzn-requestid': 'd8f4560f-6664-4dbc-b55c-d6a47e48f7fa'}, 'RetryAttempts': 0}}

{
    "Service": {
        "ServiceName": "fernando",
        "ServiceId": "81367710325945d8b76f2f4389b2a76e",
        "ServiceArn": "arn:aws:apprunner:us-east-1:247276349155:service/fernando/81367710325945d8b76f2f4389b2a76e",
        "ServiceUrl": "6vsmqcryda.us-east-1.awsapprunner.com",
        "CreatedAt": datetime.datetime(
            2021, 5, 19, 11, 45, 32, 319000, tzinfo=tzlocal()
        ),
        "UpdatedAt": datetime.datetime(
            2021, 5, 19, 11, 45, 32, 319000, tzinfo=tzlocal()
        ),
        "Status": "OPERATION_IN_PROGRESS",
        "SourceConfiguration": {
            "ImageRepository": {
                "ImageIdentifier": "public.ecr.aws/aws-containers/hello-app-runner:latest",
                "ImageConfiguration": {"Port": "8000"},
                "ImageRepositoryType": "ECR_PUBLIC",
            },
            "AutoDeploymentsEnabled": True,
        },
        "InstanceConfiguration": {"Cpu": "1024", "Memory": "2048"},
        "HealthCheckConfiguration": {
            "Protocol": "TCP",
            "Path": "/",
            "Interval": 5,
            "Timeout": 2,
            "HealthyThreshold": 1,
            "UnhealthyThreshold": 5,
        },
        "AutoScalingConfigurationSummary": {
            "AutoScalingConfigurationArn": "arn:aws:apprunner:us-east-1:247276349155:autoscalingconfiguration/DefaultConfiguration/1/00000000000000000000000000000001",
            "AutoScalingConfigurationName": "DefaultConfiguration",
            "AutoScalingConfigurationRevision": 1,
        },
    },
    "OperationId": "f601a8ada74a4681a11b9b113fdd2aa3",
    "ResponseMetadata": {
        "RequestId": "d8f4560f-6664-4dbc-b55c-d6a47e48f7fa",
        "HTTPStatusCode": 200,
        "HTTPHeaders": {
            "date": "Wed, 19 May 2021 18:45:32 GMT",
            "content-type": "application/x-amz-json-1.0",
            "content-length": "1066",
            "connection": "keep-alive",
            "x-amzn-requestid": "d8f4560f-6664-4dbc-b55c-d6a47e48f7fa",
        },
        "RetryAttempts": 0,
    },
}


client.list_services()

{
    "ServiceSummaryList": [
        {
            "ServiceName": "fernando",
            "ServiceId": "81367710325945d8b76f2f4389b2a76e",
            "ServiceArn": "arn:aws:apprunner:us-east-1:247276349155:service/fernando/81367710325945d8b76f2f4389b2a76e",
            "ServiceUrl": "6vsmqcryda.us-east-1.awsapprunner.com",
            "CreatedAt": datetime.datetime(2021, 5, 19, 11, 45, 32, tzinfo=tzlocal()),
            "UpdatedAt": datetime.datetime(2021, 5, 19, 11, 45, 32, tzinfo=tzlocal()),
            "Status": "CREATE_FAILED",
        }
    ],
    "ResponseMetadata": {
        "RequestId": "c6d45598-d20f-4994-9b1d-8c8f518b24a0",
        "HTTPStatusCode": 200,
        "HTTPHeaders": {
            "date": "Wed, 19 May 2021 18:51:33 GMT",
            "content-type": "application/x-amz-json-1.0",
            "content-length": "334",
            "connection": "keep-alive",
            "x-amzn-requestid": "c6d45598-d20f-4994-9b1d-8c8f518b24a0",
        },
        "RetryAttempts": 0,
    },
}

```


https://github.com/wellcomecollection/platform-infrastructure/blob/4b16beef44efbe8faa9a62f5459ab6f706e07032/builds/copy_docker_images_to_ecr.py