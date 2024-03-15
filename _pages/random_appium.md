---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Appium
layout: single
permalink: /random_appium/
sidebar:
   nav: "random_nav"
---

## Appium 
### Appium section 
See [pip Installation](https://pip.pypa.io/en/stable/installing/).

{% highlight bash linenos %}$ echo "dummy text" {% endhighlight %}

* Install nvm, use nvm to manage node versions
* Install node;  nvm list, nvm install
* {% highlight bash linenos %}

nvm list
nvm install 18.18.2
which node
node -v
npm i --location=global appium
appium -v
appium driver list
appium driver install chromium
{% endhighlight %}

Sometimes the loaded chromium web driver is not compatible with an older browser installed on the device
either install the correct version - usually reported in the logs or run appium to download it.
{% highlight bash linenos %}
appium ----allow-insecure chromedriver_autodownload
{% endhighlight %}

#### Appium Capabilities
[Capabilities docs](https://appium.io/docs/en/2.1/guides/caps/)
{% highlight json linenos %} 
{
  "appium:automationName": "uiautomator2",
  "appium:platformName": "Android",
  "appium:appPackage": "com.trustpower.loop",
  "appium:noReset": true
}
{% endhighlight %}


## Appium Inspector
[Download](https://github.com/appium/appium-inspector/releases)
Launching appium on MAC may throw an error that its not a trusted app. Locate the App in file browser/finder under /Applications, Use CTRL+Click to
open the App.  To change which apps can open on your Mac, choose Apple menu  > System Settings, click Privacy & Security  in the sidebar, 
then go to Security on the right. Youu can also use the command line;

{% highlight bash linenos %} 
sudo xattr -r -d com.apple.quarantine file_path
{% endhighlight %}


Note you can not have Chrome and Appium inspector debugging together (only one USB cable - obvious)
In Chrome uncheck the tick boxes [under](chrome://inspect/devices) to release device. Also `adb devices` will report nothing.

## Debug / Screen mirror using Chrome
[Use Chrome to debug](https://developer.chrome.com/docs/devtools/remote-debugging/)

