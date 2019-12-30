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
Updating author's name for multiple commits using interactive rebase
    * Find the first "good"  commit and use its hash for the rebase cmd
      {% highlight bash linenos %}git rebase -i -p <hash_of_last_good_commit> {% endhighlight %}
This will launch the editor to pick the commits to amend;
      {% highlight bash linenos %}
pick bf7d674 Initial commit, having written so much without carring about version control and git
pick 328eedb Inital commit on problems with ssh keys and github
pick 5d22471 Adding python behave section
pick db956d9 updating python notes, virtual environments
pick 7204f96 Initial commit for python behave section
pick 98bdaec git error on push
edit 0bdc666 adding howto amend authors name in git commits

# Rebase 4816d05..0bdc666 onto 4816d05 (7 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending 
{% endhighlight %}

