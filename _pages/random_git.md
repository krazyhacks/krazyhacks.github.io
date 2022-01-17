---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Git
layout: single
permalink: /random_git/
sidebar:
   nav: "random_nav"
---

### Ammend author of git commits
Commited changes as the wrong author. This happened because the git config wasn't setup correctly and could have
been avoided.

Update the very last commit using;
   {% highlight bash linenos %} git commit --amend --author"KrazyHacks <hacks.krazy@gmail.com> {% endhighlight %}
This will update the very last commit with the ``edited`` version, correcting the authors name
Why would you want to do this? ... if you have multiple accounts and accidentally commited as the wrong user? otherwise noooo idea, but good to know.
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
#### Debug git config
{% highlight bash linenos %}
git config --list --show-origin
{% endhighlight %}

### Rename branch 
#### local branch.
Rename current infocus/active branch.

{% highlight bash linenos %}
git branch -m new-name
{% endhighlight %}

Rename a different branch:
{% highlight bash linenos %}
git branch -m old-name new-name # Delete the old-name remote branch and push the new-name local branch.
{% endhighlight %}

#### Remote branch.
{% highlight bash linenos %}
git push origin :old-name new-name   # Reset the upstream branch for the new-name local branch.
				     # Switch to the branch and then:
git push origin -u new-name

{% endhighlight %}

### Rename tag
{% highlight bash linenos %}
You will need to find the commit id to tag the new commit. 
git log --oneline                        # Find & Save the <commitId>.
git tag -a <new tag name> -m <message> <commitId> # The tag the commit
git tag -d <original tag name>           # Delete the original tag:
git push --tags                          # Push your tag to remote:
git push origin :<original tag name>     #  Delete your tag from remote:
{% endhighlight %}


### Get list of tags
{% highlight bash linenos %}
git log --tags --no-walk --date=iso-local --pretty='%C(auto)%h %cd%d %s' 

8ab7a3b 2021-11-11 10:04:06 +0000 (HEAD -> release/1.2.1, tag: web_1.2.1, origin/release/1.2.1) Bump pkg version to 1.2.1 to alighn with release
9bded62 2021-10-04 10:29:25 +0100 (tag: web_1.2.0, origin/original_release/1.2.0, rc/1.2.0) Merge pull request #86 from blah/version_rev
{% endhighlight %}

