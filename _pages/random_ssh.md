---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: SSH
layout: single
permalink: /random_ssh/
sidebar:
   nav: "random_nav"
---

### SSH Keys and github
Problem, I have two github accounts, a personal and one specific for work. Haven't found a convenient way to
to work with GITHUB on both personal and work stuff on the same computer. Access to the correct git account 
gets confused when both ssh-keys are loaded.

   {% highlight bash linenos %}
$ ssh-add -l
The agent has no identities.

$ ssh-add ~/.ssh/id_rsa.work
Enter passphrase for /Users/krazyhacker/.ssh/id_rsa.work: 
Identity added: /Users/krazyhacker/.ssh/id_rsa.work (/Users/krazyworker/.ssh/id_rsa.work)
$ ssh-add -l
4096 SHA256:bOV1ApFMQPQiodlNamHdjhkawdgJe4OpzdweEX3U2ZE /Users/krazyworker/.ssh/id_rsa.work (RSA)

$ ssh-add ~/.ssh/id_rsa.personal
Enter passphrase for /Users/krazyhacker/.ssh/id_rsa.personal: 
Identity added: /Users/krazyhacker/.ssh/id_rsa.personal (/Users/krazyhacker/.ssh/id_rsa.personal)
$ ssh-add -l
4096 SHA256:bOV1ApFMQPQiodlNamHdjhkawdgJe4OpzdweEX3U2ZE /Users/krazyworker/.ssh/id_rsa.work (RSA)
4096 SHA256:6oipZYEbbzHGopH2hkawdgQ3htMlVrM0Pc1+lyYYPvk /Users/krazyhacker/.ssh/id_rsa.personal (RSA)
   {% endhighlight %}

Attempting to push to a git repository results in the following error;

{% highlight bash linenos %}
$ git push
ERROR: Permission to krazyhacks/kh-pages.git denied to krazyhacker.
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.{% endhighlight %}
Since `/Users/krazyworker/.ssh/id_rsa.work` appears first in the list, attempts to push to a personal 
git repo fails. The way i've managed so far is by having only one identity loaded at any one time.
There must be a more elegant solution that allows you to switch between github accounts?
