+++
Description = "Announcing Nandolytics: The most advanced analytics service for your blog imaginable (within about three hours to imagine it)."
Tags = [
  "Serverless",
  "Python",
  "AWS",
  "Analytics",
  "DynamoDB"
]
Categories = [
  "AWS",
  "Serverless",
  "Projects"
]
title = "Nandolytics - My Own Visits Analysis Tool"
menu = "main"
publishdate = "2018-03-30T23:04:33-05:00"
date = "2018-04-01T23:04:33-05:00"
[image]
    feature = "/images/nandolytics/nandolytics1.png"
+++

I'd like to introduce to you a new advanced analytics tool for site traffic - Nandolytics. 

Nandolytics is a highly-advanced one-of-a-kind analytics service that's going to change the world through uniquely variable analysis in a market of established and reliable vendors for analytics services. 

A few lucky visitors can have access to a demo of how I built this service. Find out if you're one of them by clicking the button below.

<!--more-->

<center> <button style="background-color: #008CBA;
    border: none;
    color: white;
    padding: 15px 32px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;"
onclick="checkHitsOnClick()">Find out if you're a winner!</button></center>
<div id='totalHits'></div>
<div id='ricky'></div>

<script>
var ricky_iFrame = '<center><iframe width="560" height="315" src="https://www.youtube.com/embed/dQw4w9WgXcQ?&autoplay=1" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe></center'
var root_url = "https://hz9ap21u7a.execute-api.us-east-1.amazonaws.com/dev/nandolytics/"
var get_endpoint = root_url + "https%3A%2F%2Fwww.fernandomc.com"
var record_endpoint = root_url + "record"

// Record a visit to this site
fetch(record_endpoint, {
  method: 'post',
  body: JSON.stringify({"siteUrl":"https://www.fernandomc.com"})
}).then(r => r.json())
  .then(data => console.log(data))
  .catch(e => console.log("The request for Nandolytics failed"))

var siteHits = 0

// Check the current visit to this site when clicked.
function checkHitsOnClick(){
  fetch(get_endpoint).then(r => r.json())
    .then(data => {
      console.log(data)
      document.getElementById('totalHits').innerHTML = '<strong style="color:blue; font-size:2em">Your visit was number: ' + data['siteHits'] + '! April Fools!</strong>'
      siteHits = data['siteHits']
    })
    .catch(e => console.log("The request for Nandolytics failed")) 

  // Everyone is a winner!
  document.getElementById('totalHits').innerHTML= siteHits
  document.getElementById('ricky').innerHTML= ricky_iFrame
  document.getElementById('aprilFoolsHidden').innerHTML= "Happy April Fools! Despite the mandatory Rick Astley video and complete lack of a real prize I do actually have a fun demo to share with you."
}

</script>

<p id="aprilFoolsHidden"></p>

I was using my website analytics recently to review my site traffic and I was curious how difficult it would be to build my own web analytics service that could count website hits on my blog. So I did - sort of: I present to you **_Nandolytics_**:

![An image of the word Nandolytics](/images/nandolytics/nandolytics1.png)

Now before I continue, let me without question let you know that this entire thing is super unreliable/easily exploitable and suggest that you never use it in a real production environment. With that said... Let's have some fun!

While working on a course that touches on AWS DynamoDB I [wrote about](https://linuxacademy.com/blog/amazon-web-services-2/dynamodb-atomic-counters/) DynamoDB atomic counters. Atomic counters essentially allow you to increment numeric values in a single write operation rather than doing a read and a subsequent write. This avoids issues like accidentally overwriting previous data and generally adds some fault-tolerance. You can technically increment _or_ decrement the values you're evaluating **so I figured what the heck - I'll try to make a website 'hits' counter that keeps track of website hits.**

Here's how you can implement it on your own site. 

The first thing you need to know is that there are two endpoints for Nandolytics:

1. The `nandolytics/record` endpoint - this is used to tabulate visit counts internally. You POST JSON data to the endpoint and it is tabulated in your DynamoDB table.
2. The `nandolytics/{id}` endpoint - this is used to check the internal site hits numbers.

The first thing you'll need to do is to copy the [Serverless Framework](https://www.fernandomc.com) project I created for this. The code and configuration can be found [on Github](https://github.com/fernando-mc/nandolytics).

You can also do this with `git clone https://github.com/fernando-mc/nandolytics.git`.

Once you have the repo locally then you can deploy it with `sls deploy`. Copy down the API endpoints it outputs from the deployment. You'll use these later on. 

This project uses a DynamoDB table that has a simple primary key consisting of a `siteUrl` attribute as a partition key. In addition to the `siteUrl` all these items have a number type attribute - `siteHits`. Now `siteHits` needs to be a number type because that's required for using an atomic counter with DynamoDB. So, a sample item in the table might initialize to this for my blog's homepage.


```json
{
  "siteUrl": "https://www.fernandomc.com/",
  "siteHits": "1"
}

```

Fortunately the Serverless Framework project will spin all this up and manage it for you along with the API endpoints and Lambda functions you need.

In order to implement this on your own site you'll need to include some JavaScript to send a POST request to your API each time you load the page. You can also optionally use the GET endpoint to get the siteHits information without adding to the site hits.

After deploying your Serverless Framework project replace the API_ENDPOINT variable below with your own and copy the code into whatever page you'd like to count hits for.

```javascript
var currentEncodedURL = encodeURIComponent(window.location.href)
var API_ENDPOINT = "https://some12url34.execute-api.us-east-1.amazonaws.com/dev/nandolytics/"
var getEndpoint = API_ENDPOINT + currentEncodedURL
var recordEndpoint = API_ENDPOINT + "record"

// Record a visit to this site
fetch(record_endpoint, {
  method: 'post',
  body: JSON.stringify({"siteUrl":currentEncodedURL})
}).then(r => r.json())
  .then(data => console.log(data))
  .catch(e => console.log("The request for Nandolytics failed"))

// Console log current site visits
fetch(get_endpoint).then(r => r.json())
  .then(data => {
    console.log(data['siteHits'])
  })
  .catch(e => console.log("The request for Nandolytics failed"))
}
```

After the JavaScript is copied onto the page it should run on your site whenever the page is loaded - And boom! You have your very own analytics service to count site hits. Have fun playing around with it and Happy April 1st!