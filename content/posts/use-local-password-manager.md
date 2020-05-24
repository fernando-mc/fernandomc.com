+++
menu = "main"
date = "2017-06-10T12:18:20-07:00"
title = "Why Use a Local Password Manager"
publishdate = "2017-06-10T12:18:20-07:00"
Description = ""
Tags = [
  "Security",
  "Password Managers",
]
Categories = [
  "Security",
]

+++

In the last few months there's been a number of online password manager bugs that have made headlines. You might have seen recent [reports](https://www.theguardian.com/technology/2017/mar/30/lastpass-warns-users-to-exercise-caution-while-it-fixes-major-vulnerability) of a major vulnerability in LastPass, one of the most popular cloud-hosted password managers.

Even more recently then that was the breach of [OneLogin](https://www.wired.com/2017/06/security-news-week-onelogin-one-bad-breach/), a vendor for single sign on management.

While I wholeheartedly believe that online password managers can be great tools for improving your security, it's also important to recognize that they do have potential drawbacks.

To avoid these drawbacks and still get the benefits of a password manager, let's take a look at some local password managers.

<!--more-->

Local password managers provide much of the same password creation, storage, and management options that you typically see in cloud-based providers. The main exception being that rather than having your passwords stored in the cloud, you store your passwords in a local file that doesn't leave your computer.

This eliminates a large number of attack vectors to gain access to your passwords. It is still important to note that

Here are a few options and how you can get started with them:

[KeePassX](https://www.keepassx.org/) or [KeePassXC](https://keepassxc.org/) are both good options. XC is a reboot of KeePassX that is much more actively maintained and developed on. Both offer support for Mac, Linux and Windows. They both also have a file format that is compatible with the [KeePass](https://keepass.info) password manager.

![KeePassXC](/images/use_local_password_manager/keepassxc.png)

[KeePass](https://keepass.info) is another option but is only supported on Windows operating systems.

![KeePass](/images/use_local_password_manager/keepass.png)

All of these tools give you the option to sort, search and store your passwords in a secure manner. They all use an encrypted password file which is the encrypted version of all your passwords.

I've used each of these in the past, but finally migrated over to KeePassXC because it is cross-platform and actively maintained. This means that I can use a single password file across all my devices and operating systems.

You can read the source code or contribute to development yourself on [Github](https://github.com/keepassxreboot/keepassxc/).