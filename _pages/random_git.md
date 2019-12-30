---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Git
layout: single
permalink: /random_git/
sidebar:
   nav: "random_nav"
---
{% include toc %}

### Ammend author of git commits
Commited changes as the wrong author. Update the very last commit using;
   {% highlight bash linenos %} git commit --amend --author"KrazyHacks <hacks.krazy@gmail.com> {% endhighlight %}
This will update the very last commit with the ``edited`` version, correcting the authors name
### Interactive rebase
Updating multiple commits using interactive rebase
    * Find the first "good"  commit and use its hash for the rebase cmd
      {% highlight bash linenos %}git rebase -i -p <hash_of_last_good_commit> {% endhighlight %}

