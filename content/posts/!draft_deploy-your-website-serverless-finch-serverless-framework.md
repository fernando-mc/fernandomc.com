+++
draft = false
Description = "Learn how to deploy your website to AWS S3 using Serverless Finch and the Serverless Framework"
Tags = [
  "Serverless",
  "Static Sites",
  "AWS",
  "Serverless Framework",
  "Serverless Finch",
  "S3",
]
Categories = [
  "AWS",
  "Serverless"
]
title = "Deploy Your Static Site to AWS S3 with Serverless Finch and the Serverless Framework"
menu = "main"
date = "2018-01-25T14:08:12-05:00"
publishdate = "2018-01-25T14:08:12-05:00"

+++

A frequent issue I ran into when working with the [Serverless Framework](https://www.serverless.com) on smaller projects was that when I wanted to deploy a static website frontend that integrated with my Serverless backend I wasn't quite sure how to do it. After some searching, I [adopted and republished](https://www.fernandomc.com/posts/publishing-serverless-finch/) a broken Serverless Framework plugin for that exact purpose.

And so Serverless Finch was born.

Now it's relatively easy to deploy your own website to AWS S3 while using the Serverless Framework and Serverless Finch. Here's how you can do it.

<!--more-->

**Some Assumptions**

I'm going to assume that you have used the Serverless Framework before and already have it installed globally. If you have questions with anything in that realm you can always head over to [their website](https://www.serverless.com) to read up on their documentation.

I'm also going to assume that you're all setup with the AWS CLI and credentials you need for it to work. There are various permissions that Serverless Finch (and the Serverless Framework itself) require to function properly so make sure you are good to go there too.

**Let's Get Started**

Starting in a directory with your Serverless Framework project go ahead and run:

```
npm install --save serverless-finch
``` 

This will install the plugin for you so that you can use it in combination with the Serverless Framework.

Next, you need to update your `serverless.yml` configuration file so that it includes some Serverless Finch configuration values - Here's an example of what you would add.

```yaml
plugins:
  - serverless-finch

custom:
  client:
    bucketName: unique-s3-bucketname-for-your-website-files
    distributionFolder: client/dist # (Optional) The location of your website. This defaults to client/dist
```

I'll go through each part of this for you:

- First, you'll add the plugin under the plugins section.
- Next you'll add a 'client' value under custom. This is the configuration details for your web client (what we're deploying)
- The bucketName value should be an S3 bucket that doesn't exist yet. **WORD OF WARNING** When deploying your static site serverless-finch will delete everything in the bucket. So do NOT use this with a bucket you store other things in. This S3 bucket should exclusively be for your static website.
- Then you can optionally include the distributionFolder configuration value. This specifies where your website files are located. By default, Serverless finch will look for them in `./client/dist` and will expect to see an index file at `./client/dist/index.html`  and an error file at `./client/dist/error.html`. You can set the distribution folder to something like `mySite` and then Serverless Finch will expect your website to be in `./mySite` with the corresponding index and error files at `./mySite/index.html` and `./mySite/error.html`. 


Now that you have that done, either create the `./client/dist` folder or if you added a custom configuration for distributionFolder then create that folder. If you _already_ have your website files ready to go just make sure you have them in the same top level directory all in that folder.

If you _don't_ have any website files yet you can pretty easily make a site with these commands:

```bash
mkdir -p client/dist
touch client/dist/index.html
touch client/dist/error.html
echo "Go Serverless" >> client/dist/index.html
echo "error page" >> client/dist/error.html```

With your website created or properly moved to the folder we're working in you can deploy your site! Just run `serverless client deploy` and Serverless Finch should take care of the rest.

You can also specify the region you want to dpeloy your website to with the --region flag. For example, if I wanted my site deployed to the us-west-2 Oregon region I could run:

`serverless client deploy --region us-west-2`

And let's say I get tired of my website altogether, I can tear it down with `serverless client remove`.

And that's it! You've used Serverless Finch to deploy your own static website. Serverless Finch is still adding new features and bug squashing so please feel free to contribute [on GitHub](https://github.com/fernando-mc/serverless-finch). If you think you might want to be a more active contributor please [get in touch](https://www.fernandomc.com/contact/)!