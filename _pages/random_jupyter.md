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
Magic commands in ipython are also available in Jupyter
#### help
`%lsmagic` - List magic commands
`%magic_command?` - Help on specific magic_command
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
Two useful commands for timinig `%%time`,  `%%timeit`  and `%timeit`
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
### Line profiling %lprun
Outlines the time performance of a python function, program or script

{% highlight python linenos %}
! pip install line_profiler
%load_ext line_profiler

def remove_dups1(lst):
	uniques=[]
	for name in lst:
		if name not in uniques:
			uniques.append(name)
	return uniques

%lprun -f remove_dups1 remove_dups1(lst)

def remove_dups2(lst):
	return list(set(list))

%lprun -f remove_dups2 remove_dups2(lst)


{% endhighlight %}
### Memory Profiling %mprun
#### Install mprun pakage
{% highlight python linenos %}
!pip install  memory_profiler

%load_ext memory_profiler

{% endhighlight %} 

#### Save the function/script as a file
Using `%%file my_file.py` will save the contents of that jupyter cell as a file
{% highlight python linenos %}
%%file my_file.py
def calc_apply(df):
    column = df['COMB (mpg)']
    new_vals = column.apply(lambda x: x* 0.425)
    df['kml'] = new_vals
    return df

def calc_listcomp(df):
    column = df['COMB (mpg)']
    new_vals = [x*0.425 for x in column]
    df['kml'] = new_vals
    return df

def calc_direct(df):
    column = df['COMB (mpg)']
    new_vals = column*0.425
    df['kml'] = new_vals
    return df

def calc_numpy(df):
    column = df['COMB (mpg)'].values
    new_vals = column*0.425
    df['kml'] = pd.Series(new_vals)
    return df
{% endhighlight %} 

#### Load the function/script 
Next, load the memory profiler extension and import your functions from the file.
{% highlight python linenos %}
// from my_file import func_name
// %mprun -f func_name func_name(params) 
%load_ext memory_profiler

from my_file import calc_apply, calc_listcomp, 
                    calc_direct, calc_numpy

{% endhighlight %} 
[See original post on lprun,mprun](https://towardsdatascience.com/effectively-use-timeit-lprun-and-mprun-to-write-efficient-python-code-f06fb8457049)
### Advanced options ;-)
[See](https://www.dataquest.io/blog/advanced-jupyter-notebooks-tutorial/)

