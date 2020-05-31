+++
Description = "Ten practical examples of using Python and Boto3 to get data out of a DynamoDB table."
Tags = [
  "Architecture",
  "Serverless",
  "Python",
  "Boto3",
  "AWS",
  "DynamoDB",
  "Python3",
]
Categories = [
  "AWS",
]
title = "Ten Examples of Getting Data from DynamoDB with Python and Boto3"
publishdate = "2020-02-05T18:41:17-08:00"
date = "2020-02-05T18:41:17-08:00"
type = "blog"
sponsor_message = "Dynobase — accelerate DynamoDB workflows with data exploration, code generation, bookmarks and more."
sponsor_link = "https://dynobase.dev/"
[image]
    feature = "/images/python-dynamodb.png"
    postheader = "/images/python-header.png"
+++

I [recently wrote](/posts/eight-examples-of-fetching-data-from-dynamodb-with-node/) about using Node.js and the AWS SDK for JavaScript to get data from DynamoDB. In this post, I'll take you through how to do the same thing with Python and Boto3! We'll use both a DynamoDB client and a DynamoDB table resource in order to do many of the same read operations on the DynamoDB table. I hope this helps serve as a reference for you whenever you need to query DynamoDB with Python.

<!--more-->

## Setting Up - Creating the Table and Loading Data

First up, if you want to follow along with these examples in your own DynamoDB table make sure you create one! I'm assuming you have the AWS CLI installed and configured with AWS credentials and a region. But if you don't yet, make sure to try that first. You can review the instructions from the post I mentioned above, or you can quickly create your new DynamoDB table with the AWS CLI like this:

```bash
aws dynamodb create-table \
  --attribute-definitions \
    AttributeName=artist,AttributeType=S \
    AttributeName=song,AttributeType=S \
  --key-schema \
    AttributeName=artist,KeyType=HASH \
    AttributeName=song,KeyType=RANGE \
  --table-name basicSongsTable \
  --provisioned-throughput \
    ReadCapacityUnits=1,WriteCapacityUnits=1
```

But, since this is a Python post, maybe you want to do this in Python instead? Well then, first make sure you have the CLI installed and configured (because we get the credentials to interact with AWS from there) and then [install Boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/quickstart.html). Once that's done, you can run this code right from your Python interpreter:

```python
import boto3

dynamodb = boto3.client("dynamodb")

response = dynamodb.create_table(
  TableName="basicSongsTable",
  AttributeDefinitions=[
    {
      "AttributeName": "artist",
      "AttributeType": "S"
    },
    {
      "AttributeName": "song",
      "AttributeType": "S"
    }
  ],
  KeySchema=[
    {
      "AttributeName": "artist",
      "KeyType": "HASH"
    },
    {
      "AttributeName": "song",
      "KeyType": "RANGE"
    }
  ],
  ProvisionedThroughput={
    "ReadCapacityUnits": 1,
    "WriteCapacityUnits": 1
  }
)

print(response)
```

**Loading Data**

Now it's time to load in the Data into the table. To do this, save the [data file](https://gist.githubusercontent.com/fernando-mc/7773ca435afebf8b6f8ad020fed929c6/raw/fe1dcdae59a98c62bdd15d09557bc6fc0b42e9a2/data.json) locally in the same directory as your other code. Then you can run this script to load the data into DynamoDB:

```python
import boto3
import json

dynamodb = boto3.client('dynamodb')

def upload():
    with open('data.json', 'r') as datafile:
        records = json.load(datafile)
    for song in records:
        print(song)
        item = {
                'artist':{'S':song['artist']},
                'song':{'S':song['song']},
                'id':{'S': song['id']},
                'priceUsdCents':{'S': str(song['priceUsdCents'])},
                'publisher':{'S': song['publisher']}
        }
        print(item)
        response = dynamodb.put_item(
            TableName='basicSongsTable', 
            Item=item
        )
        print("UPLOADING ITEM")
        print(response)

upload()
```

This should load all the data into your new table. It might take a moment to finish but when it's done you can start querying!

## Creating DynamoDB Client and Table Resources

There are two main ways to use Boto3 to interact with DynamoDB. The first is called a DynamoDB *Client*. That's what I used in the above code to create the DynamoDB table and to load the data in. But there is also something called a DynamoDB Table *resource*. This table resource can dramatically simplify some operations so it's useful to know how the DynamoDB client and table resource differ so you can use either of them to fit your needs. In the examples below, I'll be showing you how to use both!

