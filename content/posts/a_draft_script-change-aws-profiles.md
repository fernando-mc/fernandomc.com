+++
draft = false
Description = ""
Tags = [
  "AWS",
  "Python",
  "Scripts",
  "Bash",
]
Categories = [
  "AWS",
  "Scripts",
]
menu = "main"
publishdate = ""
date = "2017-03-28T20:51:04-07:00"
title = "Python Script to Change AWS Profiles"

+++

I have a bunch of AWS profiles in my ~/.aws/credentials file which looks a little something like this:

```bash
[work-profile]
aws_access_key_id = EXAMPLEASDHASKDASD
aws_secret_access_key = ASDJEXAMPLEYsbclahsd/aDGSHJASD
[work-qa-profile]
aws_access_key_id = ANOTHEREXAMPLEASIDGUYasdgasd
aws_secret_access_key = LASTEXAMPLETHING/asidughhasd
[fernando]
aws_access_key_id = HOWMANYPROFILESDOIHAVEfernando
aws_secret_access_key = tooManyPROfiLESSRSLytoomany/21t76eygudasd
[default]
aws_access_key_id = HOWMANYPROFILESDOIHAVEfernando
aws_secret_access_key = tooManyPROfiLESSRSLytoomany/21t76eygudasd
```

I frequently find myself switching between these, especially when I'm home from work switching between side projects on different AWS accounts. I got tired of opening my credentials file manually, copying and pasting the credentials from my [fernando] profile under [default], and then saving and going back to what I was doing elsewhere. 

So I wrote a Python script and a Bash function to do it for me. You can use it too! 

IMPORTANT:
- It's a really lazy script, and it expects your credentials file to be formatted identically to mine.
- Back up your credentials file if you're worried



```bash
awsps()
{
    read profile
    cd ~/.aws
    echo swapping to $profile
    python aws_profile_swap.py $profile
    cd -
}
```

And a Python 2.7 script to go in your ~/.aws/aws_profile_swap.py folder


1. Outline post
2. Write Post
3. Upload and add tags and categories
4. Check publish date and date
5. Add all the other front matter
6. Check summary


Some content for a post
<!--more-->

The rest of the content