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
Make changes in `krazyhacks.github.io` repo, create a PR and merge into develop.
When happy create a PR into `master`, once merged, github will build the site.
##### Pre-Requisite packages/tools

### Markdown cheat sheet
[Markdown cheat sheet](https://www.markdownguide.org/cheat-sheet/ "Cheat Sheet")
![alt text](/assets/images/sample.png "Sample Image")
I need to display some  
	`code`
try embedding video
<iframe width="560" height="315" src="http://www.youtube.com/embed/PWf4WUoMXwg" frameborder="0"> </iframe>

### Timeline

[Timeline](https://thecdil.github.io/timelinejs-template/about.html "timeline")

### Jekyll Folder structure
 
{% highlight bash linenos %}

_data/navigation.yml
_pages/file.md
   |-- aws
        |--aws_file.md
   |-- embedded
        |--embedded_file.md
   |-- hardware
        |--hw_file.md
_posts
   |--
assets
   |-- css
   |-- images
        |-- image_file.jpg
{% endhighlight %}

#### Navigation and permalinks convention

{% highlight bash linenos %}
cloud_nav:
  - title: Cloud
    children:
      - title: "AWS"
        url: /aws/aws_cli/
      - title: "IOT"
        url: /cloud/iot/
{% endhighlight %}
* File `_data/navigation.yml` groups lists of url links together for displaying in the right hand panel
* `main:` section is reserved for the sections listed along the top left hand side of the page.
* In `_data/navigation.yml` create page specific groups of links by adding a new tag like `cloud_nav`, named approriately.
* `navigation.yml` to use the links specified as permalink text in `_pages`, like `/aws/aws_cli` and `/cloud/iot/`. 
* Choose permalinks that match the target page in top level folder or pages in subfolder.
