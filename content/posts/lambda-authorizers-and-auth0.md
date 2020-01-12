+++
draft = true
Description = "A tutorial on adding Lambda Authorizers to your applications with Auth0."
Tags = [
  "Architecture",
  "Serverless",
  "Python",
  "AWS",
  "AWS Workspaces",
  "Auth0",
  "API Gateway",
  "AWS Lambda",
  "Lambda Authorizers",
]
Categories = [
  "AWS",
]
type = "blog"
title = "Adding Lambda Authorizers to your Serverless Applications"
publishdate = "2020-01-07T20:13:47-08:00"
date = "2020-01-07T20:13:47-08:00"
[image]
    feature = "/images/authorized.png"
    postheader = "/images/authorized-header.png"
    credit = "Christina Weese"
    creditlink = "https://www.flickr.com/photos/christinaweese/22009765950/"
+++

So you've developed a snazzy new Serverless API on AWS and everything is going great. That is until you realize that there might eventually be production data behind it that you don't want someone with Postman and 20 minutes on their hands to have access to. That's where Lambda Authorizers come in. They're a way to make sure that your API will only respond to authorized callers. Let's look at how to implement authorizers for ourselves using an example application called Serverless Jams - where we vote on our favorite coding-related music.

<!--more-->

## Resources and Tools

In this example, I'll be working with the [Serverless Framework](https://serverless.com) to spin up my API and front end and [Auth0](https://auth0.com/) in order to add user sign ups. The examples I'm creating should be within the free tiers of these two different services as of publishing this post, but always make sure to check on the most recent pricing if you're price sensitive. The project will require Python, Node.js and NPM installed.

If you've never used the Serverless Framework you can check out my [Pluralsight Course](https://www.pluralsight.com/courses/aws-nodejs-serverless-framework-using) or review some free courses on [Serverless Learn](http://serverless.com/learn) to get started. In particular, I have an entire video series on building a very similar application from scratch [here](https://serverless.com/learn/courses/serverless-for-frontend-developers/). That course will take you through building the entirety of the application, and explain how to get it running on AWS with a custom domain.

## Our Project Structure

