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
  "Python",
]
menu = "main"
publishdate = ""
date = "2017-03-30T20:51:04-07:00"
title = "Python Script to Change AWS Profiles"

+++

I frequently find myself switching between AWS profiles, especially when I'm home from work switching between side projects on different AWS accounts. I got tired of opening and editing my credentials file manually so I wrote a Python script and a Bash function to do it for me. You can use it too! 
<!--more-->

My AWS profiles in the ~/.aws/credentials file look a little like this:

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

IMPORTANT:

- It's a really lazy script, and it expects your credentials file to be formatted identically to mine.
- Back up your credentials file if you're worried (but this script does it for you in a backup_credentials file right next to the file)

Here's the code to put in your .bash_profile or .zsh_profile or .whatever_profile to call the script:

```bash
awsps()
{
    cd ~/.aws
    echo swapping to $1
    python aws_default_profile_swap.py $1
    cd -
}
```

`awsps` is short for AWS profile swap. After saving the file, be sure to `source .your_profile` where `.your_profile` is the one you edited.

Now you can get the script. The most recent version of the script can be found on [my github](https://github.com/fernando-mc/scripts/blob/master/aws_default_profile_swap.py). It's pretty well commented so take a look over everything. 

Open up your terminal and you can grab it with:

```bash
wget https://raw.githubusercontent.com/fernando-mc/scripts/master/aws_default_profile_swap.py
```

Once you're done, copy the script to the same directory as your AWS credentials file. This will (probably) do it for you.

```bash
cp aws_default_profile_swap.py ~/.aws/aws_default_profile_swap.py
```

The basic usage once you've done all the setup would be `awsps fernando`, where `fernando` is the name of the profile you'd like to switch to. If this works you should see something like this:

```bash
swapping to fernando
Success - Swapped profile to fernando
```

You can also restore your profile from the backup with `awsps restore`. 

Questions? Let me know on [Twitter]({{% my_twitter %}}). 

Ideas for code changes? Open a [Pull Request](https://github.com/fernando-mc/scripts/)