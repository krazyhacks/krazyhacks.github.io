---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Homebrew
layout: single
permalink: /random_homebrew/
sidebar:
   nav: "random_nav"
---
{% include toc %}

## Homebrew

Homebrew is an easy way to install unix utilities on MAC OSx.  
From a terminal window run the following to install *homebrew* for mac
### Install *brew* 
   {% highlight bash linenos %} /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" {% endhighlight %}
### Check *brew* is installed
   {% highlight bash linenos scroll %}$ which brew
/usr/local/bin/brew
$ brew doctor
... checks health of brew installation 
$ brew help
Example usage:
  brew search [TEXT|/REGEX/]
  brew info [FORMULA...]
  brew install FORMULA...
  brew update
  brew upgrade [FORMULA...]
  brew uninstall FORMULA...
  brew list [FORMULA...]

Troubleshooting:
  brew config
  brew doctor
  brew install --verbose --debug FORMULA

Contributing:
  brew create [URL [--no-fetch]]
  brew edit [FORMULA...]

Further help:
  brew commands
  brew help [COMMAND]
  man brew
  https://docs.brew.sh{% endhighlight %}
### Managing packages through *brew*
{% highlight bash linenos %}$ brew list # List packages currently installed
$ brew search jq # search for pkgs to install
$ brew install jq # Install a package
$ brew --prefix jq # Find path to installed package
$ brew info jq # Dump info about the package{% endhighlight %}
Where does *brew* install files? Check the following dirs pay attention to symlinks
{% highlight bash linenos %}$ which jq
$ ls -ltr /usr/local/Cellar
$ ls -ltr /usr/local/opt/jq
$ ls -ltr /usr/local/bin/jq {% endhighlight %} 
### Homebrew & Cask
Cask is an extension to brew and provides the ability to install GUI apps on MAC - i think?
{% highlight bash linenos %}$ brew cask
Homebrew Cask provides a friendly CLI workflow for the administration
of macOS applications distributed as binaries.

$ brew cask list{% endhighlight %}
### Homebrew & tap
Taps are external sources for Homebrew formulae, casks and/or external commands. They can be created by anyone to provide their own formulae, casks and/or external commands to any Homebrew user.  f it’s on GitHub, users can install any of your formulae with brew install user/repo/formula.

see [brew docs](https://docs.brew.sh/How-to-Create-and-Maintain-a-Tap) for details.

### Homebrew path 
Once homebrew has been installed remember to update shell PATH so commands installed via brew are picked up first, edit  

**```$ vi ~/.bash_profile```**  

and add a line   

**```export PATH=/usr/local/bin:$PATH```**

