# Python Packages
---

This guide is intended to clarify a lot of the confusion about distributable python packages caused by existing guides and information on the internet. This means your package will be installable, via pip or otherwise, for use systemwide.

Although we _could_ upload to PyPI, I will skip such information in this guide as much of our work is meant to stay private. If you are curious about how to accomplish such a thing, you may use [this link](http://peterdowns.com/posts/first-time-with-pypi.html) to learn more.
  
## Packages: Setting Up the Directory Structure
One of the most important parts of building a new project destined to become a package is organization. Python packages are meant to have a certain directory structure otherwise there will be errors and/or unexpected behavior.

Python packages have three primary components, however there may be more based upon complexity. These three primary components are the `setup.py` script, the `__init__.py` script, and the `namespace package`.

The `setup.py` script contains the call to setuptools which pip looks for to install the package and perform other functions. I will go over this in much more detail in a later section as it is the most important file in a package.

The `__init__.py` script(s) tell setup tools and, more importantly, Python that the directory should be treated as a module or pacakge.

Finally, `namespace package` is a fancy way of saying "folder containing your code to run". Python works off of namespaces so if you've ever used `os.path` for example, the `namespace package` is actually a folder called `os` which contains an `__init__.py` file, and contains a _subpackage_ called `path` which would in turn be its own namespace package i.e. a folder named "path" (inside the folder named "os") that contains an `__init__.py` file and some python code.

When you see things like:

```python
import A
from A import B.function
import A.B.C.function

```

Then A, B, and C are namespaces while B and C are subpackages (A is the top level package). This will, hopefully, make a lot more sense as you read on.

In the most basic case, you will have a directory structure such as this:

```
.
├── namespace
│   ├── __init__.py
│   └── sample.py
└── setup.py
```

When you install this package, typically by running `python setup.py install`, you will end up installing a package called whatever you specified as the `name` parameter in `setup.py` but would use it by calling

```python
from namespace import sample
sample.example_function()
```

In the case of `os` and `os.path` as mentioned above, the directory structure might look something like this:

```
.
├── os
│   ├── __init__.py
│   ├── os.py
│   └── path
│       ├── __init__.py
│       ├── exists.py
│       └── walk.py
└── setup.py
```
(This is obviously not correct and omits a lot, but is sufficient to illustrate the idea).

--

## Setup.py

Arguably the most important file in your package destined for distribution is the `setup.py` file. The purpose of this file is to utilize Python's setuptools to help install your package onto the system in a way that facilitates easy management.

This file can be quite daunting if you don't know what you are doing but it is actually quite simple once you realize what you do and don't need. As an example here is a complete, minimal `setup.py` that I wrote:

```python
from setuptools import setup, find_packages

setup (
	name='sample',
	version='1.0.0',
	
	author='Ian Guibas',
	author_email='Ian.Guibas@example_domain.com',
	
	description='A sample setup script',
	long_description='A simple, minimal setup.py to use as a guide',
	
	packages=find_packages()
)
```
As you can see that entire script is only a handful of lines. Let's break it down a bit more:

`name` is what the package will be called once installed. In the example directory structure above, the package containing the code was called `namespace` however in the above script, `name` is set to `sample`. The implication of this is what is seen when running `pip list` or `pip freeze` will be `sample` but what is used when running in python will be. 

`version` is the state of the package/project. The Python Foundation sets standards and guidelines for versioning in [PEP440](https://www.python.org/dev/peps/pep-0440/). The TL;DR version of this is `MAJOR_VERSION.MINOR_VERSION.MICRO_VERSION`.

`author` and `author_email` give details about the primary author of the package. On PyPI this is so people know who to contact about the package. In practice it is preferable to put real, usable contact information rather than just filling in with junk.

`description` and `long_description` are exactly what they sound like. `description` is the short summary of your package's functionality or purpose while long_description expands upon that. Typically people will read a project's README file into long_description.

`packages` defines which packages/subpackages to include in the distribution. In the above example there is only one package: the `namespace` package. This is waht you will actually `import` when you install. As an example: in `from os.path import *`, `os` is a package and `path` is a _subpackage_ of os. If our project was meant to have multiple namespaces, we would consider each namespace a package. This is just a fancy way of saying that if our project was meant to allow us to do `import thing1` which grants one suite of functionality, and `import thing2` which grants its own differing suite, we would consider `thing1` and `thing2` to be packages.

---

## Installation and usage of our example

The previous section talked about directory structure and the setup file but now its time to talk about how it all comes together and is able to be used.

Take a look at this example output from my machine:

```bash
(example) ➜  package_example ls -l
total 8
drwxr-xr-x  4 user  staff  136 Sep 19 12:45 namespace
-rw-r--r--  1 user  staff  333 Sep 19 12:44 setup.py

(example) ➜  package_example ls -l namespace
total 8
-rw-r--r--  1 user  staff   0 Sep 19 12:44 __init__.py
-rw-r--r--  1 user  staff  97 Sep 19 12:45 sample.py

(example) ➜  package_example pip list --format=columns
Package    Version
---------- -------
pip        9.0.1
setuptools 36.5.0
wheel      0.30.0

(example) ➜  package_example cat setup.py
from setuptools import setup, find_packages

setup (
    name='sample',
    version='1.0.0',
    author='Ian Guibas',
    author_email='Ian.Guibas@example_domain.com',
    description='A sample Python Package',
    long_description='A minimal skeleton of an installable Python package to be used as an example',
    packages=find_packages()
)
```

Above, I show first an example package I have made and structured (based upon the earlier directory structure example). You can clearly see the `namespace` folder containing `__init__.py` and the module actually containing code, `sample.py`. On the same level as this directory we have our `setup.py` script whose content is displayed at the bottom of the snippet (as it is rather short). Lastly, there is a list of all installed python packages (note the `(example)` denoting I am working in a virtualenv called `example`. This is to keep the installed packages to a minimum which is why the output is so short). As you can see I have not yet installed the package. Let's do that now:

```bash
(example) ➜  package_example python setup.py install
running install
running bdist_egg
running egg_info
creating sample.egg-info
writing sample.egg-info/PKG-INFO
...
Installed /Users/user/.virtualenvs/example/lib/python2.7/site-packages/sample-1.0.0-py2.7.egg
Processing dependencies for sample==1.0.0
Finished processing dependencies for sample==1.0.0

(example) ➜  package_example pip list --format=columns
Package    Version
---------- -------
pip        9.0.1
sample     1.0.0
setuptools 36.5.0
wheel      0.30.0
```

To keep the snippet terse, I have omitted several lines of debugging information above (denoted by `...`) however the idea should be reasonably clear. After running `python setup.py install`, setuptools will installs the package called `sample` as was specified by `name`. This does _not_ mean however that we import `sample` to use this package (something users of `pwntools` might be familiar with). Let's look at what happens:

```python
>>> import sample
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ImportError: No module named sample
```
Remember how we called the actual package `namespace` (the directory name above)? That's what we actually will import:

```python
>>> import namespace
>>>
```

Now is a good time to illustrate another important role of the `__init__.py` scripts. We have installed our package and want to use it. We know that we can import it via `import namespace` but the actual module we are trying to reference is `sample.py`. The contents of `sample.py` are very simple:

```python
def example_function():
    print 'This is a very simple Python module that prints this message'
```

Right now, our `__init__.py` is merely an empty file as that is all that python requires to recognize a namespace package. You might assume that, given all this information and the successful install, that you can use the package in this fashion:

```python
>>> import namespace
>>> namespace.sample
```

But what will happen currently is this:

```python
>>> import namespace
>>> namespace.sample
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'module' object has no attribute 'sample'
>>> 
```

So what does that error mean? Well `__init__.py` tries to *initialize* our namespace when it is imported. Python makes no assumptions about what to include and doesn't actually include anything unless it is explicitly told to do so whether by a `from ______ import *` style statement or ... it is specified in `__init__.py`. So you might be thinking: "How come it installed with no errors or warnings? I can't access my code at the moment". Well, that's not actually true and you in fact _can_ access your code, you just have to be very explicit about it. While you can't simply do an `import namespace` then access `namespace.sample`, you _can_ verify that the sample module is a part of your `namespace` package and importable by running `help(namespace)` which produces:

```bash
Help on package namespace:

NAME
    namespace

FILE
    /Users/user/.virtualenvs/example/lib/python2.7/site-packages/sample-1.0.0-py2.7.egg/namespace/__init__.py

MODULE DOCS
    http://docs.python.org/library/namespace

PACKAGE CONTENTS
    sample

DATA
    __loader__ = <zipimporter object "/Users/user/.virtualenvs.../pytho...
```

As you can see by `PACKAGE CONTENTS`, the sample module is infact in there. In the current state of the package, you would access it by explicitly importing the module from the namespace like so:

```python
>>> from namespace import sample
>>> sample.example_function()
This is a very simple Python module that prints this message
>>>
```

There is however a better way to do this such that we can do `namespace.sample.example_function()` to achieve the same result. The point here being that in the event there are _multiple_ modules under this namespace (for example if we included `sample2.py`), we need only do `import namespace` to access them right away rather than importing them individually. To do this we simply edit the `__init__.py` script inside `namespace` to be:

```python
import sample
```

**NOTE:** Since we did `setup.py install` rather than `setup.py develop`, which I covere later, any changes will require the whole package to be installed again to take effect.

With that change made to `namespace/__init__.py`, we can now see that we have access to the sample module right away when importing `namespace`:

```python
>>> import namespace
>>> namespace.sample
<module 'namespace.sample' from '/Users/user/.virtualenvs/example/lib/python2.7/site-packages/sample-1.0.0-py2.7.egg/namespace/sample.pyc'>
>>> namespace.sample.example_function()
This is a very simple Python module that prints this message
>>>
```

---

## Including non python files

The previously provided setup format will work for a good deal of simple projects but it is likely that you will quickly run into situations where it won't work because it fails to include supplementary files. There are a handful of ways to include these non-python files, however many may seem dauntingly unintuitive or worded in a way that doesn't quite fit what you'd expect.
