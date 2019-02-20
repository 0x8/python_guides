# Python Virtual Environments
---

For the uninitiated or unaware, Python Virtual Environments are a great way to develop or install python projects in such a way that they don't conflict with anything or muddy up your python path. They allow you to have several differnt versions of the same package installed (each in their own environment granted) so you never have to worry about having to uninstall something important to use one package for the sake of one project. I would highly recommend the use of python virtual environments in your day to day development if at all possible.

This page is intended to help you set up, understand, and use python virtual environments (virtualenvs) in an easy to understand fashion. Hopefully by the end of this (or by using this as a reference), virtual environments will become practically second nature to you.

---

**What is a virtual environment?:**  
For our purposes, you can consider a virtualenv to essentially be an independant installation of python on your computer that you can switch into and out of freely. This environment of course comes with its own version of pip, its own setuptools, the whole minimal shebang so to speak. From the start you should be able to work on basic projects or begin installing things without having to worry too much about dependencies clashing with things you already have installed, or messing up your path by botching an install of something.

It is important to note that each environment is virtually independant and as such _will not_ see any previously installed python packages. Any system libraries that are not python dependant on the otherhand will be untouched. Switching to a python virtual environment should _only_ ever effect python, everything else will stay the same.

---

**How do I set up virtual environments for Python?:** 
While it is not required, I find that the best method of setting up and using Python virtual environments is to install and setup virtualenvwrapper. This wrapper grants some pretty nifty commands to make the whole process of dealing with virtualenvs stupid simple.

You can download virtualenvwrapper directly from pip by running:

```bash
sudo pip install virtualenvwrapper
```

or, if user level only is preferred:

```bash
pip install --user virtualenvwrapper
```
and this will download the basic scripts and files you will need. The next step in getting virtualenvwrapper set up is to set up your shell to use the wrapper and a space for the virtual environment files to be stored. You can set whichever folder you want to be the home for your virtualenvs but typically (and semantically) I find $HOME/.virtualenvs to be a pretty straightforward location. It is unlikely you will already have this directory so you can create it quickly with:

```bash
mkdir $HOME/.virtualenvs
```

