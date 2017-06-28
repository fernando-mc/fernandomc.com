+++
Description = ""
Tags = [
  "AWS",
  "Scripts"
]
Categories = [
  "AWS",
  "Scripts"
]
menu = "main"
publishdate = "2017-06-03T22:23:54-07:00"
date = "2017-06-03T22:23:54-07:00"
title = "New AWS Profile Switcher"

+++

![Example of AWSPS in action](/images/awsps/awsps_demo.png)

I [recently wrote](https://www.fernandomc.com/posts/script-change-aws-profiles/) a script that switches your AWS default profile. After coming back to it a few weeks later I realized I could make it more elegant by relying directly on the ConfigParser library to modify the configuration instead of doing a line by line search and edit.

<!--more-->

Here's the improved version of the script itself which is still compatible with the command line function I mentioned in the [earlier post](https://www.fernandomc.com/posts/script-change-aws-profiles/): 

<script src="https://gist.github.com/fernando-mc/0024cbff6aa7214e3dd0ff03536e1501.js"></script>

The main reason I prefer this version is because there's less risk of accidentally editing your credentials file with a mistakenly placed line. Instead I outsource the heavy lifting to the ConfigParser library and add in functionality to let you see what profiles you have available.

Let me know what you think!