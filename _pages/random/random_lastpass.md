---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Lastpass
layout: single
permalink: /random_lastpass/
sidebar:
   nav: "lastpass_nav"
---

## Lastpass 
See [Lastpass](https://lastpass.com).
### Install lastpass command line tool

{%highlight bash linenos %}$ brew install lastpass-cli {% endhighlight %}

### Lastpass command line help
{% highlight bash linenos %}$ lpass help
Usage:
  lpass {--help|--version}
  lpass login [--trust] [--plaintext-key [--force, -f]] [--color=auto|never|always] USERNAME
  lpass logout [--force, -f] [--color=auto|never|always]
  lpass passwd
  lpass show [--sync=auto|now|no] [--clip, -c] [--quiet, -q] [--expand-multi, -x] [--json, -j] [--all|--username|--password|--url|--notes|--field=FIELD|--id|--name|--attach=ATTACHID] [--basic-regexp, -G|--fixed-strings, -F] [--color=auto|never|always] {UNIQUENAME|UNIQUEID}
  lpass ls [--sync=auto|now|no] [--long, -l] [-m] [-u] [--color=auto|never|always] [GROUP]
  lpass mv [--color=auto|never|always] {UNIQUENAME|UNIQUEID} GROUP
  lpass add [--sync=auto|now|no] [--non-interactive] [--color=auto|never|always] {--username|--password|--url|--notes|--field=FIELD|--note-type=NOTETYPE} NAME
  lpass edit [--sync=auto|now|no] [--non-interactive] [--color=auto|never|always] {--name|--username|--password|--url|--notes|--field=FIELD} {NAME|UNIQUEID}
  lpass generate [--sync=auto|now|no] [--clip, -c] [--username=USERNAME] [--url=URL] [--no-symbols] {NAME|UNIQUEID} LENGTH
  lpass duplicate [--sync=auto|now|no] [--color=auto|never|always] {UNIQUENAME|UNIQUEID}
  lpass rm [--sync=auto|now|no] [--color=auto|never|always] {UNIQUENAME|UNIQUEID}
  lpass status [--quiet, -q] [--color=auto|never|always]
  lpass sync [--background, -b] [--color=auto|never|always]
  lpass export [--sync=auto|now|no] [--color=auto|never|always] [--fields=FIELDLIST]
  lpass import [--keep-dupes] [CSV_FILENAME]
  lpass share subcommand sharename ...
{% endhighlight %}

### Login
{% highlight bash linenos %}
$ lpass login <email_address> 
$ lpass status
{% endhighlight %}


### Search
{% highlight bash linenos %}$ lpass ls | grep -i foobar

Shared-Folder/Name of Pass1 [id: 37999697144993288094929]
Shared-SysAdmin/Name of Pass2 [id: 90790979714687991585720]
Shared-SysAdmin/Name of Pass3  [id: 7909798447999533809997461]
{% endhighlight %}
### Show contents of secret store
{% highlight bash linenos %}
$ lpass show <id>
$ lpass show --password <id> | pbcopy  # MAC-OSx clipboard
$ lpass show --password -cp <id>       # built in option to copy to clipboard 

$ lpass show <url> --all               # url more intuitive than ID
$ lpass show <url> -c --password       #
{% endhighlight %}
Where `id` is numeric value from `lpass ls` command above

### Add/update entries
#### Generate new password
{% highlight bash linenos %}
$ lpass generate work/foobar 20
G>>.':@£$Ffw£$@'

$ lpass ls | grep -i foobar

work/foobar [id: 3562436310901864151]

$ lpass show 3562436310901864151
work/foobar [id: 3562436310901864151]
G>>.':@£$Ffw£$@'

{% endhighlight %}
Generates a new password called `foobar` under a folder `work`

#### Add a secure note
{% highlight bash linenos %}
# Add a new secure note
$ lpass add --note-type=ssn info/my-ssn
# (editor shows a template file; fill in and save)
Name: info/my-ssn
NoteType: Social Security
Name: My Name Goes Here
Number: 123-45-6789
Notes:    # Add notes below this line
Any notes I want to save go here.
~
~

$ lpass show my-ssn
info/my-ssn [id: 0]
Number: 123-45-6789
Name: My Name Goes Here
NoteType: Social Security
Notes:
Any notes I want to save go here.
{% endhighlight %}

#### Download an attachment
{% highlight bash linenos %}
lpass show 5010657096820745240
Shared-Folder/Secret API Key [id: <unique-id>]
URL: http://sn
att-5010657096820745240-1: filename.p8
Notes: This file is a secret required for API access.

$ lpass show 5010657096820745240 --attach=att-<unique-id>
{% endhighlight %}

#### Adding notes
[see](https://devopsheaven.com/ssh/security/lastpass/devops/2018/06/13/ssh-lastpass-cli.html)
