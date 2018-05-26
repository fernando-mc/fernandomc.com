+++
Description = "A guide to getting started with the Python libraries requests and Beautiful Soup."
Tags = [
  "Python",
  "Meetup",
  "Redmond Python",
  "requests",
  "Beautiful Soup",
  "AWS",
  "Guides"
]
Categories = [
  "Python",
  "Guides"
]
title = "Python Requests and Beautiful Soup - Playing with HTTP Requests, HTML Parsing and APIs"
menu = "main"
publishdate = "2018-05-26T13:47:49-07:00"
date = "2018-05-26T13:47:49-07:00"
[image]
    feature = "/images/requests-and-beautifulsoup.png"
    credit = "Image Credit | Vee Satayamas"
    creditlink = "https://www.flickr.com/photos/vscript/26139938867/"

+++


Recently, while running the [Redmond Python Meetup](https://www.meetup.com/Redmond-Python-User-Group/) I've found that a great way to get started using Python is to pick a few common tools to start learning. Naturally, I gravitated towards teaching the basics of one of the [most popular](https://pythonwheels.com) Python packages - [Requests](http://docs.python-requests.org/en/master/). I've also found it's useful to throw in using [Beatiful Soup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#installing-beautiful-soup) to show folks how they can efficiently interact with HTML data after getting an HTML page.

Sound interesting? Let's look at what I typically cover - including a few basic examples of how you can use Requests to make HTTP GET and POST requests.

<!--more-->

## Step one - Get Requests and Beautiful Soup

<img src="/images/requests-and-beautifulsoup.png" width=600px></a>

Despite being incredibly popular, Requests is [not](https://github.com/requests/requests/issues/2424) in Python's standard library. In order to get requests you should use pip. For this guide, I'll be assuming that you have a version of Python 3 and pip installed.

_If you want to use a Python virtual environment you can make yourself a project directory somewhere, navigate to that project in your terminal, command prompt or powershell and then run_ `python -m venv my_venv` _and_ `source my_venv/bin/activate`. _Windows users will need to run_ `my_venv\Scripts\activate.bat`.

When you're ready to install Requests you can use `pip install requests` from in your terminal, command prompt, or powershell. 

To install [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#installing-beautiful-soup), you can run `pip install beautifulsoup4` in the same place.

## Getting Started with Requests

Next up, we'll use requests in the python interpreter. Go ahead and run `python` to get into the interpreter and we'll start using it.

_In general, if you get any errors running_ `python` _you may want to try running the commands with_ `python3` _instead of_ `python` _or making sure you have Python installed_

Start by importing the requests library and making a simple GET request to the URL for this blog (Remember that the >>> isn't something you're typing).

```python
>>> import requests
>>> requests.get('https://www.fernandomc.com/')
<Response [200]>
```

After running these commands, and assuming you see the same `<Response [200]>` I do, this means my website is still functional and serving content (phew!). What this is essentially saying is that the site responded with a HTTP 200 OK response code. You can read more about HTTP codes [here](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#2xx_Success).

But this is kinda boring. We probably wanted to actually see the _content_ of the site. So how can we get this? Well, requests actually makes this pretty easy. Let's try making the same request again, except this time we'll store the result in a variable called `r`. 

```python
>>> import requests
>>> r = requests.get('https://www.fernandomc.com/')
>>> r.text
'<!DOCTYPE html>\n<!--[if lt IE 7]><html class="no-js lt-ie9 lt-ie8 lt-ie7" lang="en"> <![endif]-->\n<!--[if (IE 7)&!(IEMobile)]><html class="no-js lt-ie9 lt-ie8" lang="en"><![endif]-->\n<!--[if (IE 8)&!(IEMobile)]><html class="no-js lt-ie9" lang="en"><![endif]-->\n<!--[if gt IE 8]><!--> <html class="no-js" lang="en"><!--<![endif]-->\n<head>\n<meta charset="utf-8">\n<title>Fernando Medina Corey</title>\n<meta name="description" content="I&#39;m Fernando Medina Corey, a data engineer, published course author for software engineering content and an avid punster.">. . .'
```

After we saved the request as `r`. We can actually access the content of the site using the `text` property of the completed request (That's what we're doing when we run `r.text`). Now I've truncated the response because there's actually a lot of text making up my website's homepage and I didn't want to make you see all of it.

## Parsing Our Request Data with Beautiful Soup

So now that we have this requests data in `r.text` how do we start working with it? Well, let's start by looking at what it is.

```ruby
>>> type(r.text)
<class 'str'>
```

Well, we at least know that we're dealing with a string. So we have all the built-in Python string methods like `.split()`, `.replace()` and others. But these honestly aren't going to save us a ton of time if we have to parse through a bunch of HTML gibberish. That's where Beautiful Soup comes in. We can use Beautiful Soup to add structure to our HTML string and make it a bit easier to interact with. We already installed Beautiful Soup earlier, so how do we use it now?

In the same terminal you've had open this whole time run `from bs4 import BeautifulSoup`. This will bring in the `BeautifulSoup` class and let you get started. After that, you'll create a 'soup' variable, which will hold your BeautifulSoup class instance which will be created from an HTML document and the parser setting that you provide (in this case, HTMl).

It should look something like this:

```python
>>> from bs4 import BeautifulSoup
>>> soup = BeautifulSoup(r.text, 'html.parser')
```

As you can see above, we're passing in `r.text` as the first value to `BeautifulSoup()` in order to give it the HTML string from the website. Then, with the second value, we're also telling it to use the HTML parser when it parses that string. Here's where stuff gets super handy. We can now access HTML elements or the text inside those elements with simple properties of our new `soup` variable. 

For example:

```python
>>> soup.title
<title>Fernando Medina Corey</title>
>>> soup.title.text
'Fernando Medina Corey'
>>> soup.p
<p>I'm Fernando Medina Corey, a data engineer, published course author for software engineering content and an avid punster.</p>                                    ' # <--- Ignore this apostrophe. I just put it there to fix syntax highlighting
>>> soup.p.text
"I'm Fernando Medina Corey, a data engineer, published course author for software engineering content and an avid punster."
```

Now, these are just a few examples. I'd suggest that you [read more](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#installing-beautiful-soup) about all the other useful features you have access to when using Beautiful Soup too. But now that you understand how you can download website data and interact with it in Python let's change gears a little and look at how you can use requests to send information _back_ to a website.

## Using Requests to send POST Requests to an HTTP API 

Requests is good for more than just _getting_ site data. It provides us with a full suite of [HTTP Methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods). In this section we'll look specifically, at how we can use a POST request to send information to an HTTP API. 


### First, what the heck is this API?

But what do I mean by that? And what is an HTTP API? Well, in this case I mean that we're going to use requests to send some information to a particular URL so that it can be processed by a server and send us some information back. In this particular case, we will be using an API that I initially built [as an April Fools joke](https://www.fernandomc.com/posts/nandolytics-serverless-website-analytics/). But since then, I've realized it also works pretty well as an example of how you can use requests. 

First, we're going to need the URL of the API we're interacting with. In this case, I built the API on [AWS](http://aws.amazon.com) and just used one of the default URLs provided for APIs hosted on their API Gateway service so the endpoint looks like this `https://hz9ap21u7a.execute-api.us-east-1.amazonaws.com/dev/nandolytics/record`

Depending on the API you're working with you might need to interact with it in different ways. Some APIs might not accept certain HTTP methods or might expect to be sent certain information. 

For this particular API endpoint, we'll need to send a POST request to the endpoint above with some JSON data. JSON is a very common format for information sent and received via HTTP APIs so it might be worth [reading a little more](https://www.w3schools.com/js/js_json_intro.asp) about it. 

Specifically, we'll need to send JSON in that looks like this: 
```json
{"siteUrl": "https://www.examplesite.com"}
```
The reason for this is described in [the post](https://www.fernandomc.com/posts/nandolytics-serverless-website-analytics/) on building the API itself. But essentially the API expects the data in this format so that it can tally a 'hit' to the site's URL in a database every time a POST request is made to that endpoint. In this case, we'll technically be faking visits or 'hits' to my site.

### Now let's interact with the API using Python

So to POST to an API endpoint with Python we can do something remarkably similar to what we did earlier with the GET request. I'll assume that we've just opened a new Python interpreter below:

```python
>>> import requests
>>> url = 'https://hz9ap21u7a.execute-api.us-east-1.amazonaws.com/dev/nandolytics/record'
>>> json_data = {"siteUrl": "https://www.fernandomc.com"}
>>> r = requests.post(url, json=json_data)
>>> r
<Response [200]>
>>> r.text
'{"siteUrl": "https://www.fernandomc.com", "siteHits": "207"}'
>>> 
```

So let's break this down line by line here:

1. We import the requests library
2. We set a variable called `url` equal to the API endpoint we need to make a request to
3. We set a variable called `json_data` equal to a Python dictionary (technically this isn't JSON, but requests will handle it for us)
4. We set a variable `r` equal to the result of a HTTP POST request made using requests' `.post()` method. And in the same line, we make sure to specify the `url` we set earlier as the URL we want the request to go to. We also use the `json` keyword argument inside the `.post()` method. This argument passes in a Python dictionary to requests that will be transformed into JSON when making the POST request.
5. We return `r` (the result of the POST request) and it looks like it's ok!
6. Finally, we take a look at the text of the request with `r.text` and it contains a Python string (which happens to be JSON-formatted).

Inside the string, the API is essentially telling us "Hey for the site URL you gave us we have a total of 207 visits". But what if we run the code a few more times...

```python
>>> requests.post(url, json=json_data)
<Response [200]>
>>> requests.post(url, json=json_data)
<Response [200]>
>>> requests.post(url, json=json_data)
<Response [200]>
>>> r = requests.post(url, json=json_data)
>>> r.text
'{"siteUrl": "https://www.fernandomc.com", "siteHits": "211"}'
```

Now we see that after sending off this information four more times we have four more 'siteHits'. And there you have it! You've successfully interacted with an API using Python.

## Next Steps 

You can use your newfound Python skills in a variety of different ways. But here are a few suggestions you might want to try later on.

1. Pick another API to play around with such as the [Twitter API](https://developer.twitter.com/en/docs/tweets/search/api-reference/get-search-tweets.html)
2. Scrape some website data with Requests and Beautiful Soup and try to structure the data in a useful way.

Good luck in your next Python adventure!