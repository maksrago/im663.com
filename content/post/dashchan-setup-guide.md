+++
title = "How to add app support for LynxChan imageboards (through DashChan)"
date = 2019-12-02T00:00:00-05:00
tags = ["lynxchan", "ubuntu"]
categories = ["guide"]
draft = false
+++

Since launching [16chan](https://www.16chan.xyz) I've had several users and imageboard administrators reach out to me on
how to add mobile app support to their [LynxChan](https://gitgud.io/LynxChan/LynxChan) based imageboard.
For those unfamiliar, [DashChan](https://github.com/Mishiranu/Dashchan) is a modular
imageboard browsing app. Support for imageboards is enabled by
downloading the appropriate extension, thus in order to get support
for your imageboard, you will have to create a APK that can be added
to the DashChan app. I created a generic extension, that only requires
a few basic changes in order to add support for your LynxChan based
site. This guide is specifically built around Ubuntu, specifically
versions that have `snapd` installed. We will also be using Android Studio.

The repository containing the generic DashChan extension can be
found [here](https://github.com/maksrago/Dashchan-Extension-LynxChan-Generic).

****Things that do not work with this extension:****

-   Captcha
-   Reports


## Installing, configuring, and importing our project to Android Studio {#installing-configuring-and-importing-our-project-to-android-studio}

In order to access and modify our project, we are going to have to
first install Android studio.

Install Android studio with this simple command:

```bash
snap install android-studio --classic
```

Afterwards, we can clone the generic LynxChan DashChan extension with:

```bash
git clone https://github.com/maksrago/Dashchan-Extension-LynxChan-Generic
```

When opening Android Studio you will be given several prompts, and
after successful installation and configuration you will be able to
open projects.

{{< figure src="https://i.imgur.com/ml5C3sd.png" >}}

We are going to `Import project (Gradle, Eclipse ADT, etc)`. From
there you can specify the directory to where you cloned the extension.

{{< figure src="https://i.imgur.com/x3jB1W2.png" >}}

Afterwards, you will likely see this prompt:

{{< figure src="https://i.imgur.com/59Awnn0.png" >}}

To which you should answer `OK`. Once the gradle sync is successfully
completed you should see the following:

{{< figure src="https://i.imgur.com/pzZPDi6.png" >}}


## Setting the project default behavior {#setting-the-project-default-behavior}

This project makes it extremely easy to add support for your
imageboard, we are just going to make a few changes, however, before
we do anything we need to setup our projects default
behavior. Navigate to the top bar and click: `Edit Configurations..`
from the dropdown.

{{< figure src="https://i.imgur.com/k0q4Zi5.png" >}}

From there, we can set the default behavior to `Nothing`:

{{< figure src="https://i.imgur.com/jiWcSQ5.png" >}}

Put simply, this APK acts as a set of instructions for the DashChan
app, and how it should interpret and pull data from the specified
source we direct it to.


## Setting up Android Device (physical device or emulator) {#setting-up-android-device--physical-device-or-emulator}

Select one of these options, I would highly recommend using a physical
Android device you have one available.


### If you have a physical Android device {#if-you-have-a-physical-android-device}

You will need to follow these steps:


#### 1. Put your device into developer mode, and enable USB debugging {#1-dot-put-your-device-into-developer-mode-and-enable-usb-debugging}

The process that you need to follow in order to put your Android
device into developer mode will vary, thus I would recommend searching
up how to do so in your search engine of choice.

After you successfully put your device into USB debugging mode, and
connect it to your computer you may see the following prompt on your
device:

{{< figure src="https://i.imgur.com/3S3rMuT.png" >}}

I recommend checking off `Always allow from this computer`.


#### 2. Install the DashChan application and supporting libraries {#2-dot-install-the-dashchan-application-and-supporting-libraries}

-   Download the DashChan APK, found [here](https://github.com/Mishiranu/Dashchan-Extensions/raw/master/update/package/Dashchan.apk).
-   Download the DashChan WebM libraries [here](https://github.com/Mishiranu/Dashchan-Extensions/raw/master/update/package/DashchanWebm.apk).

The link to the DashChan repository, can be found [here](https://github.com/Mishiranu/Dashchan).


### ****(If you don't have a physical device)**** Setting up an Android Emulator through Android Studio {#if-you-don-t-have-a-physical-device--setting-up-an-android-emulator-through-android-studio}

To my knowledge, this option is only available to users running on
Intel based systems.

****I do not recommend this option, due to the fact that it requires
quite a powerful system to emulate Android smoothly.****

To setup an Android device emulator, go to the top bar, and under `No
Device`, and under the dropdown you'll the the option of `Open AVD Manager`.

{{< figure src="https://i.imgur.com/23PiAv6.png" >}}

From there you should `Create Virtual Device...`
![](https://i.imgur.com/ZHtj3YO.png)

From there you can select a device that you with to emulate, in my
case I stuck with the default:

{{< figure src="https://i.imgur.com/wFMwyLM.png" >}}

You can also customize the Android version on the emulated device, I
used Q:

{{< figure src="https://i.imgur.com/w32iQgP.png" >}}

Afterwards, Android Studio will download and pull your version of
Android that you specified and install it on your emulator:

{{< figure src="https://i.imgur.com/xYEGD9r.png" >}}

Finally, you can specify any additional settings for your emulator, we
should be fine with the default settings, so just hit `Finish`.

{{< figure src="https://i.imgur.com/1ttBBk3.png" >}}

After your emulator is successfully configured, make sure that:


#### 1. Put your device into developer mode if it isn't already, and enable USB debugging {#1-dot-put-your-device-into-developer-mode-if-it-isn-t-already-and-enable-usb-debugging}

The process that you need to follow in order to put your Android
device into developer mode will vary, thus I would recommend searching
up how to do so in your search engine of choice.


#### 2. Install the DashChan application and supporting libraries {#2-dot-install-the-dashchan-application-and-supporting-libraries}

-   Download the DashChan APK, found [here](https://github.com/Mishiranu/Dashchan-Extensions/raw/master/update/package/Dashchan.apk).
-   Download the DashChan WebM libraries [here](https://github.com/Mishiranu/Dashchan-Extensions/raw/master/update/package/DashchanWebm.apk).

The link to the DashChan repository, can be found [here](https://github.com/Mishiranu/Dashchan).


### After successfully setting up your device {#after-successfully-setting-up-your-device}

You should see one of the following options in your Android Studio now
depending on whether you chose to use a physical device or a emulated
device:

{{< figure src="https://i.imgur.com/kEB5GKZ.png" >}}

You should see the name of your physical device, or the emulated
device name along with the API version your selected in the previous
optional step.


## Setting up our boards list {#setting-up-our-boards-list}

If order for the DashChan extension to work properly, we are going to
need to create a new html file in the `/static/` directory of our
LynxChan frontend, the exact location would be
`~/LynxChan/src/fe/static/`. This file should be named `boardsList.html`, in this
file you will add the respective boards for your site, an example
`boardsList.html` might look like:

```html
<h2>NSFW</h2>
<ul class="boardlist">
  <li><a href="/b/">Random</a></li>
  <li><a href="/library/">Library</a></li>
  <li><a href="/pol/">Politically Incorrect</a></li>
  <li><a href="/r9k/">ROBOT9001</a></li>
</ul>

<h2>SFW</h2>
<ul class="boardlist">
  <li><a href="/culture/">Culture</a></li>
  <li><a href="/g/">Technology</a></li>
  <li><a href="/k/">Weapons</a></li>
  <li><a href="/meta/">16chan Discussion</a></li>
  <li><a href="/v/">Video Games</a></li>
</ul>
```

As you may have already noticed, you can use `<h2>` tags in order to
specify a category or a heading for categorizing your boards.


## Making modifications to the project (DashChan module) {#making-modifications-to-the-project--dashchan-module}

Now, we can add personalized changes to the project and point it to
our LynxChan site!

From there we are going to need to modify the following files:
-`AndroidManifest.xml`
-`LynxchanChanLocator.java`
-`LynxchanChanConfiguration.java`


### Modifying: `AndroidManifest.xml` {#modifying-androidmanifest-dot-xml}

****Located in:**** `~/Dashchan-Extension-LynxChan-Generic/`

In the `AndroidManifest.xml` file we are going to modify lines 29 and
30 to correspond to our imageboard's domain, in the case of 16chan it
would look like:

_line 29_

```java
<data android:host="16chan.xyz" />
```

and

_line 30_

```java
<data android:host="www.16chan.xyz" />
```


### Modifying: `LynxchanChanLocator.java` {#modifying-lynxchanchanlocator-dot-java}

****Located in:**** `~/Dashchan-Extension-LynxChan-Generic/src/com/sixsixthree/dashchan/chan/lynxchan/`

We just need to modify this file with our site domain information once
again, on lines 18 and 19, so in the case of 16chan it would look
like:

_line 18_

```java
addChanHost("16chan.xyz");
```

and

_line 19_

```java
addConvertableChanHost("www.16chan.xyz");
```


### Modifying: `LynxchanChanconfiguration.java` {#modifying-lynxchanchanconfiguration-dot-java}

****Located in:****
`~/Dashchan-Extension-LynxChan-Generic/src/com/sixsixthree/dashchan/chan/lynxchan/`

Here you might want to make several modifications depending on the
your site, and how you configured it.

On line 34 you can specify the amount of attachments that you want to
allow users to post, this should correspond to your site's
`Maximum number of uploaded files on posting` a setting that is found
under your site's Global Settings.

Furthermore, you can add and remove supported mime types just below
that setting, the default settings should work just fine if you don't
have any additional customization with mime types.


## Testing our APK {#testing-our-apk}

We can begin by opening the DashChan application on our device, you
should see something similar to this:

{{< figure src="https://i.imgur.com/jcle1G3.png" >}}

To test the module we just customized we can click the little green
arrow on the top bar:

{{< figure src="https://i.imgur.com/zm6BaBg.png" >}}

Afterwards you can restart the DashChan application, if you
successfully configured everything you should see the boards that you
specified in your `boardsList.html` file. In my case:

{{< figure src="https://i.imgur.com/bO40UeF.png" >}}


## Creating a shareable APK {#creating-a-shareable-apk}

Upon successfully testing and making various changes to the project,
you can generate a compiled APK file by selecting `Build` > `Build
Bundle(s) / APK(s)` > `Build APK(s)`, you should then see something
like this:

{{< figure src="https://i.imgur.com/3iidjlJ.png" >}}

This APK will be located in
`~/Dashchan-Extension-LynxChan-Generic/build/outputs/apk/debug/`, you
can then upload it to either some version control site (Github/Gitgud)
or host it on your site. Either way I recommend making a page similar to
<https://16chan.xyz/.static/pages/mobile.html>, to make it easy for your
users to understand how to setup up the application.