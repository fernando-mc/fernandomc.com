+++
Description = "Eight examples of using Node.js to get data out of a DynamoDB table using the AWS SDK for JavaScript."
Tags = [
  "Serverless",
  "Node",
  "AWS",
  "DynamoDB",
]
Categories = [
  "AWS",
]
title = "Eight Examples of Fetching Data from DynamoDB with Node.js"
publishdate = "2020-01-29T18:41:17-08:00"
date = "2020-01-29T18:41:17-08:00"
type = "blog"
sponsor_message = "Dynobase — accelerate DynamoDB workflows with code generation, data exploration, bookmarks and more."
sponsor_link = "https://dynobase.dev/"
[image]
    feature = "/images/node-dynamodb.png"
    postheader = "/images/node-header.png"
+++

I frequently see people looking for simple examples of how to use one of AWS' SDKs to do simple operations on DynamoDB and other services. So I thought it was about time we had a few examples to work from that weren't completely overwhelming. In this post, I'll show you a few ways to use the AWS SDK for JavaScript to get data out of a DynamoDB table. I hope these will serve as a decent reference for many basic operations you might need to take to read information from your DynamoDB tables!

<!--more-->

# Getting Setup

I'm going to assume that you've already installed [Node.js](https://nodejs.org/en/download/) and both installed and configured the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) with your AWS access keys and default region. If you haven't yet, take a moment to do that first.

Also, go ahead and create a directory for this project where you'll download the sample data and install the AWS SDK for JavaScript using npm. You can install the AWS SDK for JavaScript in that directory with `npm install aws-sdk`.

## Downloading the Sample Data

If you want to try these examples on your own, you'll need to get the data that we'll be querying with. You can copy or download my [sample data](https://gist.githubusercontent.com/fernando-mc/7773ca435afebf8b6f8ad020fed929c6/raw/fe1dcdae59a98c62bdd15d09557bc6fc0b42e9a2/data.json) and save it locally somewhere as `data.json`. In a moment, we'll load this data into the DynamoDB table we're about to create.

## Creating a Table

Next, we need to create a DynamoDB table with the characteristics that will match this particular data set. You can do this in the AWS Console by creating a DynamoDB table with a primary key of `artist` and a sort key of `song`. You can also use either the AWS CLI or the AWS SDK for JavaScript.

Assuming you have the AWS CLI installed and configured you can use the following command:

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

Alternatively, you could use the AWS SDK for JavaScript to do the same thing:

```javascript
// Load the AWS SDK for JS
var AWS = require("aws-sdk");
AWS.config.update({region: "us-east-1"});

// -----------------------------------------
// Create the Service interface for dynamoDB
var dynamodb = new AWS.DynamoDB({apiVersion: "2012-08-10"});

var params = {
  AttributeDefinitions: [
    {
      AttributeName: "artist",
      AttributeType: "S"
    },
    {
      AttributeName: "song",
      AttributeType: "S"
    }
  ],
  KeySchema: [
    {
      AttributeName: "artist",
      KeyType: "HASH"
    },
    {
      AttributeName: "song",
      KeyType: "RANGE"
    }
  ],
  ProvisionedThroughput: {
    ReadCapacityUnits: 1,
    WriteCapacityUnits: 1
  },
  TableName: "basicSongsTable"
};

// Create the table.
dynamodb.createTable(params, function(err, data) {
  if (err) {
    console.log("Error", err);
  } else {
    console.log("Table Created", data);
  }
});
```

If your request succeeds you should have a brand new DynamoDB table! You can check the DynamoDB console or run a command like `aws dynamodb list-tables` to see if the table exists after you create it.

## Loading Table Data 

Now that your table is created, we can load some data into it. I'm assuming you already saved the `data.json` file locally in the same directory as you installed the `aws-sdk`.

You can run this script to load the data into DynamoDB:

```js

// Load the AWS SDK for JS
var AWS = require("aws-sdk");
var fs = require("fs");
AWS.config.update({region: "us-east-1"});

// -----------------------------------------
// Create the document client interface for DynamoDB
var documentClient = new AWS.DynamoDB.DocumentClient();

console.log("Loading song data into DynamoDB");

var songData = JSON.parse(fs.readFileSync('data.json', 'utf8'));
songData.forEach(function(song) {
  var params = {
    TableName: "basicSongsTable",
    Item: {
      "artist":  song.artist,
      "song": song.song,
      "id":  song.id,
      "priceUsdCents": song.priceUsdCents,
      "publisher": song.publisher
    }
  };

  documentClient.put(params, function(err, data) {
    if (err) {
      console.error("Can't add song. Darn. Well I guess Fernando needs to write better scripts.");
    } else {
      console.log("Succeeded adding an item for this song: ", song.song);
    }
  });
});
```

Even with a very limited capacity on the table, you should see it load up pretty quickly.

## The DynamoDB Service Interface and Document Client

Before I show you examples, be aware that I'll show you two different methods for interacting with a DynamoDB table using the AWS SDK for JavaScript - The Service interface and the Document Client interface. The Service interface has an extensive set of operations and specificity you can use but the Document Client makes it a bit easier to interact with data without having to handle some DynamoDB-specific details like data type descriptors (more on these in a moment). 

Let's see how to create these different interfaces:

```js
// Load the AWS SDK for JS
var AWS = require("aws-sdk");

// Set a region to interact with (make sure it's the same as the region of your table)
AWS.config.update({region: 'us-east-1'});

// Set a table name that we can use later on
const tableName = "basicSongsTable"

// Create the Service interface for DynamoDB
var dynamodb = new AWS.DynamoDB({apiVersion: '2012-08-10'});

// Create the Document Client interface for DynamoDB
var ddbDocumentClient = new AWS.DynamoDB.DocumentClient();
```

Make sure to run the above code before working with any of the code samples below. Not only will it create the two different interfaces, it will also create the `tableName` variable for us to use throughout the examples. So, with this all setup for us, let's start by looking at how we can work with the DynamoDB service interface.

## Interacting with DynamoDB Using the DynamoDB Service Interface

To start working with the service interface we need to understand that it will always require us to specify DynamoDB attributes using data type descriptors. This means, that when we want to query DynamoDB we need to provide it with an object that contains both the type descriptor and the value of a queryable attribute. For example, if we want to find an item with a partition key called `id` that is a string type with a value of: `123456`, we need to provide the service interface an object like this: 

`"id": {"S": "123456"}`

Later, we'll simplify this process with the Document Client. But now, let's take a look at some examples!

### #1 Getting a Single Item with the DynamoDB Service Interface

In order to make it easy to see the results of my queries with node I've written all of them as async functions that include a console.log() of the result of the operation. You can just as easily not do this. The important thing to pay attention to in any of the examples in this post are the contents of the `params` passed to the different methods of the DynamoDB service interface or the DynamoDB Document Client interface.

```js
// Get a single item with the getItem operation
async function logSingleItem(){
    try {
        var params = {
            Key: {
             "artist": {"S": "Arturus Ardvarkian"}, 
             "song": {"S": "Carrot Eton"}
            }, 
            TableName: tableName
        };
        var result = await dynamodb.getItem(params).promise()
        console.log(JSON.stringify(result))
    } catch (error) {
        console.error(error);
    }
}
logSingleItem()

// { 
//    "Item":{ 
//       "priceUsdCents":{ 
//          "N":"161"
//       },
//       "artist":{ 
//          "S":"Arturus Ardvarkian"
//       },
//       "song":{ 
//          "S":"Carrot Eton"
//       },
//       "publisher":{ 
//          "S":"MUSICMAN INC"
//       },
//       "id":{ 
//          "S":"dbea9bd8-fe1f-478a-a98a-5b46d481cf57"
//       }
//    }
// }

```

The result of this request is an object with an `Item` attribute containing data for a single item. Inside of that item there are different attributes that are each described by a data type descriptor of "S" or "N" for strings or numbers, respectively.

### #2 Querying for All Songs by Artist with the DynamoDB Service Interface

```js
// Use the query operation to get all song by artist Arturus Ardvarkian
async function logSongsByArtist(){
    try {
        var params = {
            KeyConditionExpression: 'artist = :artist',
            ExpressionAttributeValues: {
                ':artist': {'S': 'Arturus Ardvarkian'}
            },
            TableName: tableName
        };
        var result = await dynamodb.query(params).promise()
        console.log(JSON.stringify(result))
    } catch (error) {
        console.error(error);
    }
}
logSongsByArtist()

// { 
//    "Items":[ 
//       { 
//          "priceUsdCents":{ 
//             "N":"312"
//          },
//          "artist":{ 
//             "S":"Arturus Ardvarkian"
//          },
//          "song":{ 
//             "S":"Baker Firebrick"
//          },
//          "publisher":{ 
//             "S":"MUSICMAN INC"
//          },
//          "id":{ 
//             "S":"1a4e5bc5-4fa3-4b37-9d36-e15dc9ab6b21"
//          }
//       },
//       ...
//    ],
//    "Count":9,
//    "ScannedCount":9
// }
```

In this case, we returned an object with multiple `Items` in it this time. Notice that the params we used in this query contained a `KeyConditionExpression` that was looking for the artist property of `artist` to match the value of `:artist` stored in the `ExpressionAttributeValues`. In this case, the value was an object that contains the attribute type descriptor and the value: 

`{':artist': {'S': 'Arturus Ardvarkian'}`

When working with the service interface we'll need to make sure to specify the value in this way. We'll look at `KeyConditionExpression`s a bit more in the next queries.

### #3 Using the DynamoDB Service Interface and `begins_with` to Find an Artist's Songs that Start with "C" 

If we want to add another condition to our query we can do so with the sort key operators. One of these is `begins_with`. We essentially extend the `KeyConditionExpression` from the simple equality operator we were using on the `artist` partition key and add in a new operator: `begins_with`. We also then need to create the value that we want the sort key to start with in the `ExpressionAttributeValues`:

```js
// Query songs by artist "Arturus Ardvarkian" that start with "C"
async function logArtistSongsStartingWithC(){
    try {
        var params = {
            KeyConditionExpression: 'artist = :artist AND begins_with ( song , :song )',
            ExpressionAttributeValues: {
                ':artist': {'S': 'Arturus Ardvarkian'},
                ':song': {'S': 'C'}
            },
            TableName: tableName
        };
        var result = await dynamodb.query(params).promise()
        console.log(JSON.stringify(result))
    } catch (error) {
        console.error(error);
    }
}
logArtistSongsStartingWithC()

// { 
//    "Items":[ 
//       { 
//          "priceUsdCents":{ 
//             "N":"142"
//          },
//          "artist":{ 
//             "S":"Arturus Ardvarkian"
//          },
//          "song":{ 
//             "S":"Cadet Celadon"
//          },
//          "publisher":{ 
//             "S":"MUSICMAN INC"
//          },
//          "id":{ 
//             "S":"fd7667cb-3a41-4777-93bb-ed2d0d8d7458"
//          }
//       },
//       ...
//    ],
//    "Count":4,
//    "ScannedCount":4
// }

```

Again, we have multiple items returned back to us. This time, the `song` attribute starts with a "C" for all the items.

### #4 Using the DynamoDB Service Interface to Scan the DynamoDB Table

In general, DynamoDB table scans are not efficient operations. However, when we don't care what items we get back or when we have a need to get all the data out of the table and don't want to use other options we can use the `scan` operation. Here's an example of how:

```js
// Use the DynamoDB client scan operation to retrieve all items of the table
async function scanForResults(){
    try {
        var params = {
            TableName: tableName
        };
        var result = await dynamodb.scan(params).promise()
        console.log(JSON.stringify(result))
    } catch (error) {
        console.error(error);
    }
}
scanForResults()

// { 
//    "Items":[ 
//       { 
//          "priceUsdCents":{ 
//             "N":"207"
//          },
//          "artist":{ 
//             "S":"Marguerite Mcclure"
//          },
//          "song":{ 
//             "S":"Cedar Columbia"
//          },
//          "publisher":{ 
//             "S":"TELEQUIET"
//          },
//          "id":{ 
//             "S":"4e01c867-3084-4ae4-9c8a-5b0750465037"
//          }
//       },
//       ...
//    ],
//    "Count":70,
//    "ScannedCount":70
// }
```

In this case, our table was small enough to return all the items in the table. However, if we had a larger DynamoDB table or larger items we might hit the limit of data we can get back in a single call. In that case, we would also get back a value for where to continue the scan operation if we were iterating over all the table data. 

## Interacting with DynamoDB Using the DynamoDB Document Client

Now it's time to switch over to using the DynamoDB Document Client. In all the examples above you got used to seeing values sent in and returned using DynamoDB Data Type Descriptors like "S" and "N" and then the value of the attribute following that. Now, we're going to look at how to abstract those descriptors away using the DynamoDB Document Client.

### #5 Using the Document Client to Get a Single Item from DynamoDB

Let's start by doing essentially the same thing as our first example and getting a single item form DynamoDB.

```js
// Get a single item with the getItem operation and Document Client
async function logSingleItemDdbDc(){
    try {
        var params = {
            Key: {
             "artist": "Arturus Ardvarkian", 
             "song": "Carrot Eton"
            }, 
            TableName: tableName
        };
        var result = await ddbDocumentClient.get(params).promise()
        console.log(JSON.stringify(result))
    } catch (error) {
        console.error(error);
    }
}
logSingleItemDdbDc()

// { 
//    "Item":{ 
//       "priceUsdCents":161,
//       "artist":"Arturus Ardvarkian",
//       "song":"Carrot Eton",
//       "publisher":"MUSICMAN INC",
//       "id":"dbea9bd8-fe1f-478a-a98a-5b46d481cf57"
//    }
// }
```

Notice that this result does not contain any of the Data Type Descriptors like "S" and "N". Instead, the document client automatically converts the data into native JavaScript types to make our lives easier. If you compare it to the first example, you'll also notice we're not including Data Type Descriptors to the params in the first place. This is one of the ways the Document Client can simplify things for us.

### #6 Querying for All Songs by Artist with the DynamoDB Document Client

In this example, you'll see that we keep the simple syntax for creating our params. We can just set the `:artist` in `ExpressionAttributeValues` equal to `'Arturus Ardvarkian'` rather than to:

`':artist': {'S': 'Arturus Ardvarkian'}`

```js
// Query all songs by artist Arturus Ardvarkian with the Document Client
async function logSongsByArtistDdbDc(){
    try {
        var params = {
            KeyConditionExpression: 'artist = :artist',
            ExpressionAttributeValues: {
                ':artist': 'Arturus Ardvarkian'
            },
            TableName: tableName
        };
        var result = await ddbDocumentClient.query(params).promise()
        console.log(JSON.stringify(result))
    } catch (error) {
        console.error(error);
    }
}
logSongsByArtistDdbDc()

// { 
//    "Items":[ 
//       { 
//          "priceUsdCents":312,
//          "artist":"Arturus Ardvarkian",
//          "song":"Baker Firebrick",
//          "publisher":"MUSICMAN INC",
//          "id":"1a4e5bc5-4fa3-4b37-9d36-e15dc9ab6b21"
//       },
//       ...
//    ],
//    "Count":9,
//    "ScannedCount":9
// }
```

The same as with any document client queries, the results are returned without data type descriptors.

### #7 Querying for an Artist's Songs that Start with "C" using the DynamoDB Document Client

In this case, we'll need to use the same `begins_with` syntax as we did before. Except in this case, we don't need to specify the data type descriptors. In `ExpressionAttributeValues` we just use `':song': 'C'` rather than specifying the string type.

```js
// Query all songs by artist Arturus Ardvarkian that start with "C" using the Document Client
async function logArtistSongsStartingWithCDdbDc(){
    try {
        var params = {
            KeyConditionExpression: 'artist = :artist AND begins_with ( song , :song )',
            ExpressionAttributeValues: {
                ':artist': 'Arturus Ardvarkian',
                ':song': 'C'
            },
            TableName: tableName
        };
        var result = await ddbDocumentClient.query(params).promise()
        console.log(JSON.stringify(result))
    } catch (error) {
        console.error(error);
    }
}
logArtistSongsStartingWithCDdbDc()

// { 
//    "Items":[ 
//       { 
//          "priceUsdCents":142,
//          "artist":"Arturus Ardvarkian",
//          "song":"Cadet Celadon",
//          "publisher":"MUSICMAN INC",
//          "id":"fd7667cb-3a41-4777-93bb-ed2d0d8d7458"
//       },
//       ...
//    ],
//    "Count":4,
//    "ScannedCount":4
// }
```

Just like before, we get back all the songs that start with C! This time with no data type descriptors.

### #8 Scanning a DynamoDB Table with the DynamoDB Document Client

```js
// Scan table for all items using the Document Client
async function scanForResultsDdbDc(){
    try {
        var params = {
            TableName: tableName
        };
        var result = await ddbDocumentClient.scan(params).promise()
        console.log(JSON.stringify(result))
    } catch (error) {
        console.error(error);
    }
}
scanForResultsDdbDc()

// { 
//    "Items":[ 
//       { 
//          "priceUsdCents":207,
//          "artist":"Marguerite Mcclure",
//          "song":"Cedar Columbia",
//          "publisher":"TELEQUIET",
//          "id":"4e01c867-3084-4ae4-9c8a-5b0750465037"
//       },
//       ...
//    ],
//    "Count":70,
//    "ScannedCount":70
}
```

Just as before, this results in giving us all the data back from the table! The main difference is (you guessed it!), that we're not getting any data type descriptors back!

## Now What?

I wanted to provide a few examples of common operations when working with DynamoDB. Do you have more questions about how to use the AWS SDK for JavaScript with DynamoDB? Do you want to know more about the other methods to write and read data from DynamoDB? Then [sign up for my mailing list](/mailing-list) and get in touch [on Twitter]({{% my_twitter %}})! I'll keep you posted on my latest guides and tutorials and you can shoot me direct replies on what you'd like me to cover next!
