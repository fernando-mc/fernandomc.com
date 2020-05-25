+++
Description = "A full list of the twenty AWS, Serverless, Python and Node.js projects I'll be sharing in the month of May. Check back for updates every weekday!"
Tags = [
  "Serverless",
  "Python",
  "Boto3",
  "AWS",
  "DynamoDB",
  "Python3",
  "Node.js",
  "Amazon Web Services",
]
Categories = [
  "Projects",
  "AWS",
]
title = "Twenty Projects in Twenty Days"
publishdate = "2020-05-01T11:10:16-08:00"
date = "2020-05-01T11:10:16-08:00"
type = "blog"
[image]
    feature = "/images/twenty-projects/clouds.png"
    postheader = "/images/twenty-projects/clouds.png"
    credit = "theaucitron"
    creditlink = "https://www.flickr.com/photos/theaucitron/5810163712"
+++

This May, I'll be sharing 20 different starter projects that I've built using different AWS services, Serverless technologies, Python, Node.js and more! If you'd like to get these projects in your inbox, you can sign up for my mailing list [here](/mailing-list).

After signing up, you'll get blog posts delivered to your inbox with the following:

- Every project's source code
- An accompanying blog post tutorial for the project
- Access to a live version of the project (if one exists)
- A free 30-day trial to all of my [Pluralsight.com](https://pluralsight.com/) courses (reply to the first welcome email and I'll send a unique trial code your way)

You can check back on this blog post throughout the month - I'll update it with links to everything. Now let's take a look at the calendar of different projects you can expect to see this month!
<!--more-->

- May 4: [Tech Pay Rates](/posts/tech-pay-rates-revisited/)
    - Tech Pay Rates is a project I put together a while back to allows users to submit wage information and search through other submissions. The entire codebase is around 500 lines and leverages Google Recaptcha to avoid span and Algolia for search. For this project, you'll get a look at the codebase, a blog post and the live site!
    - [GitHub repo](https://github.com/fernando-mc/techpayrates)
    - [Blog post](/posts/tech-pay-rates-revisited/)
    - The Live Tech Pay Rates website can be found at [techpayrates.com](https://techpayrates.com/)
- May 5: Using the Serverless Framework with Flask and DynamoDB
    - This will be a simple Flask API project deployed to AWS using the Serverless Framework. In the background, we'll manipulate three different entities: customers, surveys, and survey responses and store them all in a DynamoDB table. For this project, you'll have access to the codebase and a blog post explanation of how to get started with it.
    - [GitHub repo](https://github.com/fernando-mc/flask-based-api)
    - [Blog post](/posts/developing-flask-based-serverless-framework-apis/)
    - Bonus! If you sign up for my [mailing list](/mailing-list/) you can email me for access to my Pluralsight course on the Serverless Framework!
- May 6: Using the Serverless Framework with Express.js and DynamoDB
    - For those who prefer JavaScript, this will be the Express.js equivalent of the project on May 5. It will show how to create a simple Express.js API deployed to AWS using the Serverless Framework. In the background, we'll keep the same three entities: customers, surveys, and survey responses. We'll also store all the data in a DynamoDB table. For this project, you'll have access to the codebase and a blog post explanation of how to get started with it.
    - [GitHub repo](https://github.com/fernando-mc/express-based-api.git)
    - [Blog post](/posts/developing-expressjs-serverless-framework-apis/)
- May 7: Nandolytics - A homemade analytics service.
    - Nandolytics is a homemade web visits analytics service that uses Amazon DynamoDB atomic counters to increment page visits. This project was made on April 1 of last year... So forgive me... You'll have access to the GitHub repository for the code, along with a blog post which also happens to showcase a live demo of the tool in action.
    - [GitHub repo](https://github.com/fernando-mc/nandolytics)
    - [Blog post](/posts/nandolytics-serverless-website-analytics/)
- May 8: Chameleon - The Color Scheme Generator API
    - This project involves creating an API that returns various color schemes. The idea behind it was to allow websites to use the API to then change their color scheme using the API. You'll have access to the GitHub code used for this project, the explanatory blog post, and the live demo of this project.
    - [GitHub repo](https://github.com/fernando-mc/chameleon-v2/)
    - [Blog post](/posts/chameleon-api/)
    - The live demo of this API can be seen [here](http://chameleon-api.s3-website-us-east-1.amazonaws.com/)
- May 11: I'm Hungry - A Silly Vue.js Game
    - This project is one of the most ridiculous of the bunch. It's really just a first attempt to play around with Vue.js in a fun way. You'll see how to build the simplest of browser-based games using Vue.js. You'll get access to the GitHub code, a blog post on the project, and a live example.
    - [GitHub repo](https://github.com/fernando-mc/vue-game)
    - [Blog post](/posts/vue-js-im-hungry)
    - [Live Example](https://vue-game.now.sh/)
- May 12: Using AWS Lambda Event Destinations with the Serverless Framework
    - This will help you get started with AWS Lambda event destinations using the Serverless Framework. I've put together a GitHub repo with several examples, a blog post on when to use event destinations to setup alerts on a Lambda Function, and another article I've written previously on how to get started with them.
    - [GitHub Repo](https://github.com/fernando-mc/lambda-event-destination-alerts)
    - [Blog post](/posts/lambda-event-destinations)
    - [Getting Started with Event Destinations](https://serverless.com/blog/lambda-destinations/)
- May 13: Translating things with Amazon Translate
    - This will show you some of the basics of how you can use Amazon Translate, Python and Boto3 to translate your text into different languages. You'll get access to the code in a GitHub repo and a blog post explaining how you can use the AWS SDK for Python - Boto3 to translate your text! You'll also get a second blog post explaining how you can use Custom Terminologies to do more customized translations.
    - [GitHub Repo](https://github.com/fernando-mc/amazon-translate-examples)
    - [Blog Post - The Basics of Amazon Translate](/posts/amazon-translate-python-nodejs/)
    - [Blog Post on Custom Terminologies](/posts/amazon-translate-custom-terminology/)
    - Special bonus! Sign up for my [mailing list](/mailing-list) to get free access to my Pluralsight course on Amazon Translate (reply to the welcome email to request a code).
- May 14: GleanTeam USA
    - This project will attempt to use a mapping API to map available gleaning locations across the United States. Gleaning is when farmers have extra food they can't harvest or sell and are willing to let anyone come and take it home. Due to fluctuations in demand with COVID-19 and the increased risk of hunger for people across the United States, I thought creating a tool to map these locations could be useful. This project will give you the chance to see the entire process in action.
    - The Twitch stream of the development process was on May 9, 2020.
    - The [blog post](/posts/project-planning-glean-team-usa/)
- May 15: Serverless Scoreboard
    - This project will use the GitHub API and AWS Lambda functions to regularly update some basic data about a series of popular projects in the serverless space. We will get basic data on each project's GitHub repos including watchers, stars, and forks. Then, we will automatically collect and update the data as a JSON file in AWS and chart it using an open source charting library to showcase the relative popularity of some tools. You'll get access to the code, a blog post and a live demo of the project.
    - [GitHub Repo](https://github.com/fernando-mc/serverless-scoreboard/)
    - [Blog post](/posts/serverless-scorecard/)
    - [Live demo](https://serverless-scoreboard.now.sh/)
- May 18: Voices of COVID-19
    - For this project, I'd like to try and collect the stories of people who have been impacted by the COVID-19 pandemic across the country. I'll attempt to create a project that allows people to upload audio of their stories into a web application and have those stories then made available to anyone to listen to. 
    - You'll get to watch a live Twitch stream of the development process at 11am on May 16, 2020.
    - Access to any code created from the project
    - [Blog Post](/posts/voicesofcovid/)
    - [Live project](https://voicesofcovid.com/)
    - [GitHub Code](https://github.com/fernando-mc/voicesofcovid.com)
- May 19: AWS HTTP API with a Cognito Authorizer and Node.js
    - AWS recently released a new AWS HTTP API service which is leaps and bounds cheaper than it's older API Gateway REST API service. Because of this I've put together a few tutorials in different languages to get people started with common use cases. This first one will let you add authentication to HTTP APIs using Amazon Cognito, the Serverless Framework and Node.js. 
    - You'll get access to the GitHub code and a blog post tutorial on this 
    - [GitHub Repo](https://github.com/fernando-mc/aws-http-api-node-cognito)
    - [Blog post](/posts/aws-http-api-cognito-authorizers-nodejs/)
- May 20: AWS HTTP API with a Cognito Authorizer and Python
    - I can't let the Python users down, so I've essentially created the same project as the one on May 19 for Python users to play with. This will be pretty similar to the May 19 project except you'll get it with Python!
    - [GitHub Repo](https://github.com/fernando-mc/http-api-cognito-profiles)
    - [Blog post](/posts/aws-http-api-cognito-authorizer-python/)
- May 21: AWS HTTP API Surveys Service with DynamoDB and Node.js
    - Continuing down the AWS HTTP API rabbit hole, I've also decided to showcase the "Surveys Service" described above for Express.js and Flask applications in the context of the AWS HTTP API. You'll see how to model multiple entities in a single DynamoDB required to power a "Surveys Service". Where you can create different customers, surveys for those customers, and collect responses to the surveys they create. You'll get access to a GitHub repo for the code and a blog post on this.
    - [GitHub Repo](https://github.com/fernando-mc/http-api-surveys-service-node)
    - [Blog post](/posts/aws-http-api-surveys-service-nodejs/)
- May 22: AWS HTTP API Surveys Service with DynamoDB and Python
    - Again, I can't let Python lovers down so I've written the May 21 project in Python too for you to play with. You can expect the same things from this except with more Python!
    - [GitHub Repo](https://github.com/fernando-mc/http-api-surveys-service)
    - [Blog post](/posts/aws-http-api-surveys-service-python/)
- May 25: Serverless Frontends with Serverless Finch
    - I realized I had a post on this already! I've included an extra project repo and a post link below!
    - [Example GitHub Repo](https://github.com/fernando-mc/serverless-frontend-backend)
    - [Blog post](/posts/deploy-your-website-serverless-finch-serverless-framework)
- May 26: Schema Validation with the Serverless Framework
    - For a long time a wrote lots of validation code in my Lambda functions themselves to make sure that the incoming POST data was formatted properly. Then I realized that you can create JSON Schema Draft 04 models for your data and validate the requests before they even hit the Lambda Function! That's what I'll be showing you in this tutorial using the Serverless Framework. You'll get all the code and a blog post tutorial.
    <!-- - [GitHub Repo](https://github.com/fernando-mc/schema-validation-demo) -->
    <!-- - [Blog post](??????????) -->
- May 27: Voices of COVID Technical Deep Dive
    - This will go into more detail about the technology used to create the [Voices of COVID](https://voicesofcovid.com) project
- May 28: TBD - A Foray into Automated Software Testing - requested by Andrea. 
    - To be determined! Make sure to sign up for my [mailing list](/mailing-list/) to submit your idea!
- May 29: TBD - A subscriber submitted idea (Reserved for COVID related mutual aid or support)
    - To be determined! Make sure to sign up for my [mailing list](/mailing-list/) to submit your idea!

I can't wait to share these all with you! If you'd like to help me continue doing things like this, consider supporting my work by becoming a Patreon patron [here](https://www.patreon.com/fmc_sea). All my Patreon proceeds this month will go to [National Nurses United](https://www.nationalnursesunited.org/) to help nurses get safer working conditions and access to PPE during the COVID-19 pandemic.

Remember, if you want to request a project that I should create then [sign up for my mailing list](/mailing-list/) and reply to the welcome email with your idea! I'll pick three projects ideas submitted by subscribers to include during the last two days of this 20-day project spree!
