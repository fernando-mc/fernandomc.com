+++
menu = "main"
date = "2017-03-27T16:59:48-07:00"
title = "Sparrow - A Python Twitter Bot Shell for AWS Lambda"
draft = false
Description = ""
Tags = [
  "Sparrow",
  "Serverless",
  "Python",
  "AWS",
  "AWS Lambda",
  "Bots",
]
Categories = [
  "AWS",
  "Python",
  "Projects",
]

+++

Meet [Sparrow](https://github.com/fernando-mc/sparrow) - a Twitter bot shell that makes it easier to create interesting automated accounts. In this post I'll show you how to make a simple automated Twitter bot on AWS using Sparrow and AWS Lambda. Better yet, it's all free!

<!--more-->
Here's a few examples of ways I'm using it:

- Powering my Days Left of Trump countdown - https://twitter.com/fmc_sea/status/846119578933612546
- Running The Daily Patent - https://twitter.com/dailypatent ([code here](https://github.com/fernando-mc/dailypatent))
- To help folks learn about Python when I teach courses on [Python](https://www.meetup.com/phillypug/events/232030203/) or [AWS Lambda](https://www.pluralsight.com/courses/aws-developer-introduction-aws-lambda)

## Setup Steps
Here are some things you should make sure are done before working with this repository:

1. Install pip

    The latest version of Python 2.7 should have 
    pip installed by default so you likely have it 
    already! If you need help getting pip read 
    more here:
    http://pip.readthedocs.io/en/latest/installing/#install-pip

    This should be the case for all modern operating systems.

2. Install virtualenv

    If you don't have virtualenv installed you can
    install it with pip. One of these commands
    should work:
    
    ```bash
    pip install virtualenv
    
    sudo pip install virtualenv
    ```

    If neither of these commands work you can read
    more in the documentation here:
    https://virtualenv.pypa.io/en/stable/installation/

3. Install boto3

    If you don't have boto3 you can install it 
    with pip. This command should work:

    ```bash
    pip install boto3
    ```

    If you have any issues you can review the documentation here:
    https://boto3.readthedocs.io/en/latest/guide/quickstart.html

4. Install the AWS CLI

    If you don't have the AWS Command Line 
    Interface setup you can install it with pip. 
    One of these commands should work:

    ```bash
    pip install awscli

    sudo pip install awscli

    sudo pip install awscli --ignore-installed six
    ```

    If none of these commands work you can read
    more in the documentation here:
    http://docs.aws.amazon.com/cli/latest/userguide/installing.html

5. Get your AWS Access Keys

    Log into the AWS console and follow these
    instructions:

    a. Go to https://console.aws.amazon.com/iam/home?#home

    b. Choose "Users"
    
    c. Choose your IAM username (not the check box)
    
    d. Choose the Security Credentials tab and 
        then choose Create Access Key
    
    e. To see your access key, choose Show User 
    Security Credentials. Your credentials will 
    look something like this:

    ```bash 
    Access Key ID: AKIAIOSFODNN7EXAMPLE
    Secret Access Key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
    ```

    f. Choose Download Credentials, and store 
    the keys in a secure location

    If you have any issues with this step review the documentation:
    Reference:
    http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSGettingStartedGuide/AWSCredentials.html)

6. Configure the AWS CLI

    Once you have your access keys you can 
    configure the AWS CLI. 

    Open a shell and use this command:

    ```bash
    aws configure
    ```

    Follow the prompts and enter in your keys 
    and the region you are working in. You can
    see a list of regions and region codes **[here](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-available-regions)**. Example of what you should input (except press enter where it says ENTER):

    ```bash
    AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
    AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
    Default region name [None]: us-east-1
    Default output format [None]: ENTER
    ```
    You should now be ready to go with the AWS CLI

7. Twython

    You should be able to install Twython with:

    ```bash
    pip install twython
    ```

    If you can't do this because of any errors hold off on this for now. 
    [documentation](https://twython.readthedocs.io/en/latest/usage/install.html). 
    
8. Twitter API Keys

    You'll need to make a twitter account and then get Twitter API keys at [https://apps.twitter.com](apps.twitter.com).
    Go through the signup process and go to the "Keys and Access Keys" section to get the following values:
    
    - Consumer Key (API Key)
    - Consumer Secret (API Secret)
    - Access Token
    - Access Token Secret

    Important Notes:

    1. Don't put API credentials in your source code
    2. API creds are sensitive info that are roughly equivalent to usernames/passwords

9. Next you'll need to grab the Sparrow code from my repo [here](https://github.com/fernando-mc/sparrow). I suggest you do this with git, but you could also just download the zip file and unzip it.

    ```bash
    git clone https://github.com/fernando-mc/sparrow
    ```

OKEY DOKEY. With those setup steps out of the way, let's move on.

## Code and Config

Now that you have all the code locally, open up `creds.json` and replace the values in quotes with your API credentials. The result would look something like this:

```json
{
    "consumer_key": "EXAMPLEajsgdfvAJSDyugakshjd",
    "consumer_secret": "PCEXAMPLE0CHaisdgkjasdgASDGYJHBlasgdkjashdg",
    "access_token_key": "48123612522-6TyALjkashdGAsdlkajsEXAMple",
    "access_token_secret": "EMAMple8lakJkajlshdlkjncXZkjlasdasd"
}
```

Save that file and start a virtualenvironment:

```bash
virtualenv .env && .env/bin/activate
```

Then install Twython in the activated virtualenv
```bash 
pip install Twython
```

Your terminal prompt should look something like this now:
```bash
(.env) $ 
```

Now start up python and import your sparrow_nokms file and send a sample tweet using send_tweet and the handler. For reasons we'll get into in a moment, the handler function needs to have two values passed into it. But for now it doesn't matter what they are.

```python
from sparrow_nokms import send_tweet, handler
send_tweet('Using Sparrow by @fmc_sea rocks! https://github.com/fernando-mc/sparrow')
handler('anything', 'anything-else')
```

Check to make sure this sent a tweet out! If it worked, your configuration is good to go and you're ready for the next steps!

Now open up the sparrow_nokms.py file and navigate to `potential_tweets`. In there you'll find an array of potential tweets to send out. Change those to whatever you'd like to send out:

```python
# Sample random tweets
potential_tweets = [
    'This is my first tweet with Sparrow by @fmc_sea - https://github.com/fernando-mc/sparrow',
    'Wow! Isn\'t Sparrow by @fmc_sea just the coolest! https://github.com/fernando-mc/sparrow',
    'Jeez! Everyone should learn about AWS Lambda and Twitter Bots from @fmc_sea'
]
```
You can get as complicated as you want, form the array from scratch, or have the function create a custom tweet using some logic you determine. Just remember to stick within 140 characters!

Next, you'll need to bundle up for function for AWS Lambda. In the same directory as the code, run the setup script for your environment.

```bash
bash setup.sh
```

This should create a zip file containing everything you need to upload to AWS Lambda.

Now login to AWS and navigate to the [Lambda console](https://console.aws.amazon.com/lambda/).

 - Click "Create Function" or "Create a Lambda Function"
 - Click "Blank Function"
 - Skip the event schedule section for now and just press Next
 - Name your function and change the runtime to Python 2.7
 - From the "Code Entry Type" dropdown Select Upload a .zip file
 - Upload your recently created zip file
 - Skip down to the "Handler*" section and change the value to "sparrow_nokms.handler"
 - Select a role from the dropdown (you might have to create one but AWS will guide you)
 - Skip the advanced settings and press "Next" and then "Create Function" on the next page

You've just created your first Lambda function! Now test it with the test button and accept the default test event AWS offers. If the test is successful and you see a new tweet on Twitter you're all set! 

If you want to learn more fun things like how to securely use your API credentials with Amazon Key Management Service or how to add a CloudWatch Event to have your function run on schedule check out my [Pluralsight course](https://www.pluralsight.com/courses/aws-developer-introduction-aws-lambda) where I go into AWS Lambda and Sparrow more in depth!

Questions? Let me know on [Twitter]({{% my_twitter %}}) or in the comments!
