---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Bash Sed Awk cmdline stuff
layout: single
permalink: /random_unix/
sidebar:
   nav: "random_nav"
---

## Template 
### Template section 
See [pip Installation](https://pip.pypa.io/en/stable/installing/).

{% highlight bash linenos %}$ echo "dummy text" {% endhighlight %}

### UUID
random id, lower case, hypens stripped out 
{% highlight bash linenos %}$ uuid=$(uuidgen | awk '{print tolower($0)}' | sed 's/\-//g'){% endhighlight %}
