+++
Description = "Learn to use Ticket Owl to get notified when a webpage you're monitoring changes in a way you're interested in."
Tags = [
  "Projects",
  "Serverless",
  "Python",
  "AWS",
  "Ticket Owl"
]
Categories = [
  "AWS",
  "Serverless",
  "Projects"
]
title = "Ticket Owl - A Tool to Check for Website Changes"
menu = "main"
date = "2018-02-20T14:08:12-05:00"
publishdate = "2018-02-20T14:08:12-05:00"
[image]
    feature = "/images/ticket_owl/ticket-owl.png"
    credit = "Image Credit | Chris Parker"
    creditlink = "https://www.flickr.com/photos/chrisparker2012/14963399105/"

+++

For Valentine's Day this year I wanted to get a nice gift for a special someone. So I decided to buy some tickets for a dinner and show. Just one problem, the site I was looking at ran out of tickets for currently scheduled shows. When I called the restaurant they told me to check the website - they were going to add more tickets at a later time.

But I didn't want to constantly check the page to see if new times and dates were added, so I wrote a new project called Ticket Owl to do it for me.

<!--more-->

Ticket Owl periodically checks a website for characteristics I'm interested in. I've written it so that it is flexible enough to do a few different things:

- To check a website with varying frequency 
- To allow you to check for text on a page
- To allow you to check for text _not_ on a page
- To notify you via email or SMS or both when your condition is met
- To be deployed to AWS with the [Serverless Framework](https://www.serverless.com)
- To process multiple checks of multiple different websites

Now in addition to Valentine's Day-esque uses you could probably use this to regularly monitor the status of different websites. But, realistically, you just want to know the moment that those Taylor Swift tickets are on sale.

### Getting Started

Here's how you can use this project.

1. Make sure you already have the [Serverless Framework](https://www.serverless.com) and [Python3](https://www.python.org) installed.
2. Make sure you have an AWS account with your account 
3. Clone the repo with `git clone https://github.com/fernando-mc/ticket-owl.git`
4. Make sure you have [a verified email address](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/verify-email-addresses-procedure.html) to use in your AWS account if you want to send email alerts. 
5. a) Review the examples in `index.py` and update it to reflect the checks and alerts you'd like to work with
   </br> **Or** </br>
   b) Update the `jobs.py` file to reflect the same details and make sure your `index.py` file keeps the code to run the jobs in the handler function.
</br>
 (_More on both of these in a moment_)

6. Finally, when you're ready just use `sls deploy` to deploy Ticket Owl to AWS and optionally use `sls invoke --function scan` to manually trigger and test the function. Although if the conditions you're waiting for haven't happened yet, you won't get any alerts!

### Writing Your Checks

Let's go into more detail on the two options you have to check your website for different characteristics.

#### Option 1

Write 'jobs' in the `jobs.py` file to be checked every time your AWS Lambda function runs. Jobs in this sense are basically just configurations values like the list of websites and conditions to potentially alert on as well as how to alert on them.

An example `jobs.py` file might look like this: 

```python
JOBS = [
    {
        'url': 'https://www.myticketsite.com/event/123123',
        'value_to_check': 'Feb 10',
        'checker': 'contains_text',
        'alerts': ['email', 'sms'],
        'message': 'The ticket for Feb 10 is now on sale! - https://www.myticketsite.com/event/123123',
        'subject': 'Ticket Owl Alert',
        'verified_email': 'fernando@a-verified-email.com',
        'phone_number': '+1555666777#'
    },
    {
        'url': 'https://www.myfavoritemuscian.com/',
        'value_to_check': 'Tour',
        'checker': 'contains_text',
        'alerts': ['sms'],
        'message': 'Looks like your favorite musician is talking about her next tour! - https://www.myfavoritemuscian.com/',
        'phone_number': '+1555666777#'
    },
]
```

Let's break this down. In this file, there are two 'jobs' which are basically just Python dictionaries with a standard set of possible configuration values that are shown in the default `jobs.py` file [in GitHub](https://github.com/fernando-mc/ticket-owl/blob/master/jobs.py).

All jobs require the `url`, `value_to_check`, and `checker` values. In the same order listed, these values tell Ticket Owl what URL to fetch the page text of, what string value to search for inside of the page text and what condition to apply to that value if found. 

For example the `checker` of `contains_text` would return True if the page contained the `value_to_check` string.

Additionally, there is an `alerts` array that tells Ticket Owl how to alert the user. Either via SMS or email. 

Finally, depending on the `alerts` value some additional values must be included to allow alerting to take place. SMS and email require different values:

The `sms` alert requires only `message` and `phone_number` configuration values. 
The `email` alert requires `message`, `subject`, and `verified_email`. The email must be verified in Amazon SES.


An `index.py` file using the only the jobs method of checking websites might look as simple as this:

```python
import helpers
from jobs import JOBS


def handler(event, context):
    helpers.run_jobs(JOBS)

```

After the `jobs.py` and `index.py` files are saved you can deploy the project with the `sls deploy` command.


#### Option 2

Alternatively, you can skip using the `jobs.py` file entirely and write your own logic with the helpers provided in `helpers.py`. In this case, you might see an `index.py` file that looks like this:

```python
import helpers

url = 'https://www.tickets.com/event/123123'
message = 'That ticket you wanted is now on sale! https://www.tickets.com/event/123123'
verified_email = 'fernando@some-ses-verified-email.com'
subject = 'Your tickets are on sale!'
phone_number = '+1555777888#'

def handler():
    if helpers.contains_text('Mezzanine', url):
        if helpers.lacks_text('Only VIP Tickets On Sale Now', url):
            helpers.send_email(verified_email, subject, message)
            helpers.send_sms(phone_number, message)

```

Half of this file basically sets up the values you'll use for alerting purposes. The latter half demonstrates how simple it is to check if a site contains text or doesn't contain certain text and then how to use the helpers to alert on the result.

Right now there are only two main helpers: `contains_text` and `lacks_text` which evaluate to true, respectively, if the site contains the text you're looking for, or lack it completely. However, I'm considering adding a `site_changed` checker to be used to diff effectively between the site from one scan to another. 

And that's it really! Check it out and let me know what you think. As always, [PRs are welcome](https://github.com/fernando-mc/ticket-owl/) and feel free to leave a comment below.