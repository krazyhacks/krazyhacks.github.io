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
### Appium setup/installation 

{% highlight bash linenos %}$ 

* Install nvm, use nvm to manage node versions
* Install specific version of node for appium, list available versions, make required version active for the current shell

nvm list
nvm install 18.18.2
which node
node -v
nvm use v18.18.2
npm install -g npm@10.5.0  <=== sometimes throws errors trying to install pkgs
npm config get prefix
npm list -g
npm i --location=global appium
appium -v
npm install -g appium-doctor
appium driver list
appium driver install chromium
{% endhighlight %}


Drivers are installed under ~/.appium/node_modules/
if not available via npm download [from here](https://googlechromelabs.github.io/chrome-for-testing/#stable)try using a mirror site by adding the following line to `~/.npmrc` 

{% highlight bash linenos %}
chromedriver_cdnurl=https://npm.taobao.org/mirrors/chromedriver
{% endhighlight %}

### Appium Drivers
Install [Appium drivers](https://appium.io/docs/en/2.0/quickstart/uiauto2-driver/) 
{% highlight bash %}
appium driver install uiautomator2
appium driver install xcuitest
appium driver install mac2
appium driver install safari
appium driver install gecko
APPIUM_SKIP_CHROMEDRIVER_INSTALL=1 appium driver install chromium
APPIUM_SKIP_CHROMEDRIVER_INSTALL=1 appium driver install espresso

npm show uiautomator2 version
{% endhighlight %}

Sometimes the loaded chromium web driver is not compatible with an older browser installed on the device
either install the correct version - usually reported in the logs or run appium to download it.


{% highlight bash linenos %}
appium --allow-insecure chromedriver_autodownload
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
sudo xattr -r -d com.apple.quarantine /Applications/Appium Inspector.app
{% endhighlight %}


Note you can not have Chrome and Appium inspector debugging together (only one USB cable - obvious)
In Chrome uncheck the tick boxes [under](chrome://inspect/devices) to release device. Also `adb devices` will report nothing.

## Debug / Screen mirror using Chrome
[Use Chrome to debug](https://developer.chrome.com/docs/devtools/remote-debugging/)