First thing, run some imports in your code to setup using both the boto3 client and table resource. You'll notice I load in the DynamoDB conditions `Key` below. We'll use that when we work with our table resource. Make sure you run this code before any of the examples below.

```python
import boto3
from boto3.dynamodb.conditions import Key

TABLE_NAME = "basicSongsTable"

# Creating the DynamoDB Client
dynamodb_client = boto3.client('dynamodb', region_name="us-east-1")

# Creating the DynamoDB Table Resource
dynamodb = boto3.resource('dynamodb', region_name="us-east-1")
table = dynamodb.Table(TABLE_NAME)
```

You'll notice that I've created both the DynamoDB client we'll use in `dynamodb_client` and the DynamoDB Table Resource in `table`. Both of these can allow us to do many of the same operations to get data from the table. But we'll use them differently in order to do so. Let's take a look!

### #1 - Get a Single Item with the DynamoDB Client

Below, we're getting a Single Item from the DynamoDB table using the `get_item()` operation. Let's do this with the DynamoDB client first:

```python
# Use the DynamoDB client get item method to get a single item
response = dynamodb_client.get_item(
    TableName=TABLE_NAME,
    Key={
        'artist': {'S': 'Arturus Ardvarkian'},
        'song': {'S': 'Carrot Eton'}
    }
)
print(response['Item'])

# The client's response looks like this:
# {
#  'artist': {'S': 'Arturus Ardvarkian'},
#  'id': {'S': 'dbea9bd8-fe1f-478a-a98a-5b46d481cf57'},
#  'priceUsdCents': {'S': '161'},
#  'publisher': {'S': 'MUSICMAN INC'},
#  'song': {'S': 'Carrot Eton'}
# }
```

Note that with the DynamoDB client we get back the type attributes with the result. For example, we know that the `'artist'` is a String because the dictionary object is: `{'S': 'Arturus Ardvarkian'}`. The `S` indicates that the value inside is a string type.

### #2 - Get a Single Item with the DynamoDB Table Resource

Now let's see what it looks like to use the DynamoDB table resource:

```python
# Use the DynamoDB Table resource get item method to get a single item
response = table.get_item(
    Key={
        'artist': 'Arturus Ardvarkian',
        'song': 'Carrot Eton'
    }
)
print(response['Item'])

# The Table resource's response looks like this:
# {
#  'artist': 'Arturus Ardvarkian',
#  'id': 'dbea9bd8-fe1f-478a-a98a-5b46d481cf57',
#  'priceUsdCents': '161',
#  'publisher': 'MUSICMAN INC',
#  'song': 'Carrot Eton'
# }
```

With the Table resource we get back the native Python types without that additional explanation. The dictionary only has the names of the attributes and their values: `artist` is `'Arturus Ardvarkian'` which is a string.

But imagine if we had a number in the data above. Say we added a 'rating' value that for this song was the number 10. This rating would be returned as `'rating': {'N': '10'}` by the DynamoDB Client and `'rating': Decimal(10)` by the table resource. This means we'd have to load the Python `decimal` library to interact with it. For this reason, and many other reasons related to floating point arithmetic and currency manipulation I find it easiest to store numeric values as strings without decimals and then convert them to the appropriate value later on. For example, the `priceUsdCents` value is stored as a string without any decimal point in it. That means I can just do some string manipulation on the frontend to get it to a dollar value.

### #3 - Use the DynamoDB Client to Query for Items Matching a Partition Key

Below, we're querying the DynamoDB table for any items where the partition key is equal to the artist name of "Arturs Ardvarkian".

```python
# Use the DynamoDB client to query for all songs by artist Arturus Ardvarkian
response = dynamodb_client.query(
    TableName=TABLE_NAME,
    KeyConditionExpression='artist = :artist',
    ExpressionAttributeValues={
        ':artist': {'S': 'Arturus Ardvarkian'}
    }
)
print(response['Items'])
```

Using the DynamoDB client, we need to set a `KeyConditionExpression` that determines what we're looking for. In the case above, it shows us that we are querying based on the key of `artist` and that the artist attribute value is `:artist`. The colon syntax is a reference that allows us to specify a variable stored in the `ExpressionAttributeValues` portion of this query. It must specify the type of the attribute (`'S'` for string in this case) and the value itself `Arturus Ardvarkian`.

