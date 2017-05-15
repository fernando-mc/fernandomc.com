+++
publishdate = "2017-04-30T18:37:02-07:00"
date = "2017-04-30T18:37:02-07:00"
title = "Using SSH Keys with Multiple GitHub Accounts"
draft = true
Description = ""
Tags = [
  "Github",
  "ssh"
]
Categories = [
  "Github",
]
menu = "main"

+++


1. 

Ssh key setup

1. Username on github
2. SSH config
3. git config in local repos
4. How does it know what username to use? - Check if the changed email makes it different

Managing multiple Github accounts can be annoying. Especially if you want to keep commits from your work and personal accounts distinct and don't want to have to keep changing permissions on one repo or the other to allow you to commit from alternate user accounts.

If you're managing multiple Github accounts here's how you can use different SSH keys and some SSH and git configuration to always commit from the right account by default. (This will be focused on Unixy systems, sorry Windows folks!)

Here's what we're going to do:

1. Create two different SSH Keys
2. Configure our SSH Keys to allow us to interact with GitHub with no username/password shenangins.
3. Show how to change local repositories to work with differnt user acconts

*Create Your SSH Keys*

The first thing you'll need to do is create two SSH Keys. One for each GitHub user that you'll be using.

You can create a SSH key by opening Terminal and entering this command:

```bash
ssh-keygen -t rsa -b 4096 -C "example@example.com"
```

Replace `example@example.com` with your GitHub email address.

When you see "Enter a file in which to save the key," press Enter. This accepts the default file location.


```
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
    ignorecase = true
    precomposeunicode = true
[remote "origin"]
    url = git@github.com-fernando-mc:fernando-mc/serverless-node-cron.git
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
    remote = origin
    merge = refs/heads/master
[user]
    name = Fernando Medina Corey
    email = fmcorey@gmail.com
```

1. Outline post
2. Write Post
3. Upload and add tags and categories
4. Check publish date and date
5. Add all the other front matter
6. Check summary


Some content for a post
<!--more-->

The rest of the content