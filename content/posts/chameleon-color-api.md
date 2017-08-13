+++
title = "Chameleon Color Scheme API"
date = 2017-08-12T19:26:58-07:00
Description = "A first look at the Chameleon Color Scheme Micro API."
Tags = [
  "Serverless",
  "Python",
  "AWS",
  "Micro API",
  "Chameleon"
]
Categories = [
  "AWS",
  "Serverless",
  "Micro API"
]
menu = "main"
publishdate = "2017-08-12T19:26:58-07:00"

+++


<center>![A sample landscape used by the Chameleon color API](/images/chameleon_announcement/landscape.png)</center>

One of the newest additions to my project portfolio site [(serverlessfoo.com)](serverlessfoo.com) is the [Chameleon color scheme API](https://www.serverlessfoo.com/projects/chameleon/index.html). The API returns a random color scheme of dominant and auxiliary colors in rgb form to be applied to a website. Here's how I built the API for less than a penny.

<!--more-->

<center>![A sample landscape used by the Chameleon color API](/images/chameleon_announcement/chameleon-colors.png)</center>

This project had a few initial steps:

1. Gather copyright-free images from [pexels.com](https://www.pexels.com/). 
2. Process all these landscape images and generated a color scheme using the Python [ColorThief](https://github.com/fengsp/color-thief-py) library. This returns results in the format `rgb(#, #, #)`.
3. Take the resulting dominant color and palette values and structure them uniformly into JSON.
4. Load the JSON into an AWS DynamoDB table.
5. Develop the API itself using the [Serverless Framework](serverless.com).
6. Test, test, test. 
7. Deploy the v1 api to AWS and create a [microsite]((https://www.serverlessfoo.com/projects/chameleon/index.html)) on serverlessfoo.com to demonstrate the functionality.

All of these setup steps are free to run locally and AWS services cost a fraction of a cent to keep this API running. This does assume I don't start getting a massive influx of traffic, but I can add rate-limiting if I ever reach that point to cap my costs.

This is one of the newest micro-APIs I've been working on. I hope to extend the functionality of the [current demo](https://www.serverlessfoo.com/projects/chameleon/index.html) to allow the user to upload an image in the browser, have that image processed, and return a color scheme for that image dynamically. This presents a few new challenges for handling binary data with the AWS API Gateway so I've decided to release the first version now and iterate on it when I have a free weekend.

In case you're wondering, the initial inspiration for this project came from [design seeds](design-seeds.com). After unsuccessfully convincing them to partner with me to create this using their data and imagery I decided to just make one on my own.

If you'd like, you can check out the project code on [GitHub](https://github.com/fernando-mc/color-scheme-api) and use that to deploy your own version.
