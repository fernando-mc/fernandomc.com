+++
Description = "In this post I take a quick look at issues with the typing library and it's inclusion in Python 3.7"
Tags = [
  "Python3",
  "typing",
  "Python",
  "Python3.7",
  "AWS",
  "AWS Lambda",
]
Categories = [
  "AWS",
]
title = "Issues with the typing Library in Python 3.7 and AWS Lambda"
publishdate = "2019-05-20T13:19:30-07:00"
date = "2019-05-20T13:19:30-07:00"
[image]
    feature = "/images/keyboard.png"
+++

I recently upgraded an AWS Lambda API for [Upfront Jobs](https://beta.upfrontjobs.io/) to Python 3.7. This upgrade required me to use a library that relies on the `typing` module as one if its dependencies. However, when I deployed I noticed conflicts between the Python standard library `typing` and the `typing` module that was being installed. Here's how I resolved the issues for my work and how others could do the same. If you'd like the simple solution and no explanation, scroll down to the **Solution** section.

<!--more-->

## Identifying the Issue

The first step was identifying my problem. My API application is a Flask-based API running in AWS Lambda that sends Tracebacks and other logs to Amazon CloudWatch Logs for the runtime errors. I noticed that my logs were throwing Tracebacks that appeared related to the `algoliasearch` library:

```python
Traceback (most recent call last):
File "/var/task/wsgi_handler.py", line 44, in import_app
wsgi_module = importlib.import_module(wsgi_fqn_parts[-1])
File "/var/lang/lib/python3.7/importlib/__init__.py", line 127, in import_module
return _bootstrap._gcd_import(name[level:], package, level)
File "<frozen importlib._bootstrap>", line 1006, in _gcd_import
File "<frozen importlib._bootstrap>", line 983, in _find_and_load
File "<frozen importlib._bootstrap>", line 967, in _find_and_load_unlocked
File "<frozen importlib._bootstrap>", line 677, in _load_unlocked
File "<frozen importlib._bootstrap_external>", line 728, in exec_module
File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
File "/var/task/upfront/app.py", line 1, in <module>
from upfront.resources.root import Root
File "/var/task/upfront/resources/__init__.py", line 7, in <module>
from upfront import algolia
File "/var/task/upfront/algolia.py", line 4, in <module>
from algoliasearch.search_client import SearchClient
File "/var/task/algoliasearch/search_client.py", line 5, in <module>
from typing import Optional, Union, List
File "/var/task/typing.py", line 1356, in <module>
class Callable(extra=collections_abc.Callable, metaclass=CallableMeta):
File "/var/task/typing.py", line 1004, in __new__
self._abc_registry = extra._abc_registry
AttributeError: type object 'Callable' has no attribute '_abc_registry'
[ERROR] Exception: Unable to import upfront/app.app
Traceback (most recent call last):
  File "/var/lang/lib/python3.7/imp.py", line 234, in load_module
    return load_source(name, filename, file)
  File "/var/lang/lib/python3.7/imp.py", line 171, in load_source
    module = _load(spec)
  File "<frozen importlib._bootstrap>", line 696, in _load
  File "<frozen importlib._bootstrap>", line 677, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 728, in exec_module
  File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
  File "/var/task/wsgi_handler.py", line 118, in <module>
    wsgi_app = import_app(config)
  File "/var/task/wsgi_handler.py", line 49, in import_app
    raise Exception("Unable to import
{}
".format(config["app"]))
```

The key section of the logs above being:

```python
from algoliasearch.search_client import SearchClient
File "/var/task/algoliasearch/search_client.py", line 5, in <module>
from typing import Optional, Union, List
File "/var/task/typing.py", line 1356, in <module>
class Callable(extra=collections_abc.Callable, metaclass=CallableMeta):
File "/var/task/typing.py", line 1004, in __new__
self._abc_registry = extra._abc_registry
AttributeError: type object 'Callable' has no attribute '_abc_registry'
```

This error led me to an [GitHub issue](https://github.com/python/typing/issues/573) specifically related to the Python module `typing`. 

It appears that when the `typing` module was provisionally introduced to the Python standard library this caused several libraries that had previously relied on installing the `typing` module as a dependency to start having this issue.

Interestingly, this issue came up in a [variety](https://github.com/aws/chalice/issues/1050) of [popular tools](https://github.com/zulip/zulip/pull/11004/commits/014dd61104ccfbff83d17ce9bd37a1dd8637a520) and [packages](https://github.com/alexa/alexa-skills-kit-sdk-for-python/issues/49).

From what I can tell, the `typing` module was included as provisional part of the standard library as of Python 3.5. In versions 3.5.x and 3.6.x installing the `typing` module as a dependency didn't cause any issues. 

Recently, in Python 3.7 when it became a non-provisional part of the library the above issues with having pip-installed `typing` started to crop up.

I experienced these issues when installing `algoliasearch` in the AWS Lambda environment which requires `typing` as a package dependency and [reported an issue](https://github.com/algolia/algoliasearch-client-python/issues/422). However, I couldn't reproduce the issue locally with a virtual environment. I believe the reason for this is because of the order in which dependencies are loaded by Python and how the `typing` module conflicts with the standard library when it's loaded. 

## How Python `import`s

An huge oversimplification of the Python import process is that when you run `import typing` Python looks for:

1. A `typing.py` file in the current directory
2. If it can't find that it looks for `typing` in the standard library 
3. If `typing` isn't there it looks for a module installed by something like `pip`

Now the issue described above only occurs when Python tries to load the code from the pip-installed `typing` module. Normally that particular behavior doesn't come up because Python would load the standard library version before the pip package version.

## AWS Lambda Dependencies and Recreating the Issue

However, in AWS Lambda, and some other packaging situations, you install all the dependencies alongside your code in the same directory. In these cases, there is a `typing.py` file that appears in the same directory and takes precedence over the standard library. That's when the issue occurs and when you need to fix it.

So to recreate this issue you can do something as simple as running `pip install typing -t .` in the top level folder of your project. Then you can open up the Python 3 interpreter and just run `import typing`. 

That's all I needed to do to trigger a stack trace in a folder called `testy`:

```python
>>> import typing
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/Users/fernando/Desktop/testy/typing.py", line 1356, in <module>
    class Callable(extra=collections_abc.Callable, metaclass=CallableMeta):
  File "/Users/fernando/Desktop/testy/typing.py", line 1004, in __new__
    self._abc_registry = extra._abc_registry
AttributeError: type object 'Callable' has no attribute '_abc_registry'
```

The easiest way to test if your package suffers from this is to install it with pip or pip3 in the same directory as your current project alongside the code that would load the library. After that you can try to load it up in the interpreter. Here's how you would do this for a package called `yourpackage`:

`$ pip3 install yourpackage -t .`
`$ python`

Inside the Python interpreter you'd try to import the package with `import yourpackage` and get an error.

## But how do I fix it?

This depends. There's two options:

1. Fix the package itself
2. Fix your build process

Option one would require you to open a PR against whatever package you're using and ask the maintainer nicely to only have `typing` as a dependency for versions before Python 3.5. 

With Option two, you can just remove the installed `typing` files from your eventual build either through a final `pip uninstall typing` or simply manually removing them with a bash script or by hand.

In my case, I was using the Serverless Framework and the Python Requirements plugin which allowed me to simply exclude the library from being deployed.

All I had to do was add this snippet to my serverless.yml file:

```yaml
custom:
  pythonRequirements:
    noDeploy:
    - typing
```

So hopefully this helps any maintainers or developers facing this issue. Best of luck packaging!