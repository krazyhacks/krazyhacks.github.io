---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Homebrew
layout: single
permalink: /random_homebrew/
sidebar:
   nav: "random_nav"
---

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

### Installing specific versions of a package
Get commit hash for the version you wish to install;
{% highlight bash linenos %} 
$ git@github.com:Homebrew/homebrew-core.git
$ git pull
$ git log | grep <package_name>
$ git lg | grep yarn
* 8df9766f938 - yarn: update 1.22.11 bottle. (20 hours ago) <BrewTestBot>
* 779f8ffa36c - yarn 1.22.11 (20 hours ago) <Alexander Bayandin>
* 1bbc97c7564 - yarn: update 1.22.10 bottle. (4 weeks ago) <rui>
* d79d96bfa8d - yarn: make fsevents test macOS specific (#77854) (3 months ago) <Michka Popoff>
* ca88452bace - yarn: add 1.22.10 bottle. (3 months ago) <BrewTestBot>
* fc97e8b4aaa - yarn: remove `bottle unneeded`, fix relocation issue (3 months ago) <Amar1729>
* 5218eb90b5a - yarn-completion: add 0.17.0 bottle. (3 months ago) <BrewTestBot>
* 5e56043fa42 - yarn-completion: remove `bottle :unneeded` (3 months ago) <Amar1729>
* c0ff72fc575 - yarn-completion 0.17.0 (#69358) (7 months ago) <Seeker>
* cc95928fff0 - yarn: update livecheck (#68602) (7 months ago) <Sam Ford>
* 86132ba2a62 - yarn 1.22.10 (10 months ago) <Manu Chambon>
* 62e99cd3099 - yarn 1.22.5 (#60260) (12 months ago) <chenrui>
* b6317455c5a - yarn: add license (12 months ago) <Dustin Rodrigues>
* 0168992e8c5 - yarn: fix RuboCop style. (1 year ago) <Mike McQuaid>
* e76bad35d18 - yarn-completion: add license (1 year, 1 month ago) <William Ma>
* b9e21442178 - yarn: shorten long lines. (1 year, 5 months ago) <Mike McQuaid>
* f7de0f116e0 - yarn 1.22.4 (#51360) (1 year, 5 months ago) <Daniel15's Build Bot>
* 50f1f7ceb9e - yarn 1.22.1 (#51215) (1 year, 5 months ago) <Daniel15's Build Bot>
* c0cba0db173 - yarn 1.22.0 (#49814) (1 year, 6 months ago) <Andrew Federico Turner>
* 994bccb8d02 - yarn 1.21.1 (1 year, 8 months ago) <Rui Chen>
* 088a48f0ee8 - yarn 1.19.2 (1 year, 9 months ago) <Rui Chen>

{% endhighlight %}
If you need `yarn` version `1.22.10`, get long commit hash;

{% highlight bash linenos %}
$ git log -1 <short_hash>
commit 1bbc97c75641f6bdf094e1eaac00c1310dd460cd
Author: rui <rui@chenrui.dev>
Date:   Tue Jul 13 03:22:19 2021 +0000

    yarn: update 1.22.10 bottle.

{% endhighlight %}

Use the URL method for installing that specific version of the app/util
(needs rechecking - method no longer supported?
{% highlight bash linenos %}
brew install https://raw.github.com/Homebrew/homebrew-core/<COMMIT>/Formula/<FORMULA>.rb
brew install https://raw.github.com/Homebrew/homebrew-core/1bbc97c75641f6bdf094e1eaac00c1310dd460cd/Formula/yarn.rb
brew install https://raw.githubusercontent.com/Homebrew/homebrew-core/1bbc97c75641f6bdf094e1eaac00c1310dd460cd/Formula/yarn.rb
{% endhighlight %}

### Install by downloading the formulae

Instead of
{% highlight bash linenos %}
brew install --HEAD -s https://raw.githubusercontent.com/cloudflare/homebrew-cloudflare/master/curl.rb
{% endhighlight %}

Do
{% highlight bash linenos %}
wget https://raw.githubusercontent.com/cloudflare/homebrew-cloudflare/master/curl.rb

brew install --HEAD -s curl.rb
{% endhighlight %}


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

### Setting up private homebrew
[see](https://medium.com/prodopsio/creating-homebrew-taps-for-private-internal-tools-c41363d58ab0)