So what does this project look like? You can find the code for this particular blog post [here](https://github.com/fernando-mc/lambda-authorizers-serverless). The code should look like this:

```bash
.
├── README.md
├── backend
│   ├── auth.py
│   ├── get_song_vote_counts.py
│   ├── get_token.py
│   ├── record_song_vote.py
│   └── verify_token.py
├── frontend
│   ├── auth_config.json
│   ├── css
│   │   └── main.css
│   ├── index.html
│   └── js
│       ├── app.js
│       └── auth0.js
├── package.json
├── requirements.txt
└── serverless.yml
```

There's a little bit going on so let's tackle everything bit by bit starting with the `frontend` directory.

## The Frontend

Here's what it looks like when I run a webserver inside of the `frontend` directory (if you have Python3 installed you can run `python3 -m http.server` from inside `frontend` to do this).

![Serverless Jams Website frontend](/images/serverlessjams/serverlessjams-frontend.png)

Basically, it's a simple UI that shows vote counts for different songs and allows us to listen to them with Spotify embeds. It also has hidden UI elements that will reveal themselves when we sign in and allow us to vote for our favorites. So what's the big deal here? Why do we need to add authorizers? Well imagine we were having some sort of contest, do we want to let just anyone vote without signing in? Probably not! That's why we need the authorizers.

The first step in enabling this is allowing users of our site to sign in. We're going to enable that using `auth0.js` and making sure we have configuration values in `auth_config.json`. Let's get those configuration values first!

### Setting up the Auth0 Application

First, we need to create an [Auth0](https://auth0.com/) account. After that we need to create an Auth0 application and populate it with a few configuration values. There should be a big "Create Application" button we can press:

![Screenshot of Auth0 Create Application button](/images/serverlessjams/auth0-step1.png)

After that we can name the application and select "Single Page Web Applications" and "Create".

![Screenshot of the Create Application process](/images/serverlessjams/auth0-step2.png)

Then, you can choose to read over the quickstarts if you'd like to adapt this demo for your preferred frontend. But I'll be going straight to the settings to grab a few configuration values:

![Screenshot of the newly-created application's settings page](/images/serverlessjams/auth0-step3.png)

Specifically, I'll want to copy the Domain and Client ID values and then add them to my `auth_config.json` file like this:

```json
{
    "domain": "dev-5xmirf9t.auth0.com",
    "clientId": "SI3gTNagj0QVjzBVQhwUhOLSzQB01b5E",
    "audience": "REPLACE_ME"
}
```

Next, I'll need to make a few configuration changes to this application so we can use it with our local frontend. I'll start by scrolling down the settings page above and looking for "Allowed Callback URLs", "Allowed Web Origins", and "Allowed Logout URLs". In each of those sections I'll need to set any domain that I will be using. In my case that might include:

- Whatever localhost url I use for local testing: `http://localhost:8000`
- The any subdomains of my domain like `www` or `app`: `https://www.serverlessjams.com`
- The domain itself without any subdomain: `https://serverlessjams.com`

Keep in mind, these must be comma separated, and include the proper protocol prefix (http or https). You'll also need to include the port for those localhost connections. For this demo you can just include `http://localhost:8000` or whatever the port is for your local webserver that runs the frontend code such as `http://localhost:3000`.

Here's an example you might use later on with custom domains:

![An Auth0 configuration window showing the URLs we need to configure](/images/serverlessjams/auth0-step4.png)

Don't forget to scroll down and save these settings when you're done!

### Setting up the Auth0 API

Next, we're going to create an Auth0 API that we can use from our frontend application. Go to the APIs section on the left of the UI and click "Create API":

![The Auth0 API section of the dashboard](/images/serverlessjams/auth0-step5.png)

Then configure the API with a name and an API identifier. This will really just be a way to tell which API you're referencing later on so make it descriptive to whatever you'd like to associate it with. In this demo I'll use `serverlessjams-vote-api` to make sure that we know it's related to the ability to vote with the API we'll create with the Serverless Framework. You can leave the algorithm the same as `RS256`.

![The Auth0 API creation modal](/images/serverlessjams/auth0-step6.png)

When we're done with this we can then copy the Identifier we set into `auth_config.json`:

```json
{
    "domain": "dev-5xmirf9t.auth0.com",
    "clientId": "SI3gTNagj0QVjzBVQhwUhOLSzQB01b5E",
    "audience": "serverlessjams-vote-api"
}
```

### Testing out Our Frontend

With all these configuration values we can now test our frontend! Let's spin it up locally. Change directories into the `frontend` directory and then spin up a webserver. Make sure that the address used locally is the same as what you configured in the earlier steps in Auth0. For example, mine should be port 8000. You can use `python3 -m http.server` as a simple way to get this working.

If you're frontend is still working from earlier - cool! Just make sure to give it a hard refresh to clear the cache so we load in the new configuration values from `auth_config.json`. 

You'll be able to tell if this works when you try to log in by pressing the "Login" button in your local application. If you see your domain from Auth0 in the URL bar it worked! For example, mine starts with `https://dev-5xmirf9t.auth0.com/login?state=`. If you see something like `https://example-replace-me.auth0.com/` you still need to hard refresh the page or make sure you saved `auth_config.json`.

After you press login you should see this page:

![The Auth0 sign in page we were redirected to](/images/serverlessjams/auth0-step7.png)

You can use this page to create your own user account of your own application! After you sign up or press login you'll also probably see this page: 

![The temporary tenant authentication page](/images/serverlessjams/auth0-step8.png)

Go ahead and press accept, this is just a byproduct of our localhost testing. We shouldn't see this page in production after we removed the localhost from the fields we configured in the Settings for our Auth0 Application earlier on.

Now that we're signed in you'll notice the UI has updated! We can now see a handy vote button and selector that will allow us to vote on the different songs! This is all happening because our JavaScript is checking to see if there is a signed-in Auth0 user.

![Screenshot of the updated Serverless Jams website](/images/serverlessjams/auth0-step9.png)

The JavaScript also updates the top right corner of the screen and adds my profile image because I'm signing in with Google and that profile image is available in the information returned from that sign in.

So this is all cool, but how does it work? Let's take a look at how we get all this information. 

First, our application loads in the `auth0-spa-js` library from the Auth0 CDN inside of our `index.html`:

```html
<script src="https://cdn.auth0.com/js/auth0-spa-js/1.2/auth0-spa-js.production.js"></script>
```

Then, `auth0.js` loads the configuration values from `auth_config.json` and creates an Auth0 client:

```js
let auth0 = null;
const fetchAuthConfig = () => fetch("/auth_config.json");

const configureClient = async () => {
  const response = await fetchAuthConfig();
  const config = await response.json();

  auth0 = await createAuth0Client({
    domain: config.domain,
    audience: config.audience,
    client_id: config.clientId,
  });
};
```

This client is used to redirect the user to Auth0's sign in page when they click the "Login" button:

```js
const login = async () => {
  await auth0.loginWithRedirect({
    redirect_uri: window.location.origin
  });
};
```

This sends the page to `https://dev-5xmirf9t.auth0.com/login?/state=....`. Where the user signs in and is then redirected back to the URL they came from with two query params `code` and `state` that look something like this:

```bash
http://localhost:8000/?code=42asdygjkah24ftyas&state=jahsdgjhgasd654123asdtfuyASTF%3D%3D
```

When the frontend sees a URL that contains both `code=` and `state=` is runs the `auth0.handleRedirectCallback()` function in order to process those values and update the `auth0` client with that information before updating the UI with `updateUI()`.

```js {linenos=table,hl_lines=[5,7],linenostart=37}
window.onload = async () => {
  await configureClient();
  updateUI();
  const query = window.location.search;
  if (query.includes("code=") && query.includes("state=")) {
    // Process the login state
    await auth0.handleRedirectCallback();
    updateUI();
    // Use replaceState to redirect the user away and remove the querystring parameters
    window.history.replaceState({}, document.title, "/");
  }
};
```

Whenever `updateUI()` is called this checks if the user is logged in with the `auth0.isAuthenticated()` method that is provided by the `auth0-spa-js` library. This allows it to go back over all the HTML, and determine if the hidden elements should be revealed and which of the login/logout buttons should be disabled or enabled.

```js {linenos=table,hl_lines=[2,"4-5"],linenostart=15}
const updateUI = async () => {
  const isAuthenticated = await auth0.isAuthenticated();

  document.getElementById("btn-logout").disabled = !isAuthenticated;
  document.getElementById("btn-login").disabled = isAuthenticated;

  if (isAuthenticated) {
    document.getElementById("gated-content-1").classList.remove("hidden");
    document.getElementById("gated-content-2").classList.remove("hidden");

    const claims = await auth0.getIdTokenClaims()
    const pictureUrl = claims.picture
    
    document.getElementById("avatar-img").src = pictureUrl || 'https://icon-library.net/images/icon-of-music/icon-of-music-8.jpg';
    document.getElementById("avatar-img-div").classList.remove("hidden")

  } else {
    document.getElementById("gated-content-1").classList.add("hidden");
    document.getElementById("gated-content-2").classList.add("hidden");
  }
};
```

If the user is authenticated then `auth0.getIdTokenClaims()` will get general information about the user including a profile picture if present. Otherwise it will fallback to an image of a music note and set that as a profile image before revealing the profile avatar image div. You can test this out yourself and examine the result of `auth0.getIdTokenClaims()` in the inspector console of your web browser after signing in:

![Screenshot of the JS console after console logging the token claims](/images/serverlessjams/get-token-claims.png)

This helps us update the UI with any relevant profile information, but we also need to be able to eventually authenticate our frontend user to the backend. To do this, we'll need a JSON Web Token that we get from running `auth0.getTokenSilently()`:

![Screenshot of the JS console after console logging the id token](/images/serverlessjams/get-token-silently.png)

Your token should look similar and should have three parts separated by a period. To make sure that you're set up correctly you can copy and paste your token into a site like [jwt.io](https://jwt.io/). You can scroll down a bit and use the JWT Debugger on the homepage:

![Screenshot of using jwt.io to debug a JSON Web Token](/images/serverlessjams/jwt-debugger.png)

You should see two audiences here in the `aud` property as shown in the image:

- The `serverlessjams-vote-api` which is the API Identifier we set earlier and
- The `/userinfo` URL, which will allow us to get the profile information that we were looking at in the token claims on the backend

If these are both present this token is exactly what we need in order to process the backend API requests! Next, we can deploy our backend, and then make one final update to the frontend in order to get it working with the backend API endpoints.

## Deploying Our Backend

Now that we're ready to deploy our backend let's take a closer look at each part of it!

The `serverless.yml` is the core configuration for any Serverless Framework service. In this case, we're going to use it to configure all the API Endpoints, backing Lambda functions, the authorizer for the protected API endpoint and the DynamoDB table used by the application. My Serverless Learn courses [here](https://serverless.com/learn/courses/serverless-for-frontend-developers/) would also take you through the process of configuring a custom domain for the frontend and teaching you how to deploy it.

So let's take a look!

### Configuring the Serverless Dashboard

The first few lines configure this service with a [Serverless Dashboard](https://dashboard.serverless.com) account to monitor and debug with. 

```yaml
org: yourorg
app: serverlessjams
service: serverlessjams
```

If you want to create one you can go to [dashboard.serverless.com](https://dashboard.serverless.com).* If you decide you don't want to create the account, that's fine! Just remove the `app` and `org` lines from `serverless.yml` and keep going.

*Disclaimer - While I've been using the Serverless Framework for years, as of the publication of this post I currently work at Serverless Inc. (the folks who make the Serverless Dashboard). 

### Configuring the `version` and `provider` Details

Next, I've just made sure to specify a framework version in case you're trying to use a version which might not ve supported: 

```yaml
frameworkVersion: ">=1.53.0 <2.0.0"
```

After that, I have the provider section. In this I specify that I'm working with AWS, using Python as my function runtime and tell the service which region I'll be deploying into.

```yaml
provider:
  name: aws
  runtime: python3.7
  region: us-east-1
  environment:
    DYNAMODB_TABLE: serverlessjams-voteCounts
    AUTH0_DOMAIN: EXAMPLE_REPLACE_ME.auth0.com
    AUTH0_API_ID: EXAMPLE_REPLACE_ME
  iamRoleStatements:
    - Effect: Allow
      Action:
        - dynamodb:Scan
        - dynamodb:PutItem
        - dynamodb:UpdateItem
      Resource: "arn:aws:dynamodb:${opt:region, self:provider.region}:*:table/${self:provider.environment.DYNAMODB_TABLE}"
```

You'll also notice the `environment` section above has a few different values we need to replace. The `AUTH0_DOMAIN` and `AUTH0_API_ID` will be used by our backend in order to process the token coming in from the user's API calls so we need to include the same domain and API Identifier we used earlier:

```yaml
  environment:
    DYNAMODB_TABLE: serverlessjams-voteCounts
    AUTH0_DOMAIN: dev-5xmirf9t.auth0.com
    AUTH0_API_ID: serverlessjams-vote-api
```

Make sure to save after you update that! 

Then, there is the `iamRoleStatements` section. In this section we are giving permission to our service to take action on a DynamoDB table that we're going to create further down in the `Resources` section of `serverless.yml`.

```yaml
  iamRoleStatements:
    - Effect: Allow
      Action:
        - dynamodb:Scan
        - dynamodb:PutItem
        - dynamodb:UpdateItem
      Resource: "arn:aws:dynamodb:${opt:region, self:provider.region}:*:table/${self:provider.environment.DYNAMODB_TABLE}"
```

This will give the service permissions to run the `Scan`, `UpdateItem` and `PutItem` operations on the `serverlessjams-voteCounts` table that we'll be creating. The syntax after the `Resource` in the IAM statement is to grab the right region and table name.

### Configuring Functions and API Endpoints

Next up, we have to setup all the configuration so the Serverless Framework knows how to setup our API Endpoints:

```yaml
functions:
  auth:
    handler: backend/auth.handler
  recordSongVote:
    handler: backend/record_song_vote.handler
    events:
      - http:
          path: song/vote
          method: post
          authorizer: 
            name: auth
          cors: true
  getSongVoteCounts:
    handler: backend/get_song_vote_counts.handler
    events:
      - http:
          path: votes
          method: get
          cors: true
```

There are three separate functions above that we're creating.

- A Lambda Authorizer function called `auth` that will sit in front of the protected API Endpoint
- A Lambda Function called `recordSongVote` that will vote on songs
  - This function is configured with an `authorizer` - the `auth` function
  - It also creates a POST API endpoint with the path of `song/vote`
- A Lambda Function called `getSongVoteCounts` that will fetch the votes for all songs
  - This function hasa GET API endpoint with a path of `votes`
  - This function does not have the `authorizer` configuration because we want even non-users to be able to see vote counts 

Let's look at each in more detail.

#### Looking at the `auth` Function

The purpose of the `auth` function is to generate either an allow or deny policy depending on if the incoming request has a valid token and has verified their email address. First, it loads up dependencies:

```python
import os
import requests

from get_token import get_token
from verify_token import verify_token

AUTH0_DOMAIN = os.environ.get("AUTH0_DOMAIN")
```

Then, it defines a handler function that receives the incoming event from any API request that it is configured for.

```python
def handler(event, context):
    print(event)
    print(context)
    token = get_token(event)
    id_token = verify_token(token)
    print(id_token)
    userinfo = requests.get(
        'https://' + AUTH0_DOMAIN + '/userinfo', 
        headers={"Authorization": "Bearer " + token}
    ).json()
    if id_token and userinfo['email_verified']:
        policy = generate_policy(
            id_token['sub'], 
            'Allow', 
            event['methodArn']
        )
        return policy
    else:
        policy = generate_policy(
            id_token['sub'],
            "Deny",
            event['methodArn']
        )
        return policy
```

The `get_token` function is used to get the unverified token from the incoming header and confirm that it matches the expected format. Essentially, it makes sure that the value coming in looks something like this:

```js
Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6Ik5VWkVOalJGT0VFeE9EVkdNRFJEUTBSQk5UUTNNalUwUTBNeVJETXdOVGhCTmpJek9UTkdPQSJ9.eyJpc3MiOiJodHRwczovL2Rldi01eG1pcmY5dC5hdXRoMC5jb20vIiwic3ViIjoiZ29vZ2xlLW9hdXRoMnwxMTgzMjI2Mzk5MjQxODU1MDA2MjciLCJhdWQiOlsic2VydmVybGVzc2phbXMtdm90ZS1hcGkiLCJodHRwczovL2Rldi01eG1pcmY5dC5hdXRoMC5jb20vdXNlcmluZm8iXSwiaWF0IjoxNTc4ODY3ODA2LCJleHAiOjE1Nzg5NTQyMDYsImF6cCI6IlNJM2dUTmFnajBRVmp6QlZRaHdVaE9MU3pRQjAxYjVFIiwic2NvcGUiOiJvcGVuaWQgcHJvZmlsZSBlbWFpbCJ9.UWF3XyeFbh05H1TWp5BRaZeA-pFt1a7uLdpf6mkixqucOM0Z_B-WtJTK2lgw_mdh1j6CDlnEWhBtOfdcF2_92z-DZRu5apkQjsPA-sbnWJbcL1YVGRZduuE4KuuNhUgQTce42IDiGRfEm-Wqqpfn5-EWp0nN0Mgl1WENCDNmy35iM0CHouhpfDR4f7VYI3yQJCnlJT3MEbCoZoKE89QY2yHzJL7BbvX2FbFEWIBMAwU9D0-m2hi2dkBO9rnGEVY0jF5vRv77qB2KjSbUzLtVAPGhiX11ZheRomqdrM_aWq7aVqD6_m5OLSfrVB-XTkuAC3n7aS6jg10_HhRUJXozCw
```

If the format is wrong it logs and error and raises an error saying the request is Unauthorized: `raise Exception('Unauthorized')`.

If the token has the correct format then the `verify_token` attempts to verify it.

#### Looking at the `recordSongVote` Function

#### Looking at the `getSongVoteCounts` Function

### Adding our DynamoDB Table Resource



### CHANGE DATE TO 15th
### CHANGE DATE TO 15th
### CHANGE DATE TO 15th
### CHANGE DATE TO 15th