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

### Using Linux SCREEN
#### Script to create a session with multiple named windows
{% highlight bash linenos %} 
#!/bin/bash
SESSION_NAME=${1:-MySession}
WINDOW_PREFIX=${2:-MyWindow}
NUM_OF_WINDOWS=${3:-2}
SCREEN_WINDOW_SCRIPT="./create_screen_windows.sh"
cat << 'EOF' > ${SCREEN_WINDOW_SCRIPT}
#!/bin/bash
# ===================================================================
# Creates a screen session with multiple named windows
# USAGE : $0 <NameOfScreenSession> <WindowPrefixName> <NumOfWindows>
# ===================================================================
SESSION_NAME=${1:-MySession}
WINDOW_PREFIX=${2:-MyWindow}
NUM_OF_WINDOWS=${3:-2}

for n in `seq 1 1 ${NUM_OF_WINDOWS}`; do
        echo ${n}
        NAME="${WINDOW_PREFIX}-${n}"
        screen -p ${n} -t ${NAME}
done
screen -p 0 -t HTOP
EOF

chmod a+x ${SCREEN_WINDOW_SCRIPT}
screen -dmS ${SESSION_NAME} ${SCREEN_WINDOW_SCRIPT}  ${SESSION_NAME} ${WINDOW_PREFIX} ${NUM_OF_WINDOWS}
screen -r ${SESSION_NAME}
{% endhighlight %}

#### Launch commands within screen session+window
When command exits the screen session ends - possibly due to .screenrc file has zombie flag set?
{% highlight bash linenos %}
screen -d -m -S mysession top
screen -S mysession -p 0 -X title win0    # optional!
screen -S mysession -X screen -t win1 vmstat 10
screen -S mysession -X screen -t win2 iostat 30

screen -ls

screen -S mysession -X quit   # nuke whole the session

screen -S mysession -p win2 -X kill
screen -S mysession -p win1 -X kill
screen -S mysession -p 0 -X kill

screen -S mysession -X zombie   <=== See man page for details.
{% endhighlight %}

#### Terminate screen window+session
{% highlight bash linenos %}
ubuntu@ip:~$ screen -ls
There are screens on:
        1140574.my_test (04/03/23 22:34:56)     (Detached)
        3484.Postman    (04/02/23 22:34:24)     (Detached)
2 Sockets in /run/screen/S-ubuntu.
ubuntu@ip-172-31-9-13:~$ screen -x 1140574

TYPE : ctrl + a then ctrl + \

[screen is terminating]

{% endhighlight %}

#### Recording terminal window
Record terminal window sessions using [asciinema](https://asciinema.org/) See [docs](https://docs.asciinema.org/manual/cli/installation/) and [agg](https://docs.asciinema.org/manual/agg/usage/)
{% highlight bash linenos %}
# Installation
$ brew install asciinema

# Record 
$ asciinema rec demo.cast

# Convert to gif
$ agg demo.cast demo.gif
{% endhighlight %}

#### Edit gif to chop, speed up portions using ffmpeg
Install ffmpeg

{% highlight bash  linenos %}
fmpeg -ss 00:00:00 -to 00:01:00 -i Hot_Water_Test.gif Hot_Water_Part1.gif
ffmpeg -ss 00:01:00 -to 00:35:30 -i Hot_Water_Test.gif -vf "setpts=0.1" Hot_Water_Part2.gif
ffmpeg -ss 00:35:30 -to 00:36:46 -i Hot_Water_Test.gif Hot_Water_Part3.gif
ffmpeg -f concat -safe 0 -i list.txt -c copy Hot_Water_Test_Final.gif
ffmpeg -ss 00:01:00 -to 00:35:30 -i Hot_Water_Test.gif -vf "setpts=0.2" Hot_Water_Part2.gif
ffmpeg -f concat -safe 0 -i list.txt -c copy Hot_Water_Test_Final.gif
# Above splits the gif into separate parts based on time

ffmpeg -i Hot_Water_Test.gif -filter_complex "[0:v]fps=15,split=3[v1][v2][v3]; "\

Will need to google this again.


{% endhighlight %}