### #4 - Use the DynamoDB Table Resource to Query for Items Matching a Partition Key

Now let's look at the same operation using the table resource.

```python
# Use the Table resource to query for all songs by artist Arturus Ardvarkian
response = table.query(
  KeyConditionExpression=Key('artist').eq('Arturus Ardvarkian')
)
print(response['Items'])
```

You'll notice that the Table resource ends up having a bit more compact of a syntax using the `Key`. This lets us specify the key of the table directly and set it equal to something using `eq()`. It's also able to save us from specifying the name of the table every time we run a query which can be very useful. We do have to keep in mind that if we're working with multiple tables we have to be more explicit in naming our table resources or else we end up wondering what table we're interacting with when we run `table.query()`.

Also, just as with the earlier single item result, we see that the table resource returns items without using the attribute type syntax that is returned by the DynamoDB client. This makes for a much more condensed response and avoids you having to reference the type/value objects of each attribute.

### #5 Querying with Partition and Sort Keys Using the DynamoDB Client

Next, let's see how to integrate a sort key into our query patterns. We can use the same artist as before but in this case we want to find only songs that match a sort key beginning with the letter "C". To do this, we can use the DynamoDB `begins_with` operator. However, depending on which way we're running this query we may need to set up that operator differently. 

For example, when using the DynamoDB client we would specify it inside the KeyConditionExpression string:

```python
# Use the DynamoDB client query method to get songs by artist Arturus Ardvarkian
# that start with "C"
response = dynamodb_client.query(
    TableName=TABLE_NAME,
    KeyConditionExpression='artist = :artist AND begins_with ( song , :song )',
    ExpressionAttributeValues={
        ':artist': {'S': 'Arturus Ardvarkian'},
        ':song': {'S': 'C'}
    }
)
print(response['Items'])
```

In this case we use the `KeyConditionExpression` to setup the query conditions (searching for the `artist` value and using the song to filter when it begins with a "C"). When using the DynamoDB client, we have to define the `ExpressionAttributeValues` with both the type and value in order to use them in the `KeyConditionExpression`. The `song` and the `artist` in the `KeyConditionExpression` are the literal name of the attributes of the table we're interacting with. Whereas the `:song` and the `:artist` are references to the `ExpressionAttributeValues` we define.

We're also using a few operators here. First, the `=` is used as you'd expect - to assert an equality between our `artist` partition key and the value stored in `:artist` of `{'S': 'Arturus Ardvarkian'}`. Then, `AND` allows us to chain an additional requirement in. We use the `begins_with` condition to say that we want `song` to begin with the value stored in `:song` - `{'S': 'C'}`.

There are lot of other operators we could use including `<`, `<=`, `>`, `>=`. These work exactly as you'd expect them to with any number data types. With string values, they will sort based on the [ASCII character code values](https://en.wikipedia.org/wiki/ASCII#ASCII_printable_characters).

### #6 Using `<` (The less than operator) with the DynamoDB Client

Here is an example of using the `<` operator: 

```python
response = dynamodb_client.query(
    TableName=TABLE_NAME,
    KeyConditionExpression='artist = :artist AND song < :song',
    ExpressionAttributeValues={
        ':artist': {'S': 'Arturus Ardvarkian'},
        ':song': {'S': 'C'}
    }
)
print(response['Items'])
```

When we do this, the value of the `song` in all returned items will be less than the value contained by `:song`. In this case, because we're working with string attributes, we'll get song titles back like `{'S': 'Bright Cerulean'}` or `{'S': 'Bleu Cinnamon'}`. Both of these values start with "B", which has an ASCII Decimal Character code of 66, whereas "C" has a character code of 67. This is why they are technically "Less than" something starting with "C". Keep in mind that the ASCII character codes for uppercase letters like "B" and lowercase letters like "b" are different!

### #7 Using the `BETWEEN` operator with the DynamoDB Client

We can also use the `BETWEEN` operator to specify that the returned items must have a sort key between two values. For example:

```python
# Use the DynamoDB client query method to get songs by artist Arturus Ardvarkian
# that have a song attribute value BETWEEN 'D' and 'Bz'
response = dynamodb_client.query(
    TableName=TABLE_NAME,
    KeyConditionExpression='artist = :artist AND song BETWEEN :songval1 AND :songval2',
    ExpressionAttributeValues={
        ':artist': {'S': 'Arturus Ardvarkian'},
        ':songval1': {'S': 'Bz'},
        ':songval2': {'S': 'D'}
    }
)
print(response['Items'])
```

