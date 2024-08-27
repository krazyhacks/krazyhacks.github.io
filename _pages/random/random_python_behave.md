---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Python Behave
layout: single
permalink: /random_behave/
sidebar:
   nav: "random_nav"
---

## Python behave 
There's already resources on the web. See [behave docs](https://behave.readthedocs.io/en/latest/tutorial.html).
I'm just trying to keep a record of things i've found useful.

[Automation Panda](https://automationpanda.com/2019/04/02/python-bdd-framework-comparison/)

In order to keep tests DRY and easy to maintain, feature files and associated steps need to be organised in some logical way so new tests can be inserted easily and test coverage easily assessed.

From the [python behave]() documentation, the steps folder must be below the feature folder,
and by default behave will ONLY look for step definitions in the root feature/steps directory - if you put your files in sub-directories then behave will not recognize them. 

If sub-directories are used then these tests can be executed by explicitly passing the folder of the feature file, however, simply running `behave` will not find feature/step files in sub-dir.  One could maintain the configuration file and paths to the search list, and if someone forgets to add it these tests will be silently lost/not executed. Tags can be used, but that doesn't really help solve the orgnisational structure, it will certainly help with execution of tests. [See](https://behave.readthedocs.io/en/latest/gherkin.html?highlight=directory#layout-variations)

A naming convention for feature/step files in conjunction with a heirarchical folder structure which logically maps to the product components would make better sense and help maintain and scale test automation using `behave`. 

Assuming some kind of ticketing system is used, like Jira, it would make sense to encode feature/user-stories IDs (and jira tasks if requried) into the framework to help trace tests back to the original requirements.

See [this](https://stackoverflow.com/questions/3365740/how-to-import-all-submodules/3365846#3365846) and [this](https://qc-analytics.com/en/2019/10/31/importing-behave-python-steps-from-subdirectories/)

Aiming to achieve a folder structure as follows, note, logically groups features found on the `Landing Screen`, if the Landing screen is changed, its easy to see whats covered, where to enhance, if required easier to move tests and avoid duplicate/redundant tests because existing ones are lost/burried in another file.
{% highlight bash linenos %}$ 
features/
	+-- environment.py
	+--/steps/
		+--/__init__.py          <<====== loads steps python files as modules from sub-dirs
	+--/Landing_screen/
	.	+--LandingScreen.feature
	.	+--Login.feature
	.	+--PasswordErrors.feature
	.	+--PasswordReset.feature
	.	+--Login.feature
	.		+--/steps/
				+--/__init__.py    <<==== steps folder a module
	.			+--LandingScreen.py
	.			+--LoginTests.py
	.			+--PasswordTests.py
	.			+--LogoutTests.py
	+--/Help_Screen
	.	+--Help.feature
	.		+--/steps/
				+--/__init__.py    <<==== steps folder a module
	.			+--HelpTests.py
	+--/Accounts_Settings
	+--/Current_Statistics_Screen
	+--/Historical_Statistics_Screen

{% endhighlight %}

* Make all `steps` subfolders into python modules by adding an empty `__init__.py` file
* Import dynamically all step submodules within the steps directory by adding the following at the steps/__init__.py

{% highlight python linenos %}
import os
import pkgutil

__all__ = []
PATH = [os.path.dirname(__file__)]
for loader, module_name, is_pkg in pkgutil.walk_packages(PATH):
    __all__.append(module_name)
    _module = loader.find_module(module_name).load_module(module_name)
    globals()[module_name] = _module
{% endhighlight %}

When executing single feature file, the imports will not dynamically load without the `-i` flag, otherwise `behave` errors with steps not defined because they're actucally located in another common folder.
{% highlight bash %}
$ behave -i path/to/feature/folder/my.feature

$ behave -n "Actual name of test"
{% endhighlight %}


### Data driven
Three ways (may be more) to drive tests using data;
  * table after a step
  * Scenario Outline with Examples
  * csv file
{% highlight bash %}

Scenario: some scenario
  Given a set of specific users
     | name      | department  |
     | Barry     | Beer Cans   |
     | Pudey     | Silly Walks |
     | Two-Lumps | Silly Walks |

 When we count the number of people in each department
 Then we will find two people in "Silly Walks"
  But we will find one person in "Beer Cans"

Scenario Outline: Blenders
   Given I put <thing> in a blender,
    when I switch the blender on
    then it should transform into <other thing>

 Examples: Amphibians
   | thing         | other thing |
   | Red Tree Frog | mush        |

 Examples: Consumer Electronics
   | thing         | other thing |
   | iPhone        | toxic waste |
   | Galaxy Nexus  | toxic waste |
{% endhighlight %}

### ToDo
check out pyhamcrest for better python assertion's
How to get one Scenario to call another scenario [See example](https://www.programcreek.com/python/example/82071/behave.when)
How to create/structure test modules for reuse
[See best practices](https://speakerdeck.com/ladylovelace/writing-automated-tests-best-practices-with-python-selenium-behave-and-page-objects?slide=26)
[BDD Automation panda](https://automationpanda.com/bdd/)
[Allure history reports](https://github.com/simple-elf/github-allure-history)
