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
Commited changes as the wrong author. This happened because the git config wasn't setup correctly and could have
been avoided.

Update the very last commit using;
   {% highlight bash linenos %} git commit --amend --author"KrazyHacks <hacks.krazy@gmail.com> {% endhighlight %}
This will update the very last commit with the ``edited`` version, correcting the authors name
### Interactive rebase
Updating author's name for multiple commits using interactive rebase
    * Find the first "good"  commit and use its hash for the rebase cmd
      {% highlight bash linenos %}$ git rebase -i -p <hash_of_last_good_commit> {% endhighlight %}
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

Stopped at 0bdc666... adding howto amend authors name in git commits
You can amend the commit now, with

	git commit --amend 

Once you are satisfied with your changes, run

	git rebase --continue

{% endhighlight %}
Now amend the author of the commit
{% highlight bash linenos %}$ git commit --amend --author="KrazyHacks <hacks.krazy@gmail.com>" --no-edit
[detached HEAD 64f8ec3] adding howto amend authors name in git commits, show commit being edited
 Author: KrazyHacks <hacks.krazy@gmail.com>
 Date: Mon Dec 30 09:33:10 2019 +0000
 1 file changed, 18 insertions(+), 1 deletion(-) {% endhighlight %}
Move to the next commit
{% highlight bash linenos %}$ git rebase --continue
warning: git rebase --preserve-merges is deprecated. Use --rebase-merges instead.
Stopped at dc0f5cb... adding howto amend authors name in git commits, show rebase cmd being updated
You can amend the commit now, with

	git commit --amend 

Once you are satisfied with your changes, run

	git rebase --continue
git commit --amend --author="KrazyHacks <hacks.krazy@gmail.com>" --no-edit
[detached HEAD 64f8ec3] adding howto amend authors name in git commits, show commit being edited
 Author: KrazyHacks <hacks.krazy@gmail.com>
 Date: Mon Dec 30 09:33:10 2019 +0000
 1 file changed, 18 insertions(+), 1 deletion(-)
git rebase --continue
warning: git rebase --preserve-merges is deprecated. Use --rebase-merges instead.
Successfully rebased and updated refs/heads/master.
{% endhighlight %}

### Git Config
#### Set name and email
GIT Configs are either, system, global or local i.e. per repository
The above 
{% highlight bash linenos %}# Updates /etc/gitconfig (system wide that affects all user on the same box)
$ git config --global user.name
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com

# Updates ~/.gitconfig (Affects all projects used by the specific users i.e. home directory)
$  git config --global user.name
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com

# Updates ./git_project_folder/.gitconfig (Affects specific project)
$  git config --local user.name
$ git config --local user.name "John Doe"
$ git config --local user.email johndoe@example.com

{% endhighlight %}
