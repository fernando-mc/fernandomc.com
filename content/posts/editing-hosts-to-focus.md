+++
Description = "Editing your hosts file to stay focused and block social media distractions"
Tags = [
  "Windows",
  "Productivity",
  "OS X",
  "OSX",
  "Linux"
]
Categories = [
  "Productivity",
]
title = "Editing Your hosts File to Help Focus"
publishdate = "2018-09-13T22:28:01-07:00"
date = "2018-09-13T22:28:01-07:00"
[image]
    feature = "/images/twitter-block.png"
+++

I've recently decided to scale back some of my social media and other time-wasting activities.

Initially I was trying to do this with browser plugins to block specific sites. Unfortunately, I was too clever for my own good and removed those plugins or opened incognito windows whenever I wanted to, say watch Youtube or browse Twitter. 

So I tried to think about how I could make this even harder for myself and decided to change my `hosts` file to redirect urls to another IP effectively blocking all my site vices. Here's how I did it.

<!--more-->

The `hosts` file is a plain text file that maps hostnames to IP addresses. You might be thinking "but I thought DNS typically takes care of this for us?". If you did think that you would be right! When we visit a URL in the browser usually DNS servers are responsible for pointing us to the IP address of the machine running the website. But we can actually skip DNS entirely by adding an entry for a particular domain to the hosts file first. Let's try this now.

On Windows 10, my hosts file is located at: C:\Windows\System32\drivers\etc\hosts

On other operating systems you will find your hosts file in a different spot. OSX and Linux systems should have the hosts file located at `/etc/hosts`.

Because the hosts file is so sensitive you will have to open it up with a program that is running as an administrator or super user. 

On OSX/Linux you can do that with `sudo nano /etc/hosts` or `sudo vim /etc/hosts`. For windows I just right-clicked my favorite text editor and ran it as an administrator and then opened the file from within that program.

Once you do that you should be able to scroll to the bottom of the file and add a few entries that look like this:

```
0.0.0.0       linkedin.com
0.0.0.0       www.linkedin.com
0.0.0.0       twitter.com
0.0.0.0       youtube.com
0.0.0.0       www.youtube.com
```

On the left I'm using the 0.0.0.0 IP and on the right I'm saying go to that IP whenever I try to visit any of those websites. This blocks me from visiting any of the websites listed. If you want to add a new website to the blocklist you can just add a new line with `0.0.0.0` at least one space and then the `url-you-want-to-block.com` after that. You will have to list subdomains like www separately from the bare domains. 

If you're curious, the IP of 0.0.0.0 is basically used to designate an invalid target address. So basically I'm trying to redirect Twitter, LinkedIn, YouTube and company to nothing.

In 2017 I stopped reading articles and started [reading books](/reading-list). I'm hoping that this will help me write more tutorials for those looking to learn. 