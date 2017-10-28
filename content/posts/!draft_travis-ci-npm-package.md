+++
draft = true
Description = "Learn how to automatically push new versions of your NPM package using Travis CI"
Tags = [
  "Travis CI",
  "CI CD",
  "Continuous Integration",
  "Continuous Development",
  "GitHub",
  "NPM",
  "serverless finch"
]
Categories = [
  "Devops"
]
title = "Automatically Deploying New Versions of NPM Packages with Travis CI and GitHub"
menu = "main"
date = "2017-11-01T00:50:09-05:00"
publishdate = "2017-11-01T00:50:09-05:00"

+++

0. GET SOME IMAGES in (Feedly, RSS, etc use them and need em in there at first publish)
1. Outline post
2. Write Post
3. Upload and add tags and categories
4. Check publish date and date
5. Add all the other front matter
6. Check summary


Let's say you've already [created](https://docs.npmjs.com/getting-started/creating-node-modules) and [published](https://docs.npmjs.com/getting-started/publishing-npm-packages) your first npm package (Yay!). But now you have to maintain it and maybe add new functionality. You can do all this and [manually publish](https://docs.npmjs.com/getting-started/publishing-npm-packages) new versions of the package, or you can use a tool like Travis CI to automate the deployment process to npm automatically when you push new versions of the package to GitHub. Here's how to get started.

<!--more-->

For this example we'll be looking at the `serverless-finch` package which is a [Serverless Framework](https://www.serverless.com) plugin [I maintain](https://www.fernandomc.com/posts/publishing-serverless-finch/) to provide an easy way to create static sites on AWS S3.

**Setting up Travis CI**

The first thing you'll need to do is visit the [Travis CI](https://travis-ci.org) website and connect whatever GitHub account has the repo of the package you're working with.

After you connect your GitHub account you will also need to enable Travis to make builds from the specific repository you're trying to setup. 

PICTURE OF THE REPO ON/OFF Button slider and MULTIPLE REPOS

After you tell Travis to pay attention to your repo by flipping this on Travis will probably tell you you're still missing something

SCREENSHOT MISSING TRAVISYML

You'll need to create your own `.travis.ylm` configuration file in order to tell Travis what to do with your repo when it sees that changes have been made. Here's what my configuration file looks like for serverless finch.

```
language: node_js
node_js: lts/*
deploy:
  provider: npm
  email: mynpmemail@gmail.com
  api_key:
    secure: a_super_long_encrypted_api_credential/asduyihjopi12345stuff
  on:
    tags: true
```

Let's break this down.

CHECK THIS PART
- The `language: node_js` just tells Travis what language to expect the code to be written in.  
- `node_js: lts/*` means "Use the latest long time stability version of node"
CHECK THIS PART

- The `deploy` section gives Travis all the information it needs to deploy the node package for us.
- It uses the provider of `npm` to know we're deploying this to (surprise!) npm.
- The `email` it uses the the email address I have associated with my personal npm account.
- the `api_key` section uses an encrypted value under `secure` that is ddecrypted by Travis at runtime in order to deploy to npm. (More on this in a moment!)
- The `on:` section determines when it should actually deploy the package. Do we want it to deploy on every commit to a specific branch? What specific conditions need to be met to trigger a deploy? In this case I use `tags: true` to make sure travis only ep

**Changes and Versioning**

Recently, I had a request come in asking for added functionality to remove the site after it had been published. The requester also provided a PR which did exactly what they wanted. After thinking about this I realized it was a perfect time to get setup with Travis to automatically deploy new versions of this package to NPM. The first thing I did was to integrate the changes from the PR.

I merged the PR with the new functionality and after bungling around with a few other PRs to update the readme I also remembered to bump the package version with npm. Because this added new functionality and didn't break backwards compatability I considered it a `minor` change for [semver purposes](https://docs.npmjs.com/getting-started/semantic-versioning).

In order to increment the version of the package from v1.1.1 I used `npm version minor -m "Some commit message"` to bump the package to v1.2.0. This does a few things:

1. It updates the relevant files in your git repo including package.json and any other package files
2. It adds a git tag for the version (more on why this matters later)
3. It automatically commits the changes in git
4. With the -m option it also allows you to put in a git commit message

Now, before you do this make sure you're using the correct type of [semver version update](https://docs.npmjs.com/getting-started/semantic-versioning). Your command might end up being `npm version patch -m "just a quick patch"` or even `npm version major -m "new breaking changes yo"`. 

After you've done this **wait for just a minute and don't push these changes**. 

**Push and Autopublish!**

0. Make changes
1. npm version patch/major/minor
2. git push && git push --tags
