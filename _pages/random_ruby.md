---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Ruby
layout: single
permalink: /random_ruby/
sidebar:
   nav: "random_nav"
---

### Ruby 
##### OMG Ruby !! 
What a painful experience!! Installed different versions of ruby, MacOS versions, versions from `homebrew`, then, 
 on top of that installed `gems` left, right and center. Oh, not forgetting random sprinkling of `sudo` just to get things _running_ so i can do my day job! and not really understanding the consequences!  Then, one day I receive an 
OS update, work collegue delivers a new tool, dependent on new versions of gems / ruby. I think you can guess! stuff
just stopped working...

##### Cleanup
Attempting to cleanup, 
* Which version of ruby is my shell picking up? 
* Which `gem` version is in my path, is it compatible with the version of `ruby`? 
* Where are the `gem` pkg files being installed?  
* Why can't I uninstall `gems` ? 
* Commands still found in my path after I (thought) I had uninstalled !?

Find whats in your path, find all versions of `ruby`, `gem` executeables and start uninstalling.
{% highlight bash linenos %}
$ echo ${PATH}   ............. check howmany references to ruby installs
$ which -a ruby  -.-.-.-.-.-.- location of each ruby installed.
$ which -a gem
$ gem env home   ............. find where "gem" is installing gem files.
$ gem uninstall gem_name
$ brew info ruby ............. find versions of ruby installed via homebrew.
{% endhighlight %}

Each version of `ruby/gem` will have files installed in specific locations, target each one in turn and uninstall/cleanup;
{% highlight bash linenos %}
$ /usr/local/opt/ruby/bin/ruby --version
3.0.2
$ /usr/local/opt/ruby/bin/gem --version
3.2.22
$ /usr/local/opt/ruby/bin/gem env home
/usr/local/lib/ruby/gems/3.0.0
$ /usr/local/opt/ruby/bin/gem uninstall jekyll
$ ls -l /usr/local/lib/ruby/gems/3.0.0/gems/          <---- gem removed from this location.
$ brew uninstall ruby
{% endhighlight %}

#### rbenv
