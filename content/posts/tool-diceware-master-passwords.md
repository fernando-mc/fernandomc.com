+++
menu = "main"
layout = "post"
date = "2016-04-15T00:50:09-05:00"
title = "A Tool for Diceware Master Passwords"
Description = ""
Tags = [
  "Passwords",
  "Diceware"
]
Categories = [
  "Security"
]

+++

I'm announcing my first attempt at a tool to help with Diceware master password adoption. I've created a single-page HTML file to quickly generate Diceware passwords from dice rolls. Here is a link to the [source](https://github.com/fernando-mc/EasyDice). And here is the [live project](https://rawgit.com/fernando-mc/EasyDice/master/index.html). You can [tweet](https://twitter.com/fmc_sea) at me if you have suggestions, improvements, or requests. 

![Newer Version of Easy Dice](/images/diceware-new.png)

Here are the goals I had in mind:

**This should be simple and intuitive for the end user**

Technology is notorious for complexity. Unfortunately this means that there is a real educational challenge when it comes to helping users adopt new security measures.  To make something that is as frictionless as possible for the end-user I decided to put all code in one .html file. While this is poor practice in terms of technical style, it means the user only has to download and open one file. There are currently a few external references to Bootstrap for styling purposes, but I will also be working to make style fallbacks so that it remains useable when used offline.

In addition, the code is straightforward enough that a technical individual can review it quickly despite the internal CSS and JavaScript.

**It should promote and enforce strong security practices**

I _could_ have saved myself some work and allowed the user to generate a password with less than 6 words. But I want to provide a tool to promote good security choices to every extent it can. The user can still truncate the final password intentionally but the [defaults matter](http://www.nytimes.com/2011/10/16/technology/default-choices-are-hard-to-resist-online-or-not.html).

I'm not using random number generators primarily because I want users to be responsible for entropy via the namesake method - dice. Additionally, while I have heard some good things about in-browser crypto, pseudo-random number generators aren't my forte.

**It should look pretty**

I'll be the first to admit my sense of style is lacking. If you don't believe me look at earlier versions of the project:

![Old Diceware](/images/diceware-old.png)

That's why I enlisted the help of [@_sandramedina](https://twitter.com/_sandramedina) to help me in that ordeal.

Hopefully with these goals in mind and the first version of the project out the hardest thing about Diceware will be finding dice. On that note... if anyone knows a casino dice maker that wants to become the "Official Sponsor of Cryptographically Secure Passwords" let me know!