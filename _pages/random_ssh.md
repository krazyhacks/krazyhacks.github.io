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

### Updating SSL Cert in IONOS -> AWS
Domains in IONOS, with SSL certificates, renew within IONOS.

Ionos offers to download;
SSL Certificate - This is pem format
Create and Download .PFX File - Used for Windows Servers
Intermediate Certificate 
Site Seal

AWS Certificate Manager - import cert
Certificate Body requires PEM-encoded certificate body
Certificate Private Key requires PEM-encoded private key

#### Import into AWS
* Navigate to Certificate Manager (select region)
* Under the list of certificates, the Type is likely to be "imported"
* Select Import
** Paste SSL Certificate downloaded from IONOS into pane named "Certificate body"
** Paste Private key into pane named "Certificate private key"
** Certificate chain optional - leave blank.

Note: When a certificate is up for renewal, it can be renewed early before the old one expires.
The renwal certificate will commence early but expire on the same the old one expires but a year ahead.
Hence its valid for a little over a year/overlaps.


### SSH Access to remote hosts
To help login to remote computers using ssh keys.  Some low risk computers can be setup to access without the need
for entering a password. This is primarily to make it efficient but compromises security to some degree.

Accessing RaspberryPi from a MacBook.

* Create dedicated ssh private/public key pairs
* Add private key to macbook keychain
* Enable `ssh` on raspberryPi
* Install public key on raspberryPi
* Update sshd config on raspberryPi
* Add shortcut on MacBook

#### Generate SSH Keys
Create `.ssh` folder if it doesn't already exist. Create keys using `ssh-keygen`, -C option is a comment to easily identify the keys.
When prompted, provide the path/filename to create for ssh keys (rather than accepting the default, which may overwrite an existing pair by accidenty).
Type `ENTER` to set no password
{% highlight bash %}
$ mkdir ~/.ssh
$ ssh-keygen -t rsa -b 4096 -C "me@raspberry.pi.local"

Generating a public/private rsa key pair.
Enter the file in which you wish to save they key (i.e., /home/username/.ssh/id_rsa). /home/username/.ssh/raspberrypi_id_rsa
Enter a passphrase (leave empty for no passphrase).
Enter same passphrase again:
Your identification has been saved in /home/username/.ssh/raspberrypi_id_rsa
Your public key has been saved in /home/username/.ssh/raspberrypi_id_rsa.pub

The key fingerprint is:
ar:bc:d3:9e:g3:1f:63:6f:6b:32:2e:97:ee:42:e1:be pi@raspberry.pi.local

The key’s randomart image is:

+--[ RSA 2048]----+
| ..+**B.o++o     |
|  . o+==o. o     |
|    . .oo.=      |
|      . +E+ .    |
|        S .      |
|                 |
|                 |
|                 |
|                 |
+-----------------+
{% endhighlight %}

#### Add private key to KeyChain
{% highlight bash %}
$ eval `ssh-agent`                    # start daemon if required.
$ ssh-add -l                          # list current set of keys already registered
$ ssh-add ~/.ssh/raspberrypi_id_rsa   # Add newly created private key to KeyChain
$ ssh-add -l
2048 aa:42:d4:46:81:43:65:7f:4e:53:94:5f:2f:0d:fd:bd pi@raspberry.pi.local (RSA)
{% endhighlight %}
#### Enable ssh on raspberrypi
* Connect keyboard, mouse and screen to RaspberryPi.  
* Boot up and login.
* Launch terminal, run `raspi-config`, navigate menus and enable sshd

#### Install public key on raspberrypi
Copy the public key to raspberryPi, using one of two methods;
{% highlight bash %}
$ scp ~/.ssh/raspberrypi_id_rsa.pub pi@raspberrypi.local:/home/pi/.ssh/authorized_keys
$ ssh pi@raspberrypi.local
$ chmod 700 ~/.ssh
$ chmod 600 ~/.ssh/authorized_keys

{% endhighlight %}

OR, this command takes care of creating `.ssh` on the raspberrypi and setting the correct permissions on the file.
{% highlight bash %}
$ ssh-copy-id -i ~/.ssh/raspberrypi_id_rsa.pub pi@raspberrypi
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed == if you are prompted now it is to install the new keys
pi@raspberry.pi.local's password:
Number of key(s) added: 1
{% endhighlight %}

#### Update sshd config on raspberryPi
{% highlight bash %}
$ ssh pi@raspberrypi.local
$ vi /etc/ssh/sshd_config
{% endhighlight %}
In `/etc/ssh/sshd_config` uncomment the following line(s)

{% highlight %}
PubkeyAuthentication yes
AuthorizedKeysFile      .ssh/authorized_keys .ssh/authorized_keys2
{% endhighlight %}

Restart `sshd` or reboot raspberrypi
{% highlight bash %}
sudo /etc/init.d/ssh restart
sudo reboot
{% endhighlight %}

#### Add shortcut on macbook 
Update Macbook's `~/.ssh/config` with a shortcut by adding the following lines

{% highlight bash %}
Host pi
  HostName raspberrypi.local
  User pi
  IdentityFile ~/.ssh/raspberrypi_id_rsa
  Port 22
{% endhighlight %}
