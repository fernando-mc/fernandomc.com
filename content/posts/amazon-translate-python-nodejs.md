+++
Description = "A look at using Amazon Translate to translate text using Boto3 and the AWS SDK for JavaScript"
Tags = [
  "Node.js",
  "Python",
  "AWS",
  "Amazon Translate"
]
Categories = [
  "Projects",
  "AWS",
]
title = "Using Amazon Translate with Python and Node.js"
publishdate = "2020-05-13T12:22:16-07:00"
date = "2020-05-13T12:22:16-07:00"
[image]
    feature = "/images/20-projects-20-days/amazon-translate-flags.png"
    credit = "Jim, the Photographer"
	  creditlink = "https://www.flickr.com/photos/jcapaldi/7684519832/"
+++

This is the 8th project in my [Twenty Projects in Twenty Days](https://fernandomc.com/posts/twenty-projects-in-twenty-days/) series! In this project, we'll look at how to use Amazon Translate with Python using Boto3 and with Node.js using the AWS SDK for JavaScript. We'll translate our first text in under 10 lines of code so let's get started! You can copy the code from the GitHub repo [here](https://github.com/fernando-mc/amazon-translate-examples).

<!--more-->

First, you'll need to make sure you have a few things:

1. The [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html) installed and configured with AWS credentials
2. [Python 3](https://www.python.org/downloads/) and the [Boto3 SDK](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/quickstart.html) installed if you plan to use Python
3. [Node.js](https://nodejs.org/en/) and the [AWS SDK for JavaScript](https://aws.amazon.com/developers/getting-started/nodejs/) installed if you want to use Node.js

From there, it's super easy!

## Python

Using Python, open up the Python interpreter and type in this code:

```py
import boto3

translate = boto3.client('translate')

result = translate.translate_text(
    Text='Hello! My name is Fernando.',
    SourceLanguageCode='auto',
    TargetLanguageCode='es'
)

print(result['TranslatedText'])
```

Here's what's happening above:

1. We import `boto3`
2. We create an Amazon Translate client with Boto3
3. We use the `translate_text()` method to translate text from english to Spanish (with the `es` language code)
4. We print the result out to see what it says.

You should see something like this at the end of doing this:

```py
>>> print(result['TranslatedText'])
¡Hola! ¡Hola! Mi nombre es Fernando.
```

And that's it! It's honestly that simple! Now let's do Node.js!

## Node.js

Open up the Node.js runtime and enter in the code below:

```js
var AWS = require("aws-sdk");
AWS.config.update({region: "us-east-1"});

var translate = new AWS.Translate();

var params = {
  SourceLanguageCode: 'auto',
  TargetLanguageCode: 'es',
  Text: 'Hello! My name is Fernando.'
};

translate.translateText(params, function (err, data) {
  if (err) console.log(err, err.stack); 
  else     console.log(data['TranslatedText']);
});
```

Here's what's happening:

1. The first few lines import the AWS SDK for Node.js (the `aws-sdk`) and configure the AWS region to use
2. Then we setup an Amazon Translate client with `var translate = new AWS.Translate();`
3. From there, we set up some parameters to use the in the translation request including specifying the source language be autodetected, and that it be translated into Spanish (the `es` language code)
4. Finally, we run the `translateText()` method with the parameters from earlier and then `console.log()` the `TranslatedText` property of the result which is where the translated string is stored.

It should output something like this:

```js
> ¡Hola! ¡Hola! Mi nombre es Fernando.
```

And that's it! You should be able to use this in Node.js or Python to translate text using Amazon Translate. If you want more details on how to use Amazon Translate, you can sign up for my [mailing list](https://fernandomc.com/mailing-list) and reply to the welcome email and I'll give you free access to my Pluralsight.com courses including [my course on Amazon Translate](https://www.pluralsight.com/courses/aws-translate-text)!