### Squash and rebase
It’s simple – before you merge a feature branch back into your main branch (often master or develop), your feature branch should be squashed down to a single buildable commit, and then rebased from the up-to-date main branch.
[read it here](https://blog.carbonfive.com/always-squash-and-rebase-your-git-commits/)
[more](https://www.internalpointers.com/post/squash-commits-into-one-git)
[and more](https://levelup.gitconnected.com/how-to-squash-git-commits-9a095c1bc1fc)
[and more](https://www.git-tower.com/learn/git/faq/git-squash/)

### Merge and Rebase
[see](https://www.freecodecamp.org/news/the-ultimate-guide-to-git-merge-and-git-rebase/)

### Stash
This allows you too save work in progress (local, not pushed) so you can context switch onto anoher branch.

#### stash untracked files
{% highlight bash %}
git stash -u or git stash --include-untracked stash untracked files.
git stash -a or git stash --all stash untracked files and ignored files.
{% endhighlight %}

#### Stash a specific file
{% highlight bash %}
git stash --patch
git stash -p
{% endhighlight %}

#### stash list, save
{% highlight bash %}
$ git stash list
$ git stash save "<descriptioon>"
$ git stash save "replace hyphen with underscores"
Saved working directory and index state On master: replace hyphen with underscores" 

$ git stash list
stash@{0}: On master: remove semi-colon from schema
stash@{1}: WIP on master: d7435644 Feat: configure graphql endpoint
{% endhighlight %}

#### Retrieve stashed code
{% highlight bash %}
git stash pop stash@{1}
git stash apply stash@{1}
{% endhighlight %}

#### stash clean-up
{% highlight bash %}
$ git stash clear # empties the stash list by removing all the stashes.
$ git stash drop <stash_id> deletes a particular stash from the stash list.
{% endhighlight %}

#### Stash diffs
{% highlight bash %}
git stash show <stash_id>
git stash show stash@{0} --patch
{% endhighlight %}

#### stash checkout to branch
{% highlight bash %}

 git stash branch test_2 stash@{0}
{% endhighlight %}
 
#### Stop tracking a file in git
{% highlight bash %}
add foobar.sh to .gitignore
git rm -r --cached foobar.sh
git add foobar.sh
git commit -m "Commit message"
{% endhighlight %}

### What's changed
{% highlight bash linenos %}
See what the difference/changed with file(s) staged.
git diff --cached

See history of changes 
git log --follow --all -p dir/file.c

See lists of commits that has changed a specific part of a file (implemented in Git 1.8.4).

The result returned would be the list of commits that modified this particular part. Command:

git log --pretty=short -u -L <upperLimit>,<lowerLimit>:<path_to_filename>
where upperLimit is the start line number and lowerLimit is the ending line number of the file.
{% endhighlight %}

### Which commit removed a line

{% highlight bash linenos %}
git log -p 
git log -S<string> path/to/file
git log -G<regex> path/to/file
git log -c -S 'Received APNS deviceToken' AppDelegate.m  # Best result using this cmd
git blame --reverse <first_commit_hash_containing_string>.. path/to/file  # not sure how useful
{% endhighlight %}

  *  find out who changed the lines 2 to 7 in file build.gradle
{% highlight bash %}
git blame -L 2,+7 -- build.gradle
{% endhighlight %}

  * # See the history of change for line 2 to 7 in file build.gradle.
{% highlight bash %}
git log --pretty=short -u -L 2,+7:build.gradle
{% endhighlight %}
### Stop tracking files
{% highlight bash %}
add foobar.sh to .gitignore
git rm -r --cached foobar.sh
git add  .gitignore
git commit -m "Commit message"
{% endhighlight %}

### Rename branch
{% highlight bash %}
git checkout <old_name>
git branch -m <new_name>
git push origin -u <new_name>          \___ obviously need to be sure others haven't cloned the repo/checkdout
git push origin --delete <old_name>    /

{% endhighlight %}
### GIT Python module 

### GIT Log 
#### Aliases

{% highlight bash %}
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
        git lg

{% endhighlight %}

### Recovering from Git
#### Accidental deletion from reset --hard
Ran `git reset --hard <hash>` to revert a change after committing some new files. Bad idea!
This reverted back to the previous commit and deleted all the new files created.  Here's how i recovered the lost work.
{% highlight bash linenos %}
$ git reflog show    ................ 
ed7937b (HEAD -> master) HEAD@{0}: reset: moving to ed7937b
2495666 HEAD@{1}: commit: screenshot showing typical setup in xcode
ed7937b (HEAD -> master) HEAD@{2}: commit: Initial commit of pi work
064b846 HEAD@{3}: commit: how to inspect the contents of CSR, pks, p12 files
a643195 HEAD@{4}: commit: using rbenv to manage ruby and ruby pkgs/gems
6f87bcd HEAD@{5}: commit: Initial work on using python behave
9b0af92 HEAD@{6}: commit: usefull pkgs and links to use in python
399e3f9 HEAD@{7}: commit: starting point for setting up a homebreww tap
33e7e61 HEAD@{8}: commit: backlog of updates
101e77c HEAD@{9}: commit: link info for open homeautomation

{% endhighlight %}
Note above after running `git reset --hard ed7937b`, commit 2495666 which contained several new files
were lost, deleted, stupidly expecting them to magically reappear as uncommited files. `git status` would show
everything was upto date :-(

To recover from this, run the following, note `HEAD@{2}` is from the original ed7937b commit;

{% highlight bash linenos %}
$ git reset HEAD@{2}
Unstaged changes after reset:
D       _pages/random_aws.md_
D       _pages/random_brew.md_
D       _pages/random_ios.md_
D       _pages/random_jupyter.md_
D       _pages/random_lastpass.md_
D       _pages/random_pi.md_
D       _pages/random_python_flask.md_
D       _pages/random_robotics.md_
D       _pages/random_shell_stuff.md_
D       _pages/random_terraform.md_
D       assets/images/XcodeCertSigningError.png
{% endhighlight %}

{% highlight bash linenos %}
$ git status
On branch master
Your branch is ahead of 'origin/master' by 9 commits.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    _pages/random_aws.md
        deleted:    _pages/random_brew.md
        deleted:    _pages/random_ios.md
        deleted:    _pages/random_jupyter.md
        deleted:    _pages/random_lastpass.md
        deleted:    _pages/random_pi.md
        deleted:    _pages/random_python_flask.md
        deleted:    _pages/random_robotics.md
        deleted:    _pages/random_shell_stuff.md
        deleted:    _pages/random_terraform.md
        deleted:    assets/images/XcodeCertSigningError.png

{% endhighlight %}

Following command "should" recover files under a folder lost-found;
{% highlight bash linenos %}
$ git fsck --lost-found
Checking object directories: 100% (256/256), done.
Checking objects: 100% (158/158), done.
dangling blob afd34d9612c7abc4e920da9aef04c81356a1370a
dangling commit cd2b1be29bee1c89383de3aeaecda66d4dd9b9c0

$ tree .git/lost-found
.
├── commit
│   └── cd2b1be29bee1c89383de3aeaecda66d4dd9b9c0
└── other
    └── afd34d9612c7abc4e920da9aef04c81356a1370a

2 directories, 2 files

{% endhighlight %}
But only created blobs for two files, and they can be inspected using `git show <filename>`
Instead, since we know which files were deleted from the above `git status` command, each file
was restored using `git recover <filename>`

Having commited files accidentally, i should have reverted the commit`



#### Misc
{% highlight bash %}
git log --left-right --graph --cherry-pick --oneline develop...master
git log --left-right --oneline --stat --graph --cherry-pick 318bfce0a553c4b9619c02d227dddb07c50b9f5c...fb5d7e14c4f033db73e6bad9d40b785ba528db6e
git rev-list --left-right --count develop...master
git difftool --tool=vimdiff master..release/0.15.0S
git for-each-ref --format="%(refname:short) | %(creatordate)" refs/tags/\*   # Get date when tags were created.

{% endhighlight %}
[Python module for git](https://gitpython.readthedocs.io/en/stable/tutorial.html)

### Divergent branches
TODO : document how this can happen...

When merging the release branch into master, conflicts were raised. After resolving the conflicts in favour of the release branch the resultant showed difference with the orginal release branch.
{% highlight bash %}
git checkout master
git checkout -b release/1.2.1
git merge --no-commit -s ours release/1.2.1_orig
git rm -rf .
git checkout release/1.2.1_orig -- .
git commit
{% endhighlight %}

[How to make two branches indentical](https://stackoverflow.com/questions/36320517/making-two-branches-identical)

### GIT for multiple acounts
[See](https://dev.to/fadilnatakusumah/how-to-separate-your-git-for-work-and-git-for-personal-2n8b)