Optionally, you can also create `project_home` directory but I have not used this and as such will not cover it (more information is available [in the official documentation](http://virtualenvwrapper.readthedocs.io/en/latest/projects.html) for those interested).

Once you have set up the directory you want to use, it is time to edit your shell startup script to use the new directory and source the wrapper. You can do this by adding the following lines to your shell startup script (`$HOME/.bashrc` if you use bash or `$HOME/.zshrc` if you use zsh):

```bash
export WORKON_HOME=$HOME/.virtualenvs
source /usr/share/virtualenvwrapper/virtualenvwrapper.sh
```

> **Note:**  
> The official documentation claims that the shell script is located in `/usr/bin/local` however on ubuntu systems this is not always correct. If for whatever reason it is not `/usr/share/virtualenvwrapper`, I would recommend checking their listed directory first and if that is unsuccessful you can locate it by running the command:

```bash 
find / -name "virtualenvwrapper.sh" 2> /dev/null
```
(doing this could take a few moments, so be patient). Just make sure that you properly `source` the file and you'll be fine.

After this is done, you need to either restart your shell or resource the startup file:  
_for bash:_  

```bash
source $HOME/.bashrc
```
_for zsh:_  

```bash
source $HOME/.zshrc
```

You should now be all set to start using virtualenvs.

---

**Creating a new virtual environment:**  
The beauty of virtualenvwrapper is that now you can easily create new environments with an easy to use and remember command:

```bash
mkvirtualenv ENVIRONMENTNAME
```
where ENVIRONMENTNAME is whatever you want to call your new virtual environment. Let's say for example I wanted to make a new environment for something and I wanted to call it "get-angry" (to work with `angr`). I would create this new environment by running:

```bash
mkvirtualenv get-angry
```

You can easily tell if it worked as you will see setuptools, pip, and wheel get installed and in addition you will now have `(get-angry)` prepended to your shell prompt (that means to the far left of your terminal, before your prompt). For any environment you create, you can tell if you are working in it based upon `(ENVIRONMENTNAME)` being prepended to your prompt (or depending on themes, somewhere on your prompt). If no parenthetical is placed on your prompt, you are not working in a virtual environment and should exercise your standard level of caution.

---

**Switching to a different environment:**    
Let's say you are working on more than one project or simply need to switch to a different virtualenv. You can easily switch between the environment you are working on by using the `workon` command added by the wrapper. If you want to switch to `myenvironment2` from your current python environment you would run:

```bash
workon myenvironment2
```
and you should see the prompt indicator change to `(myenvironment2)` (assuming you have actually made this environment using `mkvirtualenv myenvironment2`).

Sometimes you may have forgotten what you called a virtualenv and need to see a list of them (although tab-completion is usually supported). If at any moment you find yourself wishing for a list of your currently set up virtualenvs, run:

```bash
lsvirtualenv
```

What if you want to exit your virtualenv altogether and get back to your standard python environment? No problem, if at any point you want to go back to your standard environement simply run:

```bash
deactivate
```
If successful you will see the prompt indicator for your virtualenv disappear. You can further verify this by looking at your `$PATH` environment variable (the virtualenv directories are prepended while working in a virtualenv and removed on deactivation).

---

**Deleting virtual environments:**  
Oh no! You've massively broken something and things aren't behaving as you would expect (or maybe you simply want to blow an environment away). How can you get rid of your virtualenvironment and start again? Luckily this is pretty easy. First it is important to get out of whichever virtual environment you are looking to delete by either switching to a different one or deactivating it. Once you are no longer working on that particular virtual environment, you can run:

```bash
rmvirtualenv ENVIRONMENTNAME
```
> **Note:**  
> You will not be asked for confirmation so just as you would for `rm`, exercise caution when using this command.

---

**GOTCHAS: Using with tmux:**   
A very important caveat to using virtualenvs is when they are paired together with a multiplexor such as `tmux` (I don't use others and cannot speak for them). The problem present in default setups is that `tmux` inherits the environment of whichever shell it was launched from and if that shell happened to have been working in a virtualenvironment when `tmux` was launched then the environment at shell startup will be incorrect and you may get messages like `no module named virtualenvwrapper` among other things. Most of the commands will still work, but they are riddled with nasty looking error messages and that is no bueno. Luckily, in the case of `tmux` at least, the fix is quite easy. In your tmux startup script (`$HOME/.tmux.conf`) simply add a global reset for the VIRTUALENVWRAPPER_PYTHON variable by adding the following:

```bash
set-environment -g VIRTUALENVWRAPPER_PYTHON "/usr/bin/python"
```

Now when you start a tmux session, regardless of your current environment, that variable will get reset properly and you will no longer get any errors.

---

## Gotchas

**Certain incompatibilities:**  
Though I have never run into such an issue, I have been told that certain packages simply will not play nice within a virtual environment whether it be due to a missing library of whatever reason. Because I have never run into such an issue, I can't offer any assistance in solving such problems but I do feel it is at least important to note that such issue _might_ exist.

Note that virtualenv doesn't remove things from your path, it simply adds them, so you _shouldn't_ run into any issues where something installed fails to resolve.

--

**Development Installs:**  
Setuptools' development mode installation was not made with virtualenvs in mind and as such you must be mindful of what you do when dealing with projects in development mode. It is easy to ruin an installation by just renaming a file.

The development mode essentially works by symlinks. You can think of the "development" mode as making a symlink in `/lib/python*/site-packages` to wherever the project actaully exists on the filesystem (e.g. `$HOME/Documents/myPythonProject/`). The problem is that the entire process is _slightly_ more complex than that. It actually symlinks to a `<Project>.egg-info` file rather than the project directory itself. What this means is that should you move, delete, rename, or otherwise edit that file in **_any_** way, the entire development installation will be broken and it will be like the project is not installed at all.

**How does that tie into virtualenvs and git?**  
The primary issue lies in how the path is setup and the state of the project directory/files. 

**For virtualenvs:**  
Consider again what a virtualenv essentially is: a completely independent version of Python. This means there are _no_ shared libraries or modules between them (they each have their own copies). If only one virtualenv is ever used and installation of the project is done _exclusively_ within that single virtualenv, it is unlikely you will run into any issues but as soon as you start using even your systems python, in addition to _any other_ virtualenv, you will likely start running into issues.

Consider this case: I have two virtualenvs called `project` and `project-dev`. What would happen if I switch to `project` and ran `python setup.py develop` (assuming no prior installations of any kind)?

```bash
(project) ➜  package_example mkvirtualenv project
New python executable in /Users/user/.virtualenvs/project/bin/python
Installing setuptools, pip, wheel...done.
virtualenvwrapper.user_scripts creating /Users/user/.virtualenvs/project/bin/predeactivate
virtualenvwrapper.user_scripts creating /Users/user/.virtualenvs/project/bin/postdeactivate
virtualenvwrapper.user_scripts creating /Users/user/.virtualenvs/project/bin/preactivate
virtualenvwrapper.user_scripts creating /Users/user/.virtualenvs/project/bin/postactivate
virtualenvwrapper.user_scripts creating /Users/user/.virtualenvs/project/bin/get_env_details

(project) ➜  package_example pip list
pip (9.0.1)
setuptools (36.5.0)
wheel (0.30.0)

(project) ➜  package_example python setup.py develop
running develop
running egg_info
writing sample.egg-info/PKG-INFO
writing top-level names to sample.egg-info/top_level.txt
writing dependency_links to sample.egg-info/dependency_links.txt
reading manifest file 'sample.egg-info/SOURCES.txt'
writing manifest file 'sample.egg-info/SOURCES.txt'
running build_ext
Creating /Users/user/.virtualenvs/project/lib/python2.7/site-packages/sample.egg-link (link to .)
Adding sample 1.0.0 to easy-install.pth file

Installed /Users/user/Documents/package_example
Processing dependencies for sample==1.0.0
Finished processing dependencies for sample==1.0.0

(project) ➜  package_example pip list
pip (9.0.1)
sample (1.0.0, /Users/user/Documents/package_example)
setuptools (36.5.0)
wheel (0.30.0)

(project) ➜  package_example ls -l
total 8
drwxr-xr-x  4 user  staff  136 Sep 19 14:38 build
drwxr-xr-x  3 user  staff  102 Sep 19 14:38 dist
drwxr-xr-x  6 user  staff  204 Sep 19 15:06 namespace
drwxr-xr-x  6 user  staff  204 Sep 19 14:38 sample.egg-info
-rw-r--r--  1 user  staff  333 Sep 19 12:44 setup.py
```

As we can see, after the command runs our project will be reflected in `pip list` and point to the project directory on the filesystem. Now let's see what happens when that file gets moved:

```bash
(project) ➜  package_example pip list
pip (9.0.1)
setuptools (36.5.0)
wheel (0.30.0)

(project) ➜  package_example mv ../sample.egg-info .

(project) ➜  package_example pip list
pip (9.0.1)
sample (1.0.0, /Users/user/Documents/package_example)
setuptools (36.5.0)
wheel (0.30.0)
```

As you can see, when the `.egg-info` file is removed from the project directory (where python is expecting it), our entry for `sample` disappears from `pip list` but once it is moved back the entry reappears. But based on what was discussed earlier about how this process works, that should be expected. So let's look then at what happens if we try to install into a different virtualenv:

```bash
(project) ➜  package_example mkvirtualenv project-dev
New python executable in /Users/user/.virtualenvs/project-dev/bin/python
Installing setuptools, pip, wheel...done.
virtualenvwrapper.user_scripts creating /Users/user/.virtualenvs/project-dev/bin/predeactivate
virtualenvwrapper.user_scripts creating /Users/user/.virtualenvs/project-dev/bin/postdeactivate
virtualenvwrapper.user_scripts creating /Users/user/.virtualenvs/project-dev/bin/preactivate
virtualenvwrapper.user_scripts creating /Users/user/.virtualenvs/project-dev/bin/postactivate
virtualenvwrapper.user_scripts creating /Users/user/.virtualenvs/project-dev/bin/get_env_details

(project-dev) ➜  package_example pip list
pip (9.0.1)
setuptools (36.5.0)
wheel (0.30.0)

(project-dev) ➜  package_example ll
total 8
drwxr-xr-x  4 user  staff   136B Sep 19 14:38 build
drwxr-xr-x  3 user  staff   102B Sep 19 14:38 dist
drwxr-xr-x  6 user  staff   204B Sep 19 15:06 namespace
drwxr-xr-x  6 user  staff   204B Sep 19 14:38 sample.egg-info
-rw-r--r--  1 user  staff   333B Sep 19 12:44 setup.py

(project-dev) ➜  package_example python setup.py install
running install
running bdist_egg
running egg_info
writing sample.egg-info/PKG-INFO
writing top-level names to sample.egg-info/top_level.txt
writing dependency_links to sample.egg-info/dependency_links.txt
reading manifest file 'sample.egg-info/SOURCES.txt'
writing manifest file 'sample.egg-info/SOURCES.txt'
installing library code to build/bdist.macosx-10.12-intel/egg
running install_lib
running build_py
creating build/bdist.macosx-10.12-intel/egg
creating build/bdist.macosx-10.12-intel/egg/namespace
copying build/lib/namespace/__init__.py -> build/bdist.macosx-10.12-intel/egg/namespace
copying build/lib/namespace/sample.py -> build/bdist.macosx-10.12-intel/egg/namespace
byte-compiling build/bdist.macosx-10.12-intel/egg/namespace/__init__.py to __init__.pyc
byte-compiling build/bdist.macosx-10.12-intel/egg/namespace/sample.py to sample.pyc
creating build/bdist.macosx-10.12-intel/egg/EGG-INFO
copying sample.egg-info/PKG-INFO -> build/bdist.macosx-10.12-intel/egg/EGG-INFO
copying sample.egg-info/SOURCES.txt -> build/bdist.macosx-10.12-intel/egg/EGG-INFO
copying sample.egg-info/dependency_links.txt -> build/bdist.macosx-10.12-intel/egg/EGG-INFO
copying sample.egg-info/top_level.txt -> build/bdist.macosx-10.12-intel/egg/EGG-INFO
zip_safe flag not set; analyzing archive contents...
creating 'dist/sample-1.0.0-py2.7.egg' and adding 'build/bdist.macosx-10.12-intel/egg' to it
removing 'build/bdist.macosx-10.12-intel/egg' (and everything under it)
Processing sample-1.0.0-py2.7.egg
Copying sample-1.0.0-py2.7.egg to /Users/user/.virtualenvs/project-dev/lib/python2.7/site-packages
Removing sample 1.0.0 from easy-install.pth file
Adding sample 1.0.0 to easy-install.pth file

Installed /Users/user/.virtualenvs/project-dev/lib/python2.7/site-packages/sample-1.0.0-py2.7.egg
Processing dependencies for sample==1.0.0
Finished processing dependencies for sample==1.0.0

(project-dev) ➜  package_example pip list
pip (9.0.1)
sample (1.0.0, /Users/user/.virtualenvs/project-dev/lib/python2.7/site-packages/sample-1.0.0-py2.7.egg)
setuptools (36.5.0)
wheel (0.30.0)user
```

The directory structure and files all stayed the same, and of course being a completely independant version of python, the sample.egg-info is irrelevant at the moment right? Well, not quite. Keep in mind that the _files_ are **not** per virtualenv, they will be the same regardless of which environment is being worked in. What happens if we absent-mindedly delete the egg-info file because we "no longer need it"?

```bash
(project-dev) ➜  package_example ll
total 8
drwxr-xr-x  4 user  staff   136B Sep 19 14:38 build
drwxr-xr-x  3 user  staff   102B Sep 19 14:38 dist
drwxr-xr-x  6 user  staff   204B Sep 19 15:06 namespace
drwxr-xr-x  6 user  staff   204B Sep 22 10:35 sample.egg-info
-rw-r--r--  1 user  staff   333B Sep 19 12:44 setup.py

(project-dev) ➜  package_example rm -rf sample.egg-info

(project-dev) ➜  package_example pip list
pip (9.0.1)
sample (1.0.0, /Users/user/.virtualenvs/project-dev/lib/python2.7/site-packages/sample-1.0.0-py2.7.egg)
setuptools (36.5.0)
wheel (0.30.0)
(project-dev) ➜  package_example workon project                                                                                                                                                                                                       

(project) ➜  package_example pip list
pip (9.0.1)
setuptools (36.5.0)
wheel (0.30.0)
```

Because the project had been full installed to the project-dev virtualenv, removing the egg-info file from the project directory has no effect on it. The file is completely irrelevant as far as `project-dev` is concerned. But after switching back to `project` where the project was installed in development mode and running a list, we can notice that `sample` is nowhere to be found. For two or three environments this is not a huge issue but it can be quite a problem if several virtualenvs were using that particular project in development mode.

An even bigger issue lies in things like makefiles and people who like to keep their directories clean. Virtual Enviornments can already be quite tricky to use in conjunction with makefiles. Typically makefiles include a `clean` target which removes all metadata or seemingly "unnecessary" files. For python projects this tends to include `*.pyo`,`*.pyc`, `build/`, `dist/`, and `*.egg-info`. In development mode, it is safe to delete everything _except_ the `*.egg-info` directories. If the process of installing in a make file includes cleaning the eggs, it _will_ cause problems for any environment in which the project is installed in development mode. 

**For git and VCS:** 
The installation can and does depend on the branch of the project if it is in any kind of VCS (such as git). Remember that on git, the project can exist in many differing states. Various files may or may not exist within a particular branch or commit. The changing state **_will_** affect any and all development installations should anything happen to the `*.egg-info` files. Python, pip, setuptools, etc. do not integrate into git (or other SVCs) and can only see the project in the state it currently exists in on the filesystem, they cannot reference inactive branches. Another interesting factoid is that if the directory structure changes and there is still a `egg-info.py`, it might be in the wrong place. If the project folder gets pushed into a submodule and more added, the absolutepath to the egg has changed and as such the installation will no longer be valid. At the same time, even if you move the egg, the paths specified in several of the files would not be correct meaning it would still not work correctly.
