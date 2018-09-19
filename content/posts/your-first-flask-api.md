+++
Description = "Instructions on how to create your first API with Python and Flask"
Tags = [
  "Python",
  "API",
  "Flask",
  "Python3",
  "JSON",
  "First Projects",
  "Starter Project",
  "HTTP",
  "Guide",
  "Data",
  "Open Data"
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
3. Understand some of the basics around Python virtual environments
4. Understand some of the basics of installing Python packages with pip

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

## Installing Requirements and Downloading Data

The next step is to get our development environment set up.

First, we'll create a new directory to host our project and navigate into that with our command line. From within that folder we'll create a Python virtual environment called `venv` with `python3 -m venv venv`. You may use `python` instead of `python3` depending how you installed python on your machine.

Next up, you'll need to turn the virtual environment on. On a Mac or Linux operating system you can run the `source venv/bin/activate` command from within the project folder. On a Windows machine you'll need to run the activate script at `venv\Scripts\activate`.

Now that your virtual environment is turned on you'll need to install your dependencies for this project. Fortunately, you should only have one - Flask. You can install it with `pip install Flask`. 

With Flask installed you should have all the requirements you need to grab the data you'll actually be using in this tutorial. You could decide to download your own data from the [National Centers for Environmental Information](https://www.ncdc.noaa.gov/cdo-web/search?datasetid=GHCND), or you can download the data I've already downloaded from them for you [here](https://raw.githubusercontent.com/fernando-mc/your-first-python-flask-api/master/seattle-data.csv).

After you download the data make sure to place it in your project directory (we'll use it later). 

## Creating our Flask Application

Without going into too much detail on how you can use Flask, we'll start by creating a file called `app.py` in the project directory. Inside of that file we will start with a very basic route for our API that returns a single day of data:

```python
# We import Flask
from flask import Flask
    
# We create a Flask app
app = Flask(__name__)

# We establish a Flask route so that we can serve HTTP traffic on that route 
@app.route('/')
def weather():
    # We hardcode some information to be returned
    return "{'Temperature': '50'}"

# Get setup so that if we call the app directly (and it isn't being imported elsewhere)
# it will then run the app with the debug mode as True
# More info - https://docs.python.org/3/library/__main__.html
if __name__ == '__main__':
    app.run(debug=True)
```

Now that We have this file setup we can actually try running our Flask API with `python app.py`. This should start up our application and we should see something like this in the terminal output 
```
 * Serving Flask app "app" (lazy loading)
 * Environment: production
   WARNING: Do not use the development server in a production environment.
   Use a production WSGI server instead.
 * Debug mode: on
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 123-456-789
```

If we see this, it effectively means our application is running and we can view it in our browser by visiting the `http://127.0.0.1:5000/` address. When we do that we should see the data that our function was supposed to return:

`{'Temperature': '50'}`

So, now we have a functioning Flask API that returns a single day of data when we visit the local address of `127.0.0.1:5000`. This is a great step forward! Because the web browser you are using is, behind the scenes, making an HTTP GET request to the `127.0.0.1:5000` address we're actually making a lot of progress here! 

## Adding and Cleaning Real Data

So now that we have a basic version of our application up and running we can take the next steps and get some real data.

The first thing we'll need to do is to get some data in a format that our Flask application can search through it and return the information we need. If you haven't already, download the csv weather data [here](https://raw.githubusercontent.com/fernando-mc/your-first-python-flask-api/master/seattle-data.csv) and put it in your current directory.

This CSV data contains Seattle Weather information from Jan. 2015 to Sep. 2018. We'll need to parse the CSV information into a format that Python can easily interact with. In a real environment we might store this information in some sort of database and look up the information from there. To simplify things, we'll be loading this information from a CSV file (which is a bit more difficult to interact with) to a JSON file (which we can easily load into a Python dictionary).

To start this process we'll take a look at the first few lines of the CSV data:

```
"STATION","NAME","DATE","AWND","PRCP","SNOW","SNWD","TAVG","TMAX","TMIN","WT01","WT02","WT03","WT05","WT08"
"USW00024233","SEATTLE TACOMA INTERNATIONAL AIRPORT, WA US","2015-01-01","2.68","0.00","0.0","0.0","33","42","26",,,,,
"USW00024233","SEATTLE TACOMA INTERNATIONAL AIRPORT, WA US","2015-01-02","5.14","0.06","0.0","0.0","35","42","32","    1",,,,
```

You'll notice that the header row has a variety of different fields, some of which we probably don't need. So while we're transforming this data into JSON we'll also cut out the fields we don't want. Specifically, we'll only be keeping these fields:

```
"DATE" - The date for the data 
"AWND" - Average Wind Speed 
"PRCP" - Precipitation 
"SNOW" - Snowfall 
"SNWD" - Snowpack
"TAVG" - Average Temperature 
"TMAX" - Max Temperature 
"TMIN" - Min temperature 
```

Fortunately, we can transition a CSV into JSON using the `csv` and `json` libraries Python provides for us. Here's a script that shows how:

<script src="https://gist.github.com/fernando-mc/aa0557dc050fc8eaaaade272ddfb9e0d.js"></script>

When we're ready, we can create a file using this code and call it `cleaner.py`. Then we can run it with `python cleaner.py`. This should create a new file called `seattle-data.json` that our API will load data from.

## Parsing API Input

With our new JSON file created, we can now work on editing our Flask application to accept input and return different results based on that input. 

In order to accept input when folks make different requests to our API we'll need to setup a more dynamic Flask route. We'll do this by changing our route from what we had before:

```python
@app.route('/')
def weather():
    return "{'Temperature': '50'}"
```

To this:
```python
@app.route('/weather/<date>')
def weather(date):
    return 'some information'
```

This small change does a few things for us. First, it allows us to request data from the API by adding `/weather/` and a date to the URL. At the moment, whatever data we included would just return the string `some information`, but the function responsible for generating data for the request can actually use the `date` that comes in from the URL to create a dynamic request. Doing this will be our next step.

Earlier in the tutorial we wrote the somewhat cheeky `what_was_the_weather_like()` function that returned data based on a provided date. Well, we're actually going to do something very similar in our Flask application. We're going to modify `app.py` so that it can make use of the information in `seattle-data.json`:

```python
# We now need the json library so we can load and export json data
import json

from flask import Flask
    
app = Flask(__name__)

# We're using the new route that allows us to read a date from the URL
@app.route('/weather/<date>')
def weather(date):
    # Additionally, we're now loading the JSON file's data into file_data 
    # every time a request is made to this endpoint
    with open('./seattle-data.json', 'r') as jsonfile:
        file_data = json.loads(jsonfile.read())
    # We can then find the data for the requested date and send it back as json
    return json.dumps(file_data[date])

if __name__ == '__main__':
    app.run(debug=True)
```

Now, we can test our API! You might have to restart your Flask application by running `python app.py` again, but after that you can visit `http://127.0.0.1:5000/weather/2018-01-01`. You should see the data for that day show up there:

```
{"PRCP": "0.00", "SNOW": "0.0", "SNWD": "0.0", "TMAX": "44", "AWND": "8.72", "TAVG": "36", "TMIN": "31"}
```

You can also try other days like `http://127.0.0.1:5000/weather/2017-11-23` just to make sure you're seeing different results:

```
{"PRCP": "0.20", "SNOW": "0.0", "SNWD": "0.0", "TMAX": "59", "AWND": "12.53", "TAVG": "57", "TMIN": "48"}
```

Congratulations! You've now created a locally-functioning HTTP API with Python and Flask! 

## Extending the Project

If you're thinking "This is cool! But what's next?" don't worry! There are many things you could do to extend this project. Here are just a few suggestions and ideas:

1. Add a database to store the data instead of hosting it all in a JSON file
2. Deploy the API to a cloud infrastructure provider like AWS
3. Add a frontend website to make requests to your API and display the information

Doing any of these things to extend the project can help you to understand more about Python, web development or modern software infrastructure. If you're interested in another tutorial on one of these topics just leave a comment below!
