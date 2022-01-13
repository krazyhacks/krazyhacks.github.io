---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Raspberry Pi
layout: single
permalink: /pi/
sidebar:
   nav: "embedded_nav"
---

## Raspberry Pi 
### Raspberry Pi section 
[RaspberryPi OS Imager](https://www.raspberrypi.org/blog/raspberry-pi-imager-imaging-utility/), download and install
[Download OS Image ](https://www.raspberrypi.org/software/operating-systems/#raspberry-pi-os-32-bit).


{% highlight bash linenos %}$ echo "dummy text" {% endhighlight %}
Login to desktop and launch terminal

{% highlight bash linenos %}pi@raspberrypi: $  sudo raspi-config {% endhighlight %}

Enable ssh & vnc


#### Change hostname

{% highlight bash linenos %}pi@raspberrypi: $  sudo raspi-config {% endhighlight %}
Network Options --> Hostname


### Enable camera
[notes from](https://medium.com/swlh/night-vision-camera-for-raspberry-pi-4db1686cb465)

### OLED Display
[OLED 0.91" Waveshare Display Manual](https://www.waveshare.com/w/upload/1/10/0.91inch_OLED_Module_User_Manual_EN.pdf)
https://www.waveshare.com/wiki/Libraries_Installation_for_RPi

### Raspberry pi zero with OBD-II interface
[OBD-II interace](https://www.instructables.com/OBD-Pi/)
