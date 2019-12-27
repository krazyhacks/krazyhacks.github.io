---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Python stuff
layout: single
permalink: /random_python/
sidebar:
   nav: "random_nav"
---
{% include toc %}

## Python 
Problem, we have multiple versions of python, each version of python can have different set of module/package versions. Computer OS's come with specific versions, different production stacks may have different setups. How do we manage to test & develop across all flavours using the same development machine?

### pyenv
[pyenv](https://github.com/pyenv/pyenv) is a mature tool for installing and managing multiple Python versions on macOS. Despite the similarity in names (pyvenv vs pyenv), pyenv is different in that its focus is to help you switch between Python versions on a system-level as well as a project-level. While the purpose of pyvenv is to separate out modules, the purpose of pyenv is to separate Python versions.

{% highlight bash linenos %}$ brew install pyenv
$ pyenv versions
* system
$ python --version
Python 2.7.10
$ pyenv install 3.7.3  # This may take some time
$ pyenv versions
* system
  3.7.3 {% endhighlight %}
Alternative installer
{% highlight bash linenos %}$ curl -L https://raw.githubusercontent.com/yyuu/pyenv-installer/master/bin/pyenv-installer | bash {% endhighlight %}

You can manage which Python you’d like to use in your current session, globally, or on a per-project basis as well. pyenv will make the python command point to whichever Python you specify. Note that none of these overrides the default system Python for other applications, so you’re safe to use them however they work best for you within your Python environment;
{% highlight bash linenos %}$ pyenv global 3.7.3
$ pyenv versions
  system
* 3.7.3 (set by /Users/dhillard/.pyenv/version)

$ pyenv local 3.7.3
$ pyenv versions
  system
* 3.7.3 (set by /Users/dhillard/myproj/.python-version)

$ pyenv shell 3.7.3
$ pyenv versions
  system
* 3.7.3 (set by PYENV_VERSION environment variable)

$ python --version
Python 3.7.3{% endhighlight %}

### Virtual Environments
Why? Virutal environments allow you to isolate python projects from the pre-installed stuff thats comes with the OS. Also allows each python project to have its own dependancies that won't interfere/disrupt other python projects on the same box.  This is especially important when using third-party packages, running different versions of python etc.

Which one? Several to choose from; virtualenv, python environment wrapper (pew), venv, pipenv, pyenv 
#### venv
[venv](https://docs.python.org/3/library/venv.html) ships with Python >= v3.3. venv builds on top of the original independent [virtualenv project](https://virtualenv.pypa.io/en/stable/). venv is useful if working with only one version of python, and, since its bundled with python no additional software needs to be installed to use it.
{% highlight bash linenos %}$ python3 -m venv ~/.virtualenv/my_environments/foo     # creates a folder ~/.virtualenv/my_environments/foo
$ source ~/.virtualenvs/my_environments/foo/
$ deactivate{% endhighlight %}


#### virtualenv
If you're using python versions <3.3 then [virtualenv](https://virtualenv.pypa.io/en/stable/) is usually installed (via pip)
{% highlight bash linenos %}$ pip install virtualenv
$ mkdir ~/project/python-virtual-environments && cd ~/project/python-virtual-environments
$ # Python 2
$ virtualenv my_env		# creates the environment
$ source env/bin/activate	# starts the environment
(env) $
(env) $ deactivate		# to exit the environment
${% endhighlight %}

#### pyvenv
Between python v3.3 and v3.4 the recommended way to create virtual environments was by using the pyvenv cmd which comes with python3.
From python v3.6 onwards that recommendation changed to `python3 -m venv blah`

#### pyenv-virtualenv
[pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv) enhances pyenv by adding a subcommand to manage virtual environments using multiple python versions.
{% highlight bash linenos %}$ brew install pyenv-virtualenv	# Use brew if thats how pyenv was installed
$ eval "$(pyenv init -)" 	# Add to shell profile, only need to do this once.

# This line runs activate/deactivate automatically when entering/leaving 
# dir containing a .python-version file file that contains the name 
# of a valid virtual environment as shown in the output of pyenv virtualenvs
$ eval "$(pyenv virtualenv-init -)"	
{% endhighlight %}

Usage, create a virtualenv using the current version of Python in use by pyenv;
{% highlight bash linenos %}$ pyenv version
3.4.3 (set by foobar)
$ pyenv virtualenv my_venv3.4.3
{% endhighlight %}
Create a virtual environment using a specific version of python
{% highlight bash linenos %}$ pyenv version
3.4.3 (set by foobar)
$ pyenv virtualenv 2.7.10 my_venv-2.7.10
{% endhighlight %}

{% highlight bash linenos %}$ pyenv virtualenvs 		# List virtual environments
$ pyenv activate <name>		# activate virtualenv
$ pyenv deactivate	# deactivate
$ pyenv uninstall my-virtual-env	 # Delete venvs, two ways not sure 
$ pyenv virtualenv-delete my-virtual-env # what the diff is?
{% endhighlight %}
What’s really nice about pyenv-virtualenv is that you can configure a virtual environment using the pyenv local command and have pyenv-virtualenv auto-activate the right environments as you switch to different directories:

{% highlight bash linenos %}
$ pyenv virtualenv 3.7.3 proj2
$ cd /Users/dhillard/proj1
$ pyenv local proj1
(proj1)$ cd ../proj2
$ pyenv local proj2
(proj2)$ pyenv versions
  system
  3.7.3
  3.7.3/envs/proj1
  3.7.3/envs/proj2
  proj1
* proj2 (set by /Users/dhillard/proj2/.python-version)
{% endhighlight %}
pyenv-virtualenv uses `python -m venv` if it is available and the virtualenv command is not available.

### Python PIP
pip is the package installer for Python. You can use pip to install packages from the Python Package Index and other indexes.

pip is already installed if you are using Python 2 >=2.7.9 or Python 3 >=3.4 downloaded from python.org or if you are working in a Virtual Environment created by virtualenv or pyvenv.
See [pip Installation](https://pip.pypa.io/en/stable/installing/).

Use pip to install python packages;
{% highlight bash linenos %}$ pip <pip arguments>
$ pip help	# usefull summary of available commands
$ python -m pip <pip arguments> # Run pip commands through python
$ pip install <package_name>
$ pip install package_name==1.0.4 # Install specific version
$ pip install 'package_name>=1.0.4' # Install minimum version{% endhighlight %}

### Requirements file
A "requirements" file capture a list of items to be installed using `pip install`
When working with virtual environments, its common to install specific packages/package versions whilst developing new code.

The requirements file can capture the specific list of packages using;

{% highlight bash linenos %}$ pip freeze > requirements.txt{% endhighlight %}

_requirements.txt_ can then be checked into version control with your project.

The same environment can be restored by reinstalling the same set of packages within a virtual environment, using;
{% highlight bash linenos %}$ pip install -r requirements.txt{% endhighlight %}

