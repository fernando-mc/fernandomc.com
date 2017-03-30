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

Here's the basic usage:

```bash

```

Here's the code I put in my .bash_profile to call the script.

```bash
awsps()
{
    read profile
    cd ~/.aws
    echo swapping to $profile
    python aws_default_profile_swap.py $profile
    cd -
}
```

The most recent version of the script can be found on [my github](https://github.com/fernando-mc/scripts/blob/master/aws_default_profile_swap.py)

Here's the code for you!

```python
#!/usr/bin/env python
# Python Script to swap AWS Profile configs

import argparse
import os

HOME_DIR = os.getenv("HOME")

def read_creds_file(filename):
    """Returns the lines of the credentials file if it exists"""
    try:
        with open(HOME_DIR + '/.aws/' + filename, 'r') as curr_creds:
            curr_creds_lines = curr_creds.readlines()
        return curr_creds_lines
    except IOError:
        print 'credentials file does not exist!'

def get_profile_to_swap_to():
    """Parse the profile requested from user input"""
    parser = argparse.ArgumentParser()
    parser.add_argument("profile", help="profile to set as default")
    args = parser.parse_args()
    profile_to_swap_to = args.profile
    creds_string = ''
    for line in read_creds_file('credentials'):
        creds_string += line
    if '[' + profile_to_swap_to + ']\n' in creds_string:
        return profile_to_swap_to
    elif profile_to_swap_to == 'restore':
        return 'restore'
    else:
        raise ValueError("The profile '{0!s}' is not in your credentials file.".format(profile_to_swap_to))

def add_backup_if_none_exists():
    """Adds a backup creds file if none exists"""
    if read_creds_file('credentials') and not os.path.exists(HOME_DIR + '/.aws/credentials_backup'):
        with open(HOME_DIR + '/.aws/credentials_backup', 'w') as backup_file:
            for line in read_creds_file('credentials'):
                backup_file.write(line)

def restore_from_backup():
    backup_file_creds_lines = read_creds_file('credentials_backup')
    with open(HOME_DIR + '/.aws/credentials', 'w') as curr_creds:
        for line in backup_file_creds_lines:
            curr_creds.write(line)
    print 'restored profile from backup'

def swap_to_profile(profile):
    curr_creds_lines = read_creds_file('credentials')
    count = 0
    for line in curr_creds_lines:
        if line == '[' + profile + ']\n':
            access_key_id = curr_creds_lines[count + 1]
            secret_key = curr_creds_lines[count + 2]
        count += 1
    with open(HOME_DIR + '/.aws/credentials', 'w') as curr_creds:
        count = 0
        for line in curr_creds_lines:
            if line == '[default]\n':
                curr_creds.write('[default]\n')
            elif curr_creds_lines[count - 1] == '[default]\n':
                curr_creds.write(access_key_id)
            elif curr_creds_lines[count - 2] == '[default]\n':
                curr_creds.write(secret_key)
            else:
                curr_creds.write(line)
            count += 1
    print 'Success - Swapped profile to ' + profile

def main():
    add_backup_if_none_exists()
    profile_to_swap_to = get_profile_to_swap_to()
    if profile_to_swap_to == 'restore':
        restore_from_backup()
    else: 
        swap_to_profile(profile_to_swap_to)
    
if __name__ == "__main__":
    main()
```

Add the script at this location in your aws folder: ~/.aws/aws_profile_swap.py folder
