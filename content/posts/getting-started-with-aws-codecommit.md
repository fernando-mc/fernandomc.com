+++
Description = "A guide on how to start using AWS CodeCommit for your source control needs."
Tags = [
  "AWS",
  "Git",
  "CodeCommit",
  "Source Control",
]
Categories = [
  "AWS",
  "DevOps",
]
title = "Getting Started with AWS CodeCommit"
publishdate = "2020-10-10T10:11:51-07:00"
date = "2020-10-10T10:11:51-07:00"
type = "blog"
sponsor_message = "You might like my courses on Pluralsight too! Sign up for a free trial to learn more about DevOps on AWS!"
sponsor_link = "https://pluralsight.pxf.io/mA1DD"
sponsor_footer = "You can take my recently published [Pluralsight course](https://www.pluralsight.com/courses/continuous-delivery-automation-aws-devops-engineers) for free with [this trial link](https://pluralsight.pxf.io/mA1DD) to learn more about AWS CodeCommit and how it integrates with other AWS DevOps tooling."
[image]
    feature = "/images/getting-started-aws-codecommit/getting-started-with-aws-codecommit.png"
    postheader = "/images/abstract-new-1.png"
+++

One of the first things you need to do when starting a new development project is to determine where your code is going to be stored. If you're using Git you've probably heard of options like GitHub or BitBucket. But in this tutorial I'll show show you how to leverage AWS CodeCommit to handle source control for your projects. 
<!--more-->

## Requirements and Assumptions

In this tutorial I'll assume that you already have:

- An AWS Account
- The AWS CLI installed and configured
- Git installed on your machine
- Some basic familiarity with Git - but if you want a quick refresher you can read [my guide here](https://fernandomc.com/posts/getting-started-with-git/).

# Create the CodeCommit Repository

The first thing we'll need to do is to create a CodeCommit Repository. Now I'll be assuming that you have administrator access to your AWS account and that you are signed in as an administrator user. While you could also do some of the following actions as the AWS Account root user

# Create the AWS IAM User

With the repository created we'll now want to create an AWS IAM user that we can get set up with access to the repository. Or, if you already have IAM users who you want to have access to the repository you can update the permissions of those users.

Now for the purposes of working with CodeCommit alone you need to grant access to 
`arn:aws:codecommit:region:account-id:repository-name`

For the purposes of this demo, I'll be working with a "Developer"

# Create the ssh config file on Mac 
# (Follow the instructions provided by AWS for other systems)
touch ~/.ssh/config

# Update the file to look like this:
# Note, that ~/.ssh/id_rsa might need to change 
# depending on your operating system
Host git-codecommit.*.amazonaws.com
User <YOUR-USER-ID>
IdentityFile ~/.ssh/id_rsa

# Update the permissions on the file:
chmod 600 ~/.ssh/config

# Git clone the repository
git clone <your-repository-url>

cd flasky
ls -al

git checkout -b main
touch app.py
git add app.py
git commit -m "add app.py"
git push origin main



2. Check publish date and date
CHANGE BACK TO:
<!-- 
publishdate = "2020-10-22T10:11:51-07:00"
date = "2020-10-22T10:11:51-07:00" -->