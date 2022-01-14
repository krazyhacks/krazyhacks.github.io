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
and the repository exists.
{% endhighlight %}
Since `/Users/krazyworker/.ssh/id_rsa.work` appears first in the list, attempts to push to a personal 
git repo fails. The way i've managed so far is by having only one identity loaded at any one time.
There must be a more elegant solution that allows you to switch between github accounts?

<hr>
TODO
{% highlight bash linenos %}
https://psychowhiz.medium.com/configuring-multiple-ssh-keys-for-git-on-the-same-device-41c29320e5fe
{% endhighlight %}

### SSL 
[See](https://www.sslshopper.com/article-most-common-openssl-commands.html)

### Inspect files.
#### Inspect .p12 file
{% highlight bash %}
openssl pkcs12 -nokeys -info -in ../DistCertificates.p12
{% endhighlight %}
#### Inspect contents of pem file
{% highlight bash %}
openssl x509 -noout -text -in Certificate.pem
{% endhighlight %}

#### Inspect contents of CSR
{% highlight bash %}
openssl req -in mycsr.csr -noout -text
{% endhighlight %}

#### Inspect contents of .cer file
{% highlight bash %}
openssl x509 -in cerfile.cer -noout -text
openssl x509 -inform pem -in cerfile.cer -noout -text
openssl x509 -inform der -in cerfile.cer -noout -text  # format produced by Apple developer console
{% endhighlight %}
