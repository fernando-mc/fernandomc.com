+++
draft=true
Description = "Instructions on how to create your first API with Python and Flask"
Tags = [
  "Python",
  "API",
  "Flask",
  "Python3",
  "JSON",
  "First Projects",
  "Start Project"
]
Categories = [
  "Guides",
  "Python"
]
title = "Creating Your First Python API with Flask"
publishdate = "2018-09-18T17:02:12-07:00"
date = "2018-09-18T17:02:12-07:00"
[image]
    feature = "/images/generic_backgrounds/bogota-library2.png"
+++

In this tutorial, we'll use Flask to create an API that serves up historical weather data in the Seattle area. We'll put together a very simple API using open weather data and finally, we'll suggest a few modifications you could make to continue expanding and improving the functionality of your API.

<!--more-->

Before we get started, you should know that this guide expects you to:

1. Already have Python3 installed on your machine (need help? Try [this guide](https://redmondpython.com/onboarding/) or [this one](https://docs.python-guide.org))
2. Have some of the basics of Python syntax down (Use the guides above)

## First Steps - Planning

One of the first steps in building any API is to plan out how you want the API to work. In most cases, this means thinking about the data you want to handle and how you want to handle it. get the data you want (or fake some data that will do for now). Let's start by thinking about what we want to be able to do:

1. We want to be able to provide the API a day in the past
2. We want the API to lookup the weather conditions for that day
3. We want the API to return the weather conditions for that day back from the API

For right now, let's try doing this with just a simple Python function and limiting the scope of what we're after to a few days.

```python
# Let's say we looked this up somewhere
data = {
    "2018-01-01": {"Temperature": "50"},
    "2018-01-02": {"Temperature": "53"},
    "2018-01-03": {"Temperature": "52"}
}

# Define our simple function
def what_was_the_weather_like(date):
    return str(data[date])

what_was_the_weather_like("2018-01-01")

# This should return:
# "{'Temperature': '50'}"
```

So this function works right? Technically it accomplishes our three goals:
1. We provide "the API" a day in the past
2. "The API" looks up the weather conditions for that day
3. "The API" returns the data for that day

I guess we're done! 

Okay, so maybe we might want to add a few more components to what we want to accomplish. API means "Application Program Interface", which our code above maybe _sort of_ qualifies as. We probably want to extend the goals for this project to building an **HTTP API**. This means that we could access the data by making an HTTP request. So let's expand our requirements and say we also want our API:

- To provide the information via HTTP GET requests
- To provide the data outputted in JSON format
- To work when we make HTTP GET requests _locally_ on our machines (we can save deploying the API for later)
- To rely on the Flask microframework 

With these goals in mind we can move on to actually building our HTTP API with Flask.

## Installing Requirements

We're evaluating the input data and then returning it


4. Check publish date and date
5. Add all the other front matter
6. Check summary

