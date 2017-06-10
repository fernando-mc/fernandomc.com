+++
publishdate = "2017-07-20T18:37:02-07:00"
date = "2017-07-20T18:37:02-07:00"
title = "Using SSH Keys with Multiple GitHub Accounts"
Description = ""
Tags = [
  "Github",
  "ssh",
  "workflow",
  "git",
  "passwords"
]
Categories = [
  "Github",
]
menu = "main"

+++

**NOTE TO SELF**
Would be a good post but basically impossible to test completely on my machine as it is setup already. Postpone until I get another machine to test on.
**NOTE TO SELF**


Managing multiple Github accounts can be annoying. Especially if you want to keep commits from your work and personal accounts distinct. Additionally, it's a pain if you have to keep checking which accounts use which credentials.

If you're managing multiple Github accounts here's how you can use different SSH keys and some initial SSH and git configuration setup to:

1. Always commit from the right account by default and
2. Never have to enter your username and password credentials again!
 
<!--more-->

(This will be focused on Unixy systems, sorry Windows folks!)

**NOTES**
1. Username on github
2. SSH config
3. git config in local repos
4. How does it know what username to use? - Check if the changed email makes it different
**NOTES**

Here's what we're going to do:

1. Create two different SSH Keys
2. Configure our SSH Keys to allow us to interact with GitHub without having to enter our username or password
3. Show how to change local repositories to work with differnt user acconts by default

**Create Your SSH Keys**

The first thing you'll need to do is create two SSH Keys. One for each GitHub user that you'll be using.

You can create a SSH key by opening Terminal and entering this command:

```bash
ssh-keygen -t rsa -b 4096 -C "example@example.com"
```

In both cases, replace `example@example.com` with your GitHub email address and when you see "Enter a file in which to save the key," name it something distinct such as github_work for your work key and github_personal for your personal github ssh key. If you have any issues with this step, you can also take advntage of the [Github documentation](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) for it.

You'll also want to enter a secure randomized password created by your [password manager](https://www.fernandomc.com/posts/user-local-password-manager/).

To see the results of this process you can list the contents of your .ssh directory with `ls ~/.ssh`. Amoung the listed contents you should see some entries like this:

```bash
github_personal      github_work
github_personal.pub  github_work.pub
```

This process will create both a private and public key. The public key has the ending `.pub`. The private key has no extension. Alongside the SSH keys you just created you might see a few other files like `id_rsa`, `id_rsa.pub` (the default key names when creating ssh keys) or `config` (the SSH configuuration file).

If you already see a `config` file in your `~/.ssh` folder then open it now in a text editor.

You may already have entries in your config file. If you do, try not to change those unless they're specific to Github, in which you wil need to make some changes. We'll be adding or modifying our SSH config as it is relevant to our Github SSH keys.

Now that we have our two keys we need to tell our SSH config when they should be used. This will allow it to pick which key to use when a Github repo is assocaited with a different Github account. To do this we will add two entries, one for our work account and one for our personal account. The naming convention here is just for clarity and you can change the names and comments to reflect your use case. You can also add more than two of these Host entries. 

```bash
# Inside ~/.ssh/config

# Work Github Account
Host github.com-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/github_work

# Personal Github Account
Host github.com-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/github_personal
```

There are a few important things to note from your ssh config file.

The first is the Host value of `github.com-work` and `github.com-personal`. You'll need these values to configure the git repos you'd like to push directly to later.




```bash
# config

# Work Github Account
Host github.com-curenando
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa

# Personal Github Account (work computer)
Host github.com-home
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_fmcorey
```

Confirm this works with serverless node cron repo

Git settings
```
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
    ignorecase = true
    precomposeunicode = true
[remote "origin"]
    url = git@github.com-home:fernando-mc/serverless-node-cron.git
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
    remote = origin
    merge = refs/heads/master
[user]
    name = Fernando Medina Corey
    email = fmcorey@gmail.com
```
3. Upload and add tags and categories
4. Check publish date and date
5. Add all the other front matter
6. Check summary
