---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Raspberry Pi Kiosk
layout: single
permalink: /random/pi_kiosk/
sidebar:
   nav: "embedded_nav"
---

## Raspberry Pi Kiosk

Setup raspberry pi as a status monitoring display. When the raspberry pi boots up, the device will auto login as a non-root user. Launch the chrome browser in kiosk mode.  If using RPi-5, launch two chrome sessions, one for each HDMI port to a specific web page.  Additionally, each browser could open mutiple tabs and then rotate through the tabs, hence, display different status information. 

Since this is making use of the raspberry pi's desktop and auto login, the Pi could be hijacked and compromised. Thus, remote access needs to be locked down. Launching bash/zsh terminals must present a login prompt. SD Card should be encrypted  

### Raspberry Pi Image
 * Follow instructions to image a blank SD card
 * Boot Raspberry Pi with keyboard & Mouse
 * Configure locale, hostname, username + password, check for updates, reboot
 * Enable auto login
 * 
 * Disable USB - prevent attack from rogue usb device

### Lock down Pi

 * Enable SSH
 * Install public key for remote access
 * Update local `.ssh/config` for smoother ssh login

### Auto-boot

### Extra Packages
#### Remove unnecessary pkgs
{% highlight bash %}
sudo apt purge wolfram-engine scratch scratch2 nuscratch sonic-pi idle3 -y
sudo apt purge smartsim java-common minecraft-pi libreoffice* -y
{% endhighlight %}
#### Remove Clean up local apt repository
{% highlight bash %}
sudo apt clean
sudo apt autoremove -y
sudo apt update
sudo apt upgrade
{% endhighlight %}
#sudo apt install xdotool unclutter### Install extra pkgs
 * xdotool - send key strokes via script
 * unclutter - hide mouse cursor
 {% highlight bash %}
 sudo apt install xdotool unclutter
 {% endhighlight %}

### Browser setup
Launch Chrome from command line / script
{% highlight bash %}
chromium --new-tab https://google.com --new-tab https://yahoo.com
{% endhighlight %}
### Launch scripts
 * bash script 
{% highlight bash %}
#!/bin/bash

xset s noblank  # Don't blank out screensaver
xset s off      # Disable screensaver
xset -dpms      # Disable PMU

unclutter -idle 0.5 -root &

rm -r ~/.cache/chromium/Default/Cache/Cache_Data/*

# Prevents chrome browser from showing warning banners in kiosk mode
# Removes the need to use keyboard/mouse to clear banners
sed -i 's/"exited_cleanly":false/"exited_cleanly":true/' /home/$USER/.config/chromium/Default/Preferences
sed -i 's/"exit_type":"Crashed"/"exit_type":"Normal"/' /home/$USER/.config/chromium/Default/Preferences

/usr/bin/chromium-browser --incognito --noerrdialogs --disable-infobars --kiosk https://grafana.com https://www.adafruit.com &

while true; do
      xdotool keydown ctrl+Next; xdotool keyup ctrl+Next;
      sleep 15
      xdotool keydown ctrl+r; xdotool keyup ctrl+r;
      sleep 15
done

{% endhighlight %}


### Register as Service 
Edit `/lib/systemd/system/kiosk.service`
{% highlight bash %}
[Unit]
Description=Chromium Kiosk
Wants=graphical.target
After=graphical.target

[Service]
Environment=DISPLAY=:0.0
Environment=XAUTHORITY=/home/pi/.Xauthority
Type=simple
ExecStart=/bin/bash /home/pi/kiosk.sh
Restart=on-abort
User=pi
Group=pi

[Install]
WantedBy=graphical.target
{% endhighlight %}

* `sudo systemctl enable kiosk.service`
* `sudo systemctl start kiosk.service`
* `sudo systemctl status kiosk.service`
* `sudo systemctl stop kiosk.service`
* `sudo systemctl disable kiosk.service`


### Drive browser from ssh using xdotool
May need to login to web portal via ssh without keyboard and mouse.  A crude way as an example;

{% highlight bash %}
#!/bin/bash


read -p "Enter email : " email_address
stty -echo
read -p "Enter passwd: " password
stty echo
echo

export DISPLAY=:0
for i in "key Tab" "key Return" "type ${email_address}" "key Tab" "key Tab" "key Return" "type ${password}" "key Tab " "key Tab " "key Return "
do
        read -p "CMD ${i}" foo
        xdotool ${i}
done

{% endhighlight %}
