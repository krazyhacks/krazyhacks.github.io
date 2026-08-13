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

### Check HDMI Monitors connected
{% highlight bash %}
$ xrandr 
Screen 0: minimum 320 x 200, current 1920 x 1080, maximum 8192 x 8192
HDMI-1 disconnected primary (normal left inverted right x axis y axis)
HDMI-2 connected 1920x1080+0+0 (normal left inverted right x axis y axis) 477mm x 268mm
   1920x1080     60.00*+  50.00    59.94
   1920x1080i    60.00    50.00    59.94
   1680x1050     59.88
   1280x1024     60.02
   1440x900      59.90
   1280x800      59.91
   1152x864      75.00
   1280x720      60.00    50.00    59.94
   1024x768      70.07    60.00
   800x600       60.32    56.25
   720x576       50.00
   720x480       60.00    59.94
   640x480       66.67    60.00    59.94
   720x400       70.08
{% endhighlight %}

#### Configure monitor layout
`xrandr --output HDMI-1 --primary --auto --left-of HDMI-2`

#### Launch script to use two monitors
Either use one script to launch browser on each monitor OR have two systemd scripts, one for each monitor.  The latter will allow rotating through multiple tabs independently.

{% highlight bash %}
#!/bin/bash

sleep 10  # Allow time for X to fully start

chromium-browser --kiosk --user-data-dir=/home/pi/kiosk1 --window-position=0,0 --window-size=1920,1080 https://dashboard1.example.com &
chromium-browser --kiosk --user-data-dir=/home/pi/kiosk2 --window-position=1920,0 --window-size=1920,1080 https://dashboard2.example.com &

{% endhighlight %}

### NGINX Reverse Proxy Setup 

Common reason for using Raspberry Pi in a kiosk mode, is to display status dashboard. For example a grafana dashboard.  Grafana would normally require user to login, but each time the pi reboots, the login credentials would need to be entered.  Any portal that accepts a Bearer Token to gain access, a reverse proxy could be used to inject the Token as a header.

The RPi will launch a web browser and navigate to a local web address, to avoid the error stating the site is insecure/dangerous - create and install ssl ssl_certificate and ssl_certificate_key.  NGINX can the redirect the request to the real site, injecting the bearer token.



#### Setup NGINX
Configure RPi to run a proxy on localhost:3000
Create a nginx proxy configuration file `/etc/nginx/sites-available/grafana`

{% highlight bash %}

server {
    listen 443 ssl;
    server_name kiosk-display-01-02 grafana-kiosk-01-02;

    ssl_certificate /etc/nginx/ssl/kiosk.crt;
    ssl_certificate_key /etc/nginx/ssl/kiosk.key;

    location / {
        proxy_pass https://procodeuk.grafana.net/;
        proxy_set_header Host procodeuk.grafana.net;
        proxy_ssl_server_name on;

        proxy_set_header Authorization "Bearer xxxxxxxxxxxxxxxxxxxxx";
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

{% endhighlight %}

#### Enable NGINX

{% highlight bash %}
sudo ln -s /etc/nginx/sites-available/grafana /etc/nginx/sites-enabled/
{% endhighlight %}

{% highlight bash %}
# Test the nginx config file
sudo nginx -t
# Reload nginx config file.
sudo systemctl reload nginx

# start, stop, status on service
sudo systemctl status nginx
{% endhighlight %}

#### Hosts file on RPI.
{% highlight bash %}
cat /etc/hosts

127.0.0.1	localhost
::1		localhost ip6-localhost ip6-loopback
ff02::1		ip6-allnodes
ff02::2		ip6-allrouters

127.0.1.1	kiosk-display-01-02 grafana-kiosk-01-02
127.0.1.1	dashboard-01-02.local

{% endhighlight %}

### Sample scripts for launching browser
{% highlight bash %}
# /etc/nginx/sites-enabled/grafana-kiosk-01-02
server {
    listen 443 ssl default_server;
    server_name _;

    ssl_certificate /etc/nginx/ssl/kiosk.crt;
    ssl_certificate_key /etc/nginx/ssl/kiosk.key;

    location / {
        proxy_pass https://procodeuk.grafana.net;
        proxy_set_header Host procodeuk.grafana.net;
        proxy_ssl_server_name on;
        proxy_ssl_name procodeuk.grafana.net;

        proxy_set_header Authorization "Bearer xxxxxxxxxxxxxxxxxxxxxxx";
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        proxy_http_version 1.1;
        proxy_set_header Connection "";
        proxy_buffering off;
    }
}
{% endhighlight %}

### Left and Right Screens
{% highlight bash %}
#!/bin/bash
set -e

export DISPLAY=:0
export XAUTHORITY=/home/grafana-display-01-02/.Xauthority

# URL="https://127.0.0.1/playlists/play/bep1oq3spc2dcb?kiosk&autofitpanels"
URL="https://127.0.0.1/playlists/play/bep1oq3spc2dcb?kiosk"

PROFILE_DIR="/home/grafana-display-01-02/.config/chromium-lhs-display"
PROFILE_NAME="Default"

# Disable screen blanking
xset s noblank || true
xset s off || true
xset -dpms || true

# Hide mouse cursor
pkill unclutter 2>/dev/null || true
unclutter -idle 0.5 -root &

# Kill any existing Chromium
# pkill chromium 2>/dev/null || true
sleep 2

exec /usr/bin/chromium \
  --user-data-dir="$PROFILE_DIR" \
  --profile-directory="$PROFILE_NAME" \
  --kiosk \
  --no-first-run \
  --no-default-browser-check \
  --disable-session-crashed-bubble \
  --disable-infobars \
  --ignore-certificate-errors \
  --force-device-scale-factor=1.0 \
  --window-position=3840,0 \
  "$URL"

{% endhighlight %}

{% highlight bash %}
#!/bin/bash
set -e

export DISPLAY=:0
export XAUTHORITY=/home/grafana-display-01-02/.Xauthority

# URL="https://127.0.0.1/playlists/play/cep1fvq8z0y68f?kiosk&autofitpanels"
URL="https://127.0.0.1/playlists/play/cep1fvq8z0y68f?kiosk"

PROFILE_DIR="/home/grafana-display-01-02/.config/chromium-rhs-display"
PROFILE_NAME="Default"

# Disable screen blanking
xset s noblank || true
xset s off || true
xset -dpms || true

# Hide mouse cursor
pkill unclutter 2>/dev/null || true
unclutter -idle 0.5 -root &

# Kill any existing Chromium
# pkill chromium 2>/dev/null || true
sleep 2

exec /usr/bin/chromium \
  --user-data-dir="$PROFILE_DIR" \
  --profile-directory="$PROFILE_NAME" \
  --kiosk \
  --no-first-run \
  --no-default-browser-check \
  --disable-session-crashed-bubble \
  --disable-infobars \
  --ignore-certificate-errors \
  --force-device-scale-factor=1.0 \
  --window-position=0,0 \
  "$URL"

{% endhighlight %}

### Grafana Dashboard kiosk setup
[See here for details](https://github.com/grafana/grafana-kiosk)


