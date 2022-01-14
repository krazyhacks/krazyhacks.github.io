---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Jupyter Notes
layout: single
permalink: /random_jupyter/
sidebar:
   nav: "random_nav"
---

## Jupyter Labs/Notes 
### Installation 
See [Jupyter.org](https://jupyter.org/).
[Quick guide](https://realpython.com/jupyter-notebook-introduction/).
{% highlight bash linenos %}
$ python3 -m venv venv-jupyter
$ pip install jupyterlab 
$ pip install notebook
$ pip install jupyterthemes
{% endhighlight %}
Remember to start jupyter notes from a venv to pick-up locally installed pkgs
or modules being developed.
## Tips 
#### extend cells
Copy/Run following in first cell
{% highlight python linenos %}
from IPython.core.display import display, HTML
display(HTML("<style>.container { width:100% !important; }</style>"))
{% endhighlight %}
#### Pretty Display of Variables
{% highlight python linenos %}
from IPython.core.interactiveshell import InteractiveShell
InteractiveShell.ast_node_interactivity = "all"
{% endhighlight %}

### Keyboard short-cuts
* CMD+SHIFT+P `Keyboard shortcut help`
* SHIFT+J , SHIFT+K  `Highlight up & Down (then copy,cut,paste)`
* SHIFT+M `Merge highlighted cells`

### iPython Magic
#### %env
Set environment variables
{% highlight python linenos %}
# Running %env without any arguments
# lists all environment variables# The line below sets the environment
# variable
%env OMP_NUM_THREADS%env OMP_NUM_THREADS=4
{% endhighlight %}
{% highlight python linenos %}
env: OMP_NUM_THREADS=4
{% endhighlight %}
#### %run
Execute python code from external `.py` file  AND other jupyter notebooks
`%run` is not the same as importing a python module
{% highlight python linenos %}
# this will execute and show the output from
# all code cells of the specified notebook
%run ./two-histograms.ipynb
{% endhighlight %}
#### %load
Insert script from an external file;
{% highlight python linenos %}
# Before Running
%load ./hello_world.py
{% endhighlight %}

{% highlight python linenos %}
# After Running
# %load ./hello_world.py
if __name__ == "__main__":
 print("Hello World!")
{% endhighlight %}
{% highlight python linenos %}
Hello World!
{% endhighlight %}
#### %who
List all variables in global scope
{% highlight python linenos %}
one = "for the money"
two = "for the show"
three = "to get ready now go cat go"
%who str

one three two
{% endhighlight %}
#### Timing
Two useful commands for timinig `%%time` and `%timeit`
{% highlight python linenos %}
%%time
import time
for _ in range(1000):
 time.sleep(0.01) # sleep for 0.01 seconds

CPU times: user 21.5 ms, sys: 14.8 ms, total: 36.3 ms Wall time: 11.6 s
{% endhighlight %}
For `%%timeit` python uses _timeit module_ , takes the average of 100K runs
{% highlight python linenos %}
import numpy
%timeit numpy.random.normal(size=100)

100000 loops, best of 3: 5.5 µs per loop
{% endhighlight %}
### Advanced options ;-)
[See](https://www.dataquest.io/blog/advanced-jupyter-notebooks-tutorial/)
