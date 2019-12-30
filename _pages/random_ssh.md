---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: SSH
layout: single
permalink: /random_ssh/
sidebar:
   nav: "random_nav"
---
{% include toc %}

### SSH Keys and github
Problem, I have two github accounts, a personal and one specific for work. Haven't found a convenient way to
to work with GITHUB on both personal and work stuff on the same computer. Access to the correct git account 
gets confused when both ssh-keys are loaded.

   {% highlight bash linenos %}
$ ssh-add -l
The agent has no identities.
$ ssh-add ~/.ssh/id_rsa.personal
Enter passphrase for /Users/krazyhacker/.ssh/id_rsa.personal: 
Identity added: /Users/krazyhacker/.ssh/id_rsa.personal (/Users/krazyhacker/.ssh/id_rsa.personal)

$ ssh-add -l
4096 SHA256:6oipZYEbbzHGopH2hkawdgQ3htMlVrM0Pc1+lyYYPvk /Users/atulpatel/.ssh/id_rsa.personal (RSA)

$ ssh-add ~/.ssh/id_rsa.work
Enter passphrase for /Users/krazyhacker/.ssh/id_rsa.work: 
Identity added: /Users/krazyhacker/.ssh/id_rsa.work (/Users/krazyhacker/.ssh/id_rsa.work)
i$ ssh-add -l
4096 SHA256:6oipZYEbbzHGopH2hkawdgQ3htMlVrM0Pc1+lyYYPvk /Users/krazyhacker/.ssh/id_rsa.personal (RSA)
4096 SHA256:bOV1ApFMQPQiodlNamHdjhkawdgJe4OpzdweEX3U2ZE /Users/krazyhacker/.ssh/id_rsa.work (RSA)
   {% endhighlight %}
  GIT stuff