This query will return items with song values after 'Bz' and before 'D' which because of the ASCII character code order ends up being song attribute values that start with 'C' like `{'S': 'Cadet Celadon'}` and `{'S': 'Carnelian Cobalt'}`. If we used "B" instead of "Bz" for `:songval1` then we would end up with song titles starting with "B" too.

### #8 Querying with Partition and Sort Keys Using the DynamoDB Table Resource

Now let's take a look at how we'd do some similar things with the DynamoDB table resource. Let's look at how to do the same things we just did with the DynamoDB client. First, let's look for songs starting with "C". We used `begins_with` in the `KeyConditionExpression` with the DynamoDB client, but with the table resource we use a slightly different `KeyConditionExpression`. 

Rather than passing the table resource's `KeyConditionExpression` a string, we use two of the `Key` objects we used previously with it. Except, instead of using the `eq()` method (which we could if we wanted to query for a specific item), we use `begins_with()`. Take a look:

```python
# Use the Table resource to query all songs by artist Arturus Ardvarkian 
# that start with 'C'
response = table.query(
  KeyConditionExpression=Key('artist').eq('Arturus Ardvarkian') & Key('song').begins_with('C')
)
print(response['Items'])
```

This creates a compound `KeyConditionExpression` for us that results in getting the same items as when we used `begins_with` in the DynamoDB client. One key difference is that with the table resource, we get the results back without attribute values. 

Overall, this `Key` object syntax can be a bit less verbose and save you time by converting data to native Python types for you automatically. However, one thing that can be somewhat confusing is wondering what the bitwise and operator `&` is doing in the `KeyConditionExpression` above. Well, when you take the result of `&`ing two `Key`s you get a `boto3.dynamodb.conditions.And` object that is actually passed to the `KeyConditionExpression` and evaluated by DynamoDB. In this context, it is probably just easier to think of it as "and this other condition must also be true" rather than "let's take the bitwise result of the two `Key` objects".

### #9 Using `lt()` - The less than method of `Key` with the DynamoDB Table Resource

When using the table resource, we'll use named operators instead of one of these: `<`, `<=`, `>`, `>=`.

These operators are:

- `gt` which is equivalent to `>` - ("Greater Than")
- `gte` which is equivalent to `>=` - ("Greater Than or Equal To")
- `lt` which is equivalent to `<` - ("Less Than")
- `lte` which is equivalent to `<=` - ("Less Than or Equal To")

Let's follow the same earlier example and try to get values back with a `song` that is less than 'C':

```python
# Use the Table resource to query all songs by artist Arturus Ardvarkian 
# that are less than 'C'
response = table.query(
  KeyConditionExpression=Key('artist').eq('Arturus Ardvarkian') & Key('song').lt('C')
)
print(response['Items'])
```

This would result in the same items as the earlier query with the DynamoDB client, again with the attributes automatically put in native Python types.

### #10 Using the `between()` Method of `Key` with the DynamoDB Table Resource

We can also still use `between` and expect the same sort of response with native Python types. The difference here is that `between` is a method of `Key` not a string to include in the `KeyConditionExpression`. Here's how we'd do it to get `song` values between 'Bz' and 'D' (ones that probably start with 'C'):

```python
# Use the Table resource to query all songs by artist Arturus Ardvarkian 
# that are between 'Bz' and 'D'
response = table.query(
  KeyConditionExpression=Key('artist').eq('Arturus Ardvarkian') & Key('song').between('Bz', 'D')
)
print(response['Items'])
```

If you'd like to review the full reference for how to use the `Key` comparators you can take a look [here](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/customizations/dynamodb.html#ref-dynamodb-conditions).

## What's Next?

These are just a few examples of how you might use either the DynamoDB table service resource or the DynamoDB Client and boto3 but there's a variety of others! Are you curious how to write items to the table with them both? How about fetching batches of items from the table? What about conditionally writing items to a table? Or writing multiple items at the same time guaranteed? There's a lot you can do with DynamoDB! If you're curious about how you might do these things then [sign up for my mailing list](/mailing-list)! I'll keep you posted on my latest guides and tutorials and you can shoot me a reply to let me know what you'd like me to cover next!
