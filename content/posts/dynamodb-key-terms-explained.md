+++
Description = "Learn about all the DynamoDB jargon you'll need to know when you start interacting with the service."
Tags = [
  "Serverless",
  "DynamoDB",
  "AWS",
  "Amazon Web Services"
]
Categories = [
  "AWS",
]
title = "DynamoDB Jargon Explained - Every Key Term You need to Know about Amazon DynamoDB"
publishdate = "2018-12-17T12:11:10-08:00"
date = "2018-12-17T12:11:10-08:00"
[image]
    feature = "/images/dynamodb-jargon-explained/dynamodb.png"
+++

I regularly see questions related to the various bits of terminology surrounding DynamoDB. Specifically, questions always come up related to Primary Keys, Partition Keys, Sort Keys, and a bunch of the other names and types of each of them. I wanted to do my best to describe what each of these terms means and how they relate to one another.

I'll assume a little bit of DynamoDB knowledge going into this post. If you'd like to dive into this yourself I'd suggest getting started with the [DynamoDB Developer Guide](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html). So let's get started breaking apart all the jargon involved with AWS's Managed NoSQL offering.

<!--more-->

## Building Blocks - Tables, Items, Attributes

The first thing you need to know about DynamoDB is that it starts at the level of a **table**. There's no database, no instance, and no SSH credentials you need to worry about. You're interacting with the AWS table resource you create through HTTP APIs either through the AWS Console, the AWS SDKs or the AWS CLI. 

Inside the tables are **items**. These items are similar to rows in RDBMS systems and can contain one or many different values the same way a row might.

Those values inside of the items? Those are called **attributes** and can be several different data types (that you can look at more [here](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.NamingRulesDataTypes.html#HowItWorks.DataTypes)).

So tables contain items which contain attributes. Got it? Awesome! 

Don't know yet? Here's a visualization that I hope will help. You can also refer back to it from the next sections too!

<center>
![DynamoDB diagram showing tables, items, attributes and other concepts](/images/dynamodb-jargon-explained/dynamodb-concepts-viz.png)
</center>

## Primary keys and their ilk 

So now that we we know how the building blocks of DynamoDB tables are formed how do we actually differentiate between these items in a table? Where that's where primary keys come in.

### Types of Primary Keys 

Now, the key difference (pun intentional) between DynamoDB and some other systems, is that the table's items will each have a unique **primary key**. You can't change an item's primary key - it is literally used to differentiate the item from another. You also can't have two items with the same primary key. Whenever you're writing a new item in a table - it needs a unique primary key. Otherwise you'll just end up changing an existing item.

So what is a primary key? Well, it's just an attribute (or two!) that you select when creating the table. 

For example, if you have a `customers` table maybe you'd have a `customer_id` attribute as your primary key. A primary key with a single attribute is called a **simple primary key**. 

But hold on, I said we could also have two attributes in a primary key. For example, maybe we have a `comments` table that we want to use to record information about social media posts. We might have a `post_id` and a `timestamp` make up the primary key and each of these items might be a particular comment by some user on some post. This would be a "composite primary key".

Well why might we do this? Let's take a closer look at that by looking at what the attributes inside these primary keys are called and what they're used for.

### Partition/Hash Keys and Sort/Range Keys,

First off, I'll admit that the naming convention here is more than a bit confusing for beginners. Stay with me!

When we're creating a DynamoDB table, you will ALWAYS have a **partition key** attribute. The reason for this is because DynamoDB tables will always need to be partitioned to write/read the data you store in them. Partition key attributes are also known as **hash keys** or **hash key attributes** because when an item is stored in a partition, the partition/hash key attribute is hashed using a hashing algorithm and used to store the data in a partition based on that hash. In the examples above, the partition key attribute is `customer_id` or the `post_id`, respectively.

In addition to this partition key attribute we might also have a **sort key** attribute. When sort keys are used, the partition key determines which partition an item is send to and the sort key determines the sorting of items within a partition. This allows you to make efficient queries using both the partition key and the sort key. In the second example above, the sort key is the `timestamp` attribute. Sort keys are also called **range attributes** or **range keys**.

## Wrapping up

So in short:

- DynamoDB tables can have two types of primary keys for their items:
    - Simple Primary Keys - A 1-attribute key of a Partition/Hash attribute
    - Composite Primary Keys - A 2-attribute key with a Partition/Hash attribute and a Sort/Range attribute

Hopefully, this helps clear up a lot of confusion I frequently see around this terminology. 

Still finding something confusing? Totally understandable. Ask about it in the comments below and I'll update this post to help future readers.
