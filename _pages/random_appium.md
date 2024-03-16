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
[Appium Webpage](https://appium.io/docs/en/latest)
[Appium Library](https://appium.readthedocs.io/en/latest/en/about-appium/api) NOTE: Best to navigate using the `EN` drop down menu to jump to a section then use the `previous` and `next` arrows to move within that section. The page with the full menu of direct links are broken :-( 
[Appium API Documentation](https://appium.readthedocs.io/en/latest/en/commands/elements/attributes/README)

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

## Appium Doctor
Run `Appium Doctor` to check health of install, and, use the report to ensure the correct packages/libraries are installed.
{% highlight bash linenos %}
$ appium-doctor
{% endhighlight %}


#### Appium Capabilities
[Capabilities docs](https://appium.io/docs/en/2.1/guides/caps/)
{% highlight json linenos %} 
{
  "platformName": "Android",
  "appium:automationName": "uiautomator2",
  "appium:deviceName": "cexx17xxxx5330xx03",
  "appium:appPackage": "com.trustpower.loop"
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


## Launch and run Appium Inspector

* Ensure environment variables are set for current shell, add it to `.bashrc` or `.zshrc`
{% highlight bash linenos %}
export JAVA_HOME=/Library/Java/JavaVirtualMachines/zulu-11.jdk/Contents/Home/
export PATH=${JAVA_HOME}/bin:${PATH}

export ANDROID_HOME=${HOME}/Library/Android/sdk
{% endhighlight %}

Ensure you have `adb` in your path. Connect android phone over USB to laptop. Check device is detected
{% highlight bash linenos %}
$ adb devices
List of devices attached
cexx17xxxx5330xx03      unauthorised      <=== authorise from handset under developer settings

$ adb devices
cexx17xxxx5330xx03      device
{% endhighlight %}

From this shell start `appium` server (pass argument to allow auto download of chromuim as above)
{% highlight bash linenos %}
$ appium 
{% endhighlight %}
Appium server will start up on default localhost:4723

Launch `Appuim Inspector` using spotlight search `CND-SPACE`
Within `Appium Inspector` create/add desired Capabilities (see above simple example) or with [more options](https://appium.io/docs/en/2.0/guides/caps/)
Use `SaveAs` to store setup for easy recall/templating for different configurations. Settings are stored under `~/Library/Application Support/appium-inspector/settings.json`.

Click `Start Session`,  `Appium Inspector` should connect to the `appuim` server started in the terminal shell, in turn connect to the mobile device and launch the App specified in desired Capabilities. `Appium Inspector` should mirror the screen for inspection.

