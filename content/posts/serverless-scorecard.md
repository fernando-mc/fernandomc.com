+++
Description = "In this post, we take a look at how to use the public GitHub APIs and AWS to create a project that compares GitHub project popularity."
Tags = [
  "Architecture",
  "Serverless",
  "Python",
  "AWS",
]
Categories = [
  "Projects",
  "AWS",
]
title = "The Serverless Scorecard"
publishdate = "2020-05-15T12:33:45-07:00"
date = "2020-05-15T12:33:45-07:00"
type = "blog"
[image]
    feature = "/images/20-projects-20-days/scorecard.png"
    postheader = "/images/20-projects-20-days/scorecard-postheader.png"
+++


In this project we reach the halfway point in my [20 projects in 20 days](https://fernandomc.com/posts/twenty-projects-in-twenty-days/) series! We'll be looking at how I built the [Serverless Scorecard](https://serverless-scoreboard.now.sh/) to keep track of the fluctuations in popularity of common serverless development tools.
<!--more-->


## Prerequisites

Before we get started, here's what I used to build this project:

1. [The Serverless Framework](https://serverless.com)
2. An AWS Account with the [AWS CLI installed](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) and configured
3. [Python](https://www.python.org/downloads/)
4. [Node.js](https://nodejs.org/en/)

## Build the project

The overall concept of the project is pretty simple:

1. Grab the forks, stars, and watchers stats of several severless GitHub projects
2. Put them up on a website together and chart how they compare
3. Update the statistics on a daily basis

Fortunately for us, the GitHub APIs are open and easy to use. In this case, you can get the data we want on GitHub repos quite easily using the [repos endpoint](https://developer.github.com/v3/repos/#get-a-repository). This endpoint can be hit from anywhere within a certain rate limit. Since we only need to call it a few times a day we're well within the limits here. So getting the data and storing it as JSON can be accomplished with a simple Python Lambda Function:

```py
import json
import boto3
import requests

def handler(event, context):
    # Set of Repos to look for metadata
    frameworks = [
        'serverless/serverless', 
        'miserlou/zappa', 
        'apex/apex', 
        'aws-amplify/amplify-js', 
        'awslabs/serverless-application-model', 
        'jorgebastida/gordon',
        'mweagle/Sparta',
    ]
    # Request data from the GitHub API
    results = []
    for framework in frameworks:
        r = requests.get('https://api.github.com/repos/' + framework)
        data = json.loads(r.text)
        framework_stats = {
            "name": data["name"],
            "subscribers_count": data["subscribers_count"],
            "stargazers_count": data["stargazers_count"],
            "forks_count": data["forks_count"],
        }
        results.append(framework_stats)
    json_result = json.dumps(results)
    # Output data to S3 file
    s3 = boto3.client('s3')
    s3.put_object(
        ACL='public-read',
        Body=json_result,
        Bucket="fmc-data-utils", 
        Key="recent_results.json",
    )
    print("RESULTS:")
    print(results)
    return "Success"
```

In the code above, we have a set of framework organizations and repos that we look through. We then gather the specific data for each of those repos and put them into a JSON file which we store in Amazon S3 using the AWS SDK for Python - Boto3. 

From this point, we need a website to showcase this data.

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <!-- Site Properties -->
  <title>Serverless Scorecard</title>
  <link rel="stylesheet" type="text/css" href="https://cdnjs.cloudflare.com/ajax/libs/semantic-ui/2.4.1/semantic.min.css">
  <script
      src="https://code.jquery.com/jquery-3.4.1.min.js"
      integrity="sha256-CSXorXvZcTkaix6Yvo6HppcZGetbYMGWSFlBw8HfCJo="
      crossorigin="anonymous">
  </script>
  <script src="https://underscorejs.org/underscore-min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/semantic-ui/2.4.1/semantic.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/chart.js@2.8.0"></script>
</head>
<body>
<!-- ... -->
```

The website relies on Semantic UI for the majority of the styling and then also uses Underscore.js in order to make parsing the data a little easier. The last dependency here is chart.js which is an open source library for making simple charts.

From here, we have a few HTML elements to style the page with Semantic and to contain the charts when they are rendered by Chart.js. To take a look at that, see lines 20 to 64 [here](https://github.com/fernando-mc/serverless-scoreboard/blob/master/frontend/index.html)

After the HTML we have a short script section where our JavaScript code makes a single HTTP request to get the most recent data from the `recent_results.json` file that our Python Lambda Function recreates each day inside of Amazon S3. We then use this data to plot several Chart.js charts inside the website's divs:

```js
    $.getJSON('https://fmc-data-utils.s3.amazonaws.com/recent_results.json' , function(json_results) {
        var topStars = document.getElementById('top-stars').getContext('2d');
        var topWatchers = document.getElementById('top-watchers').getContext('2d');
        var topForks = document.getElementById('top-forks').getContext('2d');
        var chart = new Chart(topStars, {
            type: 'bar',
            data: {
                labels: _.pluck(json_results, 'name'),
                datasets: [
                  {
                    label: 'Stars',
                    backgroundColor: '#801515',
                    borderColor: '#801515',
                    data: _.pluck(json_results, 'stargazers_count')
                  }
                ]
            },
            options: {
              scales: {
                yAxes:[{
                  ticks: {
                    callback: function(value, index, values) {
                      return value / 1e3 + 'k';
                    }
                  }
                }]
              }
            }
        });
        var chart = new Chart(topWatchers, {
            type: 'bar',
            data: {
                labels: _.pluck(json_results, 'name'),
                datasets: [
                  {
                    label: 'Watchers',
                    backgroundColor: '#FFAAAA',
                    borderColor: '#FFAAAA',
                    data: _.pluck(json_results, 'subscribers_count')
                  },
                ]
            },
            options: {
              scales: {
                yAxes:[{
                  ticks: {
                    callback: function(value, index, values) {
                      return value / 1e3 + 'k';
                    }
                  }
                }]
              }
            }
        });
        var chart = new Chart(topForks, {
            type: 'bar',
            data: {
                labels: _.pluck(json_results, 'name'),
                datasets: [
                  {
                    label: 'Forks',
                    backgroundColor: '#550000',
                    borderColor: '#550000',
                    data: _.pluck(json_results, 'forks_count')
                  }
                ]
            },
            options: {
              scales: {
                yAxes:[{
                  ticks: {
                    callback: function(value, index, values) {
                      return value / 1e3 + 'k';
                    }
                  }
                }]
              }
            }
        });
        
    });
```

Chart.js gives us a variety of utilities to create new charts, style them, and then change the labels and formatting around. For more details on using Chart.js take a [look here](https://www.chartjs.org/).

## Deploying the Project

Awesome! So how do we deploy it all? For the frontend, I ended up using [Vercel](https://vercel.com/) to deploy my frontend folder pretty easily. All I need to do was [download and install Vercel](https://vercel.com/download) and then sign in with my account before running the `vercel` command and pointing it to my `frontend` folder in the setup process. Then it deployed the project out for me where you can see it [here](https://serverless-scoreboard.now.sh/).

For the backend, I used the Serverless Framework - Clearly showcasing some bias in this project. Let's look at the `serverless.yml` configuration file for this project now. It starts with a service name and provider configuration:

```yml
service: serverless-scorecard

provider:
  name: aws
  runtime: python3.7
  iamRoleStatements:
    - Effect: 'Allow'
      Action:
        - 's3:GetObject'
        - 's3:PutObject'
        - 's3:PutObjectAcl'
      Resource:
        - 'arn:aws:s3:::fmc-data-utils/*'
```

This section will allow us to create a `python3.7` Lambda function and also to give the service the ability to refresh the data in the S3 bucket as well as to make the object it puts there public so that the website can pick it up. From there, we can define the Lambda function in the `functions` section:


```yml
functions:
  hello:
    handler: handler.handler
    events:
      - schedule: rate(1 day)
```

This configuration refers to a `handler.py` file and the `handler()` function inside of that. It also sets this function to run once a day with the schedule event. The next section configures the `serverless-python-requirements` plugin that I use to install my Python dependencies:

```yml
plugins:
  - serverless-python-requirements

custom:
  pythonRequirements:
    dockerizePip: non-linux
```

In order to deploy the project I first need to run `npm install serverless-python-requirements` to get the plugin. Then I can run `serverless deploy` to deploy the service out to regularly update the data file. 

**Caveats**

If you're trying to build something similar keep in mind that you'll need to setup your own Amazon S3 bucket and configure the permissions on that bucket to be able to reference it's data from other websites with CORS settings. You'll also need to make sure that you're granting the appropriate permissions to your Serverless Framework service in order to access your S3 bucket.

Want to learn more about how to create Serverless Framework projects or use other AWS services? Join my [mailing list](/mailing-list) and you can reply to the welcome email to get 30 days of free access to all [my Pluralsight Courses](https://app.pluralsight.com/profile/author/fernando-medina).