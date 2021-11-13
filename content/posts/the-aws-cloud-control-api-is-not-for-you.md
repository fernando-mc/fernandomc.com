+++
Description = "A look at the new AWS Cloud Control API and who should use it."
Tags = [
  "Architecture",
  "AWS",
  "Cloud",
]
Categories = [
  "Architecture",
  "Infrastructure as Code",
  "AWS",
]
title = "The AWS Cloud Control API Is Not for You"
publishdate = "2021-11-11T13:56:08-07:00"
date = "2021-11-11T13:56:08-07:00"
type = "blog"
[image]
    feature = "/images/the-aws-cloud-control-api-is-not-for-you/og-image.png"
    postheader = "/images/abstract-og-2.png"
+++

Forget the hype, the new AWS [Cloud Control API](https://aws.amazon.com/blogs/aws/announcing-aws-cloud-control-api/) is not the infrastructure as code magic bullet you're looking for.

Every AWS cloud engineer dreams of the day they can call the same API across any cloud resource to create their beautiful infrastructure seamlessly. This is because AWS has a terrible record of keeping API calls that would *seem* to do the same thing named consistently. If you're looking for a solution to this out of the Cloud Control API then you're looking in the wrong place. Let's take a look at who really benefits from this new API, in general how it's used, and some of the context around it.

<!--more-->

Imagine that you want to see the tags on an AWS resource which of these API methods would you use?

- `list_tags_for_resource()`
- `describe_tags()`
- `get_tags()`
- `list_tags()`

Unless your answer was "it depends" you are probably wrong! Because these are all valid ways to get tags on [specific](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/sns.html#SNS.Client.list_tags_for_resource) [distinct](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/alexaforbusiness.html#AlexaForBusiness.Client.list_tags) [resources](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/resource-groups.html#ResourceGroups.Client.get_tags). 

And this is just to get tags! Now try and create resources and you'll end up with a similar nightmare of checking the docs over and over to find out of you need to find `create_thing()`, `launch_thing()`, `deploy_thing()`, or some combination of all the above. AWS readily admits this as part of the reason prompting this new launch. 

When I first saw the announcement, my naive hope was that the Cloud Control API would help standardize some of the same method verbs we've been dealing with for a long time and provide a nice new tool that could go into my infrastructure as code toolkit. Before this, you were probably using some combination of Terraform, [CloudFormation](https://aws.amazon.com/cloudformation/), or the [AWS CDK](https://aws.amazon.com/cdk/) to manage your infrastructure.

With the introduction of the Cloud Control API, you can create, get, and delete resources with a standardized API and then get some JSON back that you can parse rather than working with the existing methods within your SDK of choice. You can also use an idempotency token with these requests to keep tabs on the status of the update.

## Creating a Resource with the Cloud Control API

Lets's try creating a new S3 Bucket using the AWS CLI and the Cloud Control API. You could use one of the AWS SDKs with the CloudControl API too, but we'll stick to the CLI in this example.

Every creation uses the same structure but requires a distinct resource type and desired state. We can also use an idempotency token called "client-token" to make sure that we're not running multiple creation requests:

```bash
aws cloudcontrol create-resource \
--type-name AWS::S3::Bucket \
--desired-state "{"BucketName": "newcloudcontroltestbucket123"}" \
--client-token my-unique-request-token-123
```

The type name you see above is the same value that you'd see when creating a resource from CloudFormation. Additionally, the desired state JSON should contain the same properties that you would use when working with CloudFormation. After running the above command, we'll initially get a response like this:

```json
{
    "ProgressEvent": {
        "TypeName": "AWS::S3::Bucket",
        "Identifier": "newcloudcontroltestbucket123",
        "RequestToken": "cce38983-d0b0-40f0-91d1-1fc484622e70",
        "Operation": "CREATE",
        "OperationStatus": "IN_PROGRESS",
        "EventTime": "2021-11-12T18:08:06.949000-08:00"
    }
}
```

Then, we can rerun the same command from earlier until the OperationStatus is updated with a `SUCCESS` response:

```json
{
    "ProgressEvent": {
        "TypeName": "AWS::S3::Bucket",
        "Identifier": "newcloudcontroltestbucket123",
        "RequestToken": "cce38983-d0b0-40f0-91d1-1fc484622e70",
        "Operation": "CREATE",
        "OperationStatus": "SUCCESS",
        "EventTime": "2021-11-12T18:08:37.559000-08:00"
    }
}
```

Next, if we want to see the details of the resource we created (or another resource), we can run the `get-resource` command. This command will take the `type-name` for the resource. The type name is the same type we mentioned earlier - the CloudFormation type. It also requires an `identifier` which will vary depending on the resource but in this case is the `BucketName` value we passed in earlier.

Here's the full command:

```bash
aws cloudcontrol get-resource \
--type-name AWS::S3::Bucket \
--identifier newcloudcontroltestbucket123
```

Notice that with the above request, we don't have to include idempotency tokens because a `get-resource` action inherently shouldn't change the resource. The result from the above is:

```json
{
    "TypeName": "AWS::S3::Bucket",
    "ResourceDescription": {
        "Identifier": "newcloudcontroltestbucket123",
        "Properties": "{\"BucketName\":\"newcloudcontroltestbucket123\",\"RegionalDomainName\":\"newcloudcontroltestbucket123.s3.us-east-1.amazonaws.com\",\"DomainName\":\"newcloudcontroltestbucket123.s3.amazonaws.com\",\"WebsiteURL\":\"http://newcloudcontroltestbucket123.s3-website-us-east-1.amazonaws.com\",\"DualStackDomainName\":\"newcloudcontroltestbucket123.s3.dualstack.us-east-1.amazonaws.com\",\"Arn\":\"arn:aws:s3:::newcloudcontroltestbucket123\"}"
    }
}
```

I also noticed when running the request that it took several seconds longer than a typical AWS CLI command. Most likely, this is because this process of getting basic data about an S3 bucket runs several distinct AWS CLI commands to describe different attributes of an S3 bucket to get all this information. This can either be a positive (in that you don't have to run these commands yourself individually) or a drawback (in that the command to get basic information in this case takes much longer to retrieve all the data). 

Finally, if we want to delete the S3 bucket we can run a `delete-resource` command which will also require the type name and the identifier. It will also take the optional, but highly recommended `client-token` to avoid duplicate operations:

```bash
aws cloudcontrol delete-resource \
--type-name AWS::S3::Bucket \
--identifier newcloudcontroltestbucket123 \
--client-token my-unique-delete-request-123
```

We'll get this as the initial response:

```json
{
    "ProgressEvent": {
        "TypeName": "AWS::S3::Bucket",
        "Identifier": "newcloudcontroltestbucket123",
        "RequestToken": "bbe1b7ff-c298-4fdd-98be-dd3833ea829c",
        "Operation": "DELETE",
        "OperationStatus": "IN_PROGRESS",
        "EventTime": "2021-11-12T18:44:06.795000-08:00"
    }
}
```

And then eventually get this when the process is completed after rerunning this command with the same idempotency command from the first delete request:

```json
{
    "ProgressEvent": {
        "TypeName": "AWS::S3::Bucket",
        "Identifier": "newcloudcontroltestbucket123",
        "RequestToken": "bbe1b7ff-c298-4fdd-98be-dd3833ea829c",
        "Operation": "DELETE",
        "OperationStatus": "SUCCESS",
        "EventTime": "2021-11-12T18:44:07.519000-08:00"
    }
}
```

And that's a full cycle here!

## Who Is this Useful For?

So now that we've seen a minimal example on one specific resource let's think about who this is useful for. 

If you're trying to manage your infrastructure in a development project will you be using this command line utility or using it through one of the SDKs? I don't think so, and AWS [doesn't seem to either](https://aws.amazon.com/blogs/aws/announcing-aws-cloud-control-api/) given how they describe it's aimed at organizations like HashiCorp, Pulumi, and organizations that have similar infrastructure tooling level needs.

For most folks, we probably fall into the "third type of builders that will benefit from Cloud Control API" by "using solution such as Terraform or Pulumi". Basically saying we wont really be using it, but we can use those providers better now that they'll be able to more rapidly create infrastructure as code tooling for us faster to the release of new services.

## Will We Use it?

In theory, the AWS Cloud Control API is kind of neat! 

In practice, you're not going to start changing your existing CloudFormation or Terraform templates. You're just going to be happy that Terraform supports AWS NewServiceY on launch day. 

Will you use it? No... Unless you're at a company that makes a development tool that touches on the infrastructure as code space. 

But prove me wrong please!

## What Did We, in Our Naivety Dream of?

Rather than this interesting amalgam on top of existing AWS APIs what did I dream was in this release?

Well, in some vibrant future where a new (but still backwards-compatible!) version of the AWS CLI exists, we could imagine AWS SDKs and APIs that allowed us to run standardized commands for similar processes across all resource types. 

From my tags example from earlier, what if 95% of the disparate commands like this were reduced into one command (I don't care which one!):

- `list_tags_for_resource()`
- `describe_tags()`
- `get_tags()`
- `list_tags()`

Let's say we pick `get_tags()` because it's nice and short. Then we could run `get_tags()` on *any* resource and have it work from just a resource identifier. We could still let these old methods work for their particular resources! But could this then make for a much simpler learning curve with the AWS SDKs?

Or, if we were creating resources, we could just use a command that might work like this:

```python
service_xyx = boto3.client('service_xyx')
service_xyx.create_xyz(
  Identifier="myresourcename",
  OtherProperty1="Etc.",
  OtherProperty2=123,
)
```

In this case, we could imagine `xyx` as an SNS topic, DynamoDB table, EC2 Instance or anything else. The standardization here comes in two parts:

- The `Identifier` instead of `TableName`, `TopicName`, etc.
- The fact that we don't have to worry about what verb is required for the creation of a resource: it's just `create_noun()`

Of course, there are many places this sort of creation process breaks down and I have to admit this is a non-serious proposal given how much overhead a change like this would actually take. 

But, with the introduction of the Cloud Control API it is interesting to think about what you can accomplish with different kinds of standardization. 

Realistically, we'd end up with this...

![Standards](https://imgs.xkcd.com/comics/standards.png)
