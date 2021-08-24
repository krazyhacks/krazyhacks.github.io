---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Jekyll
layout: single
permalink: /jekyll/
sidebar:
   nav: "jekyll_nav"
---

### Jekyll

This site is powered by Jekyll using the minimal mistakes theme
![alt text](/assets/images/jekyll_howto.png "Map of files that update these pages.")
#### How this site was put together.
{% highlight bash linenos %}$ git clone git@github.com:krazyhacks/krazyhacks.github.io.git 
$ bundle exec jekyll serve
{% endhighlight %}

Add a new item to left hand menu ;
{% highlight bash linenos %}
$ vi _data/navigation.yml
{% endhighlight %}

Add a new page to match menu and customise;
{% highlight bash linenos %}
$ cd \_pages
$ cp random_template.md name_of_new_page.md
$ vi name_of_new_page.md
{% endhighlight %}

##### GitHub
##### GitHub repo
##### Pre-Requisite packages/tools

### Markdown cheat sheet
[Markdown cheat sheet](https://www.markdownguide.org/cheat-sheet/ "Cheat Sheet")
![alt text](/assets/images/sample.png "Sample Image")
I need to display some  
	`code`
try embedding video
<iframe width="560" height="315" src="http://www.youtube.com/embed/PWf4WUoMXwg" frameborder="0"> </iframe>

