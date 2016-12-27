+++
draft = true
title = "Python C Libraries and AWS Lambda"
Description = ""
Tags = [
  "Serverless",
  "Python",
  "AWS",
]
Categories = [
  "",
  "",
]
menu = "main"
date = "2016-12-27T14:08:12-05:00"

+++

Recently I was trying to use the Pandas and Psycopg2 libraries for Python in combination with AWS Lambda. My first hint that this was probably overkill was that the function package, when zipped, started exceeding 50MB. I quickly found out that 50MB is the size limit for Lambda packages. Whoops. 

After correcting a few mistakes (my virtualenv bundled in with my dependencies - don't ask) and trimming some dependencies I started testing again.

A new error - Progress! 

This time the error was hinting that my libraries weren't able to load. What? I'd tested locally so I knew the libraries were working properly on my OS X Machine when I'd installed them within the virtual environemnt and even when I had the zip package unzipped and tested locally in a new folder. Strange...
th
Let's see what the internet has to say.

Turns out that a few other folks had experienced the error. The issue crops up whenever dealing with Python libraries that aren't purely written in Python and have to be built at installtion on the host machine. The issue here was that my Pandas was different from the Pandas on a Ubuntu machine, and that Pandas might be different than an Amazon Linux Machine.

Here's a few solutions:

- You can (with a full knowledge ofthe irony) use an EC2 Instance to install dependencies for your serverless function and zip the file up there before uploading it to Lambda. The irony is a killer here but it might be the easiest solution if you don't have to change the functions frequently.

- You can use only pure Python libraries. This isn't so bad if you can find an easy substitute. I didn't really need pandas or psycopg2 because my function was doing some simple queries against AWS Redshift. I opted to substitute these for another library - pg8000. Let's face it, with a name like that could I really afford not to try it?

- You can _try_ using a linux machine and seeing if the code compiles properly. But I'm not sure how this will different on Ubuntu vs. Debian vs. an actual Amazon Linux Machine and I was too lazy to find out.

- You can abandon the hype around serverless solutions and run this directly in a small EC2 instance. But that's not really a solution.

```This is a new test```

Do you have a better solution? Let me know on Twitter and I'd love to hear it. (@fmcorey).
