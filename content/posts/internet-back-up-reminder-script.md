+++
Tags = [
  "Internet",
  "Ping",
  "Scripts",
  "Fun",
]
Categories = [
  "Scripts",
]
menu = "main"
date = "2017-02-02T08:37:07-05:00"
title = "The Internet Is Back up - a Reminder Script"
draft = false
Description = "What do you when the internet goes out? Write a bash script to notify you when it's back up!"

+++

ARGGG! The internet went out. What to do while I wait? I know! Write a bash script to let me know when the internet is back up!

<!--more-->

Line by line starting after the 'Begin ping':

0. Starts a for loop with 1000 loops.
1. Tries to ping google (-q)uietly for a response and only sends a (-c)ount of 1 packet as a test
2. It then uses the built in OS X voice ‘Zarvox’ to inform me I need to get back to work
3. After that it echos to the terminal that the internet has come back
4. And pipes the result of an echo command to the mail command which sends a text message to my phone with t-mobiles mail-to-text address. (Most carriers have some version of this).

``` bash
#!/usr/bin/env bash
echo 'Begin ping'
for i in {1..1000}
do
       if ping -q -c 1 www.google.com
       then
       say -v Zarvox 'The internet has returned. Back to work mortal.'
       echo "The internet has returned"
       echo "The Internet is Back" | mail -s "Internet Is Back" 5555555555@tmomail.net
       exit
       fi
       sleep 5
done
```

I hope you never have to use it!

Have questions? Feel free to leave them below!