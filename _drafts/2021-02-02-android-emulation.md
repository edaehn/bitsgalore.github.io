---
layout: post
title: Android emulation experiments
tags: [Android, emulation, virtualization]
comment_id: 74
---

<figure class="image">
  <img src="{{ BASE_PATH }}/images/2021/02/wwwspace.png" alt="Satellite image of Wadden Sea">
  <figcaption><a href="https://commons.wikimedia.org/wiki/File:World_Wide_Web_-_Digital_Preservation.png">“World Wide Web - Digital Preservation”</a> by <a href="https://www.wikidata.org/wiki/Q55754361">Jørgen Stamp</a>, used under <a href="https://creativecommons.org/licenses/by/2.5/dk/deed.en">CC BY 2.5 DK</a>; <a href="https://commons.wikimedia.org/wiki/File:Wadden_Sea.jpg">“Wadden Sea”</a> by Envisat satellite, used under <a href="https://creativecommons.org/licenses/by-sa/3.0-igo">CC BY-SA 3.0-IGO</a>.</figcaption>
</figure>

So far the KB hasn't actively pursued the preservation of mobile apps. However, born-digital publications in app-only form have become increasingly common, as well as "hybrid" publications, with apps that are supplemental to a paper book. We cannot ignore mobile apps any longer, and at the request of our Digital Preservation department I've started looking into how to preserve them. The [2019 iPres paper on the Acquisition and Preservation of Mobile eBook Apps by the British Library's Maureen Pennock, Peter May and Michael Day](https://zenodo.org/record/3460450) provided an excellent starting point for this, and it highlights many of the challenges involved.

However, before we can start archiving mobile apps ourselves, some additional aspects need to be addressed in more detail. One of these is the question of how to ensure long-term access. [Emulation](https://en.wikipedia.org/wiki/Emulator) is the obvious strategy here, but I couldn't find much information on the emulation of mobile platforms in a digital preservation context.So, I ran some simple experiments, where I tried to emulate two selected apps. The main objective here was to explore the current state of emulation of mobile devices, and to get an initial impression of the suitability of some popular emulation solutions for long-term access.

For practical reasons I've limited myself to the Android platform[^1]. Attentive readers may recall I [briefly touched on this subject back in 2014]({{ BASE_PATH }}/2014/10/23/running-archived-android-apps-pc-first-impressions). As much of the information in that blog post has now become outdated, this new post  presents a more up-to date investigation.

<!-- more -->

## Android emulation options

The Emulation General Wiki contains [a good overview of Android "emulators"](https://emulation.gametechwiki.com/index.php/Android_emulators)[^3]. Most of these are closed-source, and the Wiki warns that some may come with malicious apps pre-installed. For the purposes of this study these are of no interest. The listed open-source solutions are:

- [Android-x86](https://www.android-x86.org/) is a port of the [Android open source project](https://source.android.com/) to the [x86](https://en.wikipedia.org/wiki/X86) architecture. It is not an emulator, but rather an operating system that can be installed on either a physical device, or within a virtual machine (e.g. using VirtualBox or QEMU).
- [Android Studio](https://developer.android.com/studio/) is Google's offial development environment for Android. It includes an [Android Emulator](https://developer.android.com/studio/run/emulator), which "simulates Android devices on your computer so that you can test your application on a variety of devices and Android API levels without needing to have each physical device".
- [Anbox](https://github.com/anbox/anbox) is "a container-based approach to boot a full Android system on a regular GNU/Linux system like Ubuntu". It is not an emulator, but rather a [compatibility layer](https://en.wikipedia.org/wiki/Compatibility_layer)[^4].
- [Shashlik](http://www.shashlik.io/) is another project for running Android apps on Linux. Going by [its description](http://www.shashlik.io/what-is/), it uses a stripped-down Android base that is run within a modified version of QEMU, combined with graphics rendering on the host machine.

## Experimental setup

The experiments described in this post focus on the emulation of two selected test apps:

1. The Dutch-language children's book "De avonturen van Max - Op zoek naar F…" comes with the [ARize "augmented reality" app](https://arize.io/). By pointing the mobile device's camera to selected pages in the book, the app adds 3-D visualizations and animations[^5]. 
2. [Immer](https://immer.app/), which is a book reading app that claims to "help\[ing\] you read more often, easily and enjoyably on your phone or tablet".

I tried to emulate these apps using the following environments:

1. Android-x86, running in VirtualBox
2. Android-86, running in QEMU
3. Anbox
4. Android Studio's emulator

I did not include Shashlik, as this project has been [inactive since 2016](https://github.com/shashlik). I evaluated each of these environments by the following (admittedly very basic) criteria:

- Does the environment work at all?
- Is it possible to install the ARize and Immer apps?
- Do the installed apps work?

I ran all tests on a regular desktop PC with the following characteristics:

- Quad-core [Intel Core i5-6500 processor](https://ark.intel.com/content/www/us/en/ark/compare.html?productIds=88184)
- 12 GB RAM
- Operating system: Linux Mint 19.3 (Tricia)

Below follows a more detailed discussion of each of the emulated environments.

## Android-x86 + VirtualBox

### Setup

[This blog post by Sjoerd Langkemper](https://www.sjoerdlangkemper.nl/2020/05/06/testing-android-apps-on-a-virtual-machine/) gives a good overview of how to set up a virtual machine running Android-x86 with VirtualBox, and I largely followed the instructions mentioned here. I used VirtualBox verion 6.0.24, r139119. I had some difficulty getting a functional virtual machine from the latest Android-86 ISO installer image, so in the end I settled for the pre-built VirtualBox image from [osboxes.org](https://www.osboxes.org/android-x86/) (Android-x86 9.0-R2, 64-bit). Initially the virtual machine got stuck on a black screen at startup. I was able to fix this by setting the graphics controller (which can be found under "Display" in the VM settings) to "VBoxSVGA". I also set the number of processors ("System' settings, "Processor" tab) to 2, as according to the [Android-86 documentation](https://www.android-x86.org/documentation/virtualbox.html):

> Processor(s) should be set above 1 if you have more than one virtual processor in your host system. Failure to do so means every single app (like Google Chrome) might crush if you try to use it.

Here's what the virtual machine looks like after it has booted: 

<figure class="image">
  <img src="{{ BASE_PATH }}/images/2021/02/vbox_android_startup.png" alt="Home app, Android-x86 on VirtualBox.">
  <figcaption>Home app, Android-x86 on VirtualBox.</figcaption>
</figure>

At a first glance, everything pretty much works, although I did run into a number of crashes of the Chrome browser app. These all occurred while entering text into the address bar. Strangely, I couldn't replicate these crashes in a later session, so I'm not sure about the exact cause. 

### App installation

To install apps on Android, you'd normally use the [Google Play Store](https://play.google.com/store/apps). Within a typical preservation workflow, you're more likely to have a local copy of the app's [Android Package (APK)](https://en.wikipedia.org/wiki/Android_application_package). Because of this, I tried to "emulate" this by using locally downloaded APKs for my experiments. To achieve this, I first used the [gplaycli](https://github.com/matlink/gplaycli) tool to download APK files of the test apps to my local (Linux) machine. Installing these APKs on the emulated machine can be a bit tricky, as VirualBox (and most other Android emulators) provides no easy way to set up shared folders between the host machine and the emulated device. The easiest method (which works for *all* of the environments covered by this post) uses the [Android Debug Bridge (adb)](https://developer.android.com/studio/command-line/adb), which is part of [Android Studio](https://developer.android.com/studio/). [Langkemper's blog post](https://www.sjoerdlangkemper.nl/2020/05/06/testing-android-apps-on-a-virtual-machine/) covers the use of the *adb* tool in detail, so for brevity I'll only show the basic steps here.

First, we need to find the IP address of our virtual Android machine, which in my particular case turned out to be "127.0.0.1" (localhost)[^6]. Then we can use the Android Debug Bridge tool to connect to the virtual machine:

```bash
adb connect 127.0.0.1
```
If all goes well, you should see this:

```
connected to 127.0.0.1:5555
```

If you get a "Connection refused" error instead, you might need to set a port forwarding rule for the virtual machine. In VirtualBox, go to "Network" in your VM's settings. Click on "Advanced", followed by "Port Forwarding". Here, click on the green "+" icon (top-right). This adds a port forwarding rule. Now change the values of both "Host Port" and "Guest Port" to 5555 (defaults for both are 0):

<figure class="image">
  <img src="{{ BASE_PATH }}/images/2021/02/vbox_ptforward.png" alt="Setting port forwarding rules in VirtualBox.">
  <figcaption>Setting port forwarding rules in VirtualBox.</figcaption>
</figure>

Then try to connect again.  Once the connection is established, you can install the local APK file with adb using its "install" subcommand, with the name of the package file as an argument:

```bash
adb install com.Triplee.TripleeSocial.apk
```

### Results for test apps

I was able to install the test apps without any problems, and their launcher icons show up promptly after the installation:

<figure class="image">
  <img src="{{ BASE_PATH }}/images/2021/02/vbox_android_apps.png" alt="App launchers after installation (note ARize and Immer icons).">
  <figcaption>App launchers after installation (note ARize and Immer icons).</figcaption>
</figure>

However, the ARize app crashes immediately after it is launched:

<figure class="image">
  <img src="{{ BASE_PATH }}/images/2021/02/vbox_android_arize.png" alt="ARize crash message after repeated launch attempts.">
  <figcaption>ARize crash message after repeated launch attempts.</figcaption>
</figure>

I'm not really sure why this happens, but going by various reports of sites like StackOverflow, such crashes are pretty common. One possible explanation might be that the app uses native ARM libraries that are not supported by Android-x86 (e.g. see [here](https://stackoverflow.com/a/60148570)), but I'm not sure this is the culprit here. This needs further investigation.

By contrast, the Immer app works without any problems. Below are some screenshots that show Immer in action:

<figure class="image">
  <img src="{{ BASE_PATH }}/images/2021/02/vbox_android_immer.png" alt="Immer welcome screen (Android-x86 + VirtualBox).">
  <figcaption>Immer welcome screen (Android-x86 + VirtualBox).</figcaption>
</figure>


<figure class="image">
  <img src="{{ BASE_PATH }}/images/2021/02/vbox_android_immer_2.png" alt="Immer book selection screen (Android-x86 + VirtualBox).">
  <figcaption>Immer book selection screen (Android-x86 + VirtualBox).</figcaption>
</figure>


<figure class="image">
  <img src="{{ BASE_PATH }}/images/2021/02/vbox_android_immer_3.png" alt="Immer book reading interface (Android-x86 + VirtualBox).">
  <figcaption>Immer book reading interface (Android-x86 + VirtualBox).</figcaption>
</figure>


## Android-x86 + QEMU

### Setup

Since the Debian packages of QEMU for the Linux Mint version I'm using are way out of date, I first compiled and installed the most recent (5.2) QEMU release from its source, using the instructions [here](https://www.qemu.org/download/#source)[^7]. For setting up Android-x86 within QEMU, I largely followed [this guide by Nitesh Kumar](https://linuxhint.com/android_qemu_play_3d_games_linux/) (starting from the *Android-x86 QEMU Installation Walkthrough* section). However, these instructions initially failed for me, and I could trace this back to problems with QEMU's OpenGL (which is related to graphics rendering) support[^8]. After some experimentation, I managed to make it work by removing all OpenGL-related command line arguments. I'll briefly summarize the setup steps here:

1. Download the Android-x86 ISO installer image from [here](https://osdn.net/projects/android-x86/releases) (I picked the 64-bit 9.0 R2 release).
2. Create a virtual hard disk (size: 8 GB):
```bash
qemu-img create -f qcow2 androidx86_9_hda.img 8G
```
3. Boot the Android-x86 live ISO image inside a virtual machine, attaching also the virtual hard disk:
```bash
qemu-system-x86_64 \
-enable-kvm \
-m 2048 \
-smp 2 \
-cpu host \
-device ES1370 -device virtio-mouse-pci -device virtio-keyboard-pci \
-serial mon:stdio \
-boot menu=on \
-net nic \
-net user,hostfwd=tcp::4444-:5555 \
-hda androidx86_9_hda.img \
-cdrom android-x86_64-9.0-r2.iso
```
4. Follow [Kumar's guide](https://linuxhint.com/android_qemu_play_3d_games_linux/) (starting from the first screenshot) to install Android on the virtual machine. 
5. After the installation process is completed, close down the virtual machine (you can do this by simply closing the QEMU window), and then re-start it using (same command as before, but omitting the `-cdrom` argument):
```bash
qemu-system-x86_64 \
-enable-kvm \
-m 2048 \
-smp 2 \
-cpu host \
-device ES1370 -device virtio-mouse-pci -device virtio-keyboard-pci \
-serial mon:stdio \
-boot menu=on \
-net nic \
-net user,hostfwd=tcp::4444-:5555 \
-hda androidx86_9_hda.img
```

And here's what this looks like after start-up:: 

<figure class="image">
  <img src="{{ BASE_PATH }}/images/2021/02/qemu_android_startup.png" alt="Home app, Android-x86 on QEMU.">
  <figcaption>Home app, Android-x86 on VirtualBox.</figcaption>
</figure>

One oddity is that the rendering of colours doesn't look quite right, with reds shown as shades of blue (this is even more apparent when you open some web pages with the Chrome app). Perhaps this could be remedied by using better device settings in QEMU, but I haven't looked into this any further.

### App installation

Because of the slightly different network configuration, I had to add a reference to the `4444` network port make to make the *adb* connection to the QEMU machine:

```bash
adb connect 127.0.0.1:4444
```

After this, the package install procedure is identical to the one I showed for VirtualBox[^9].

### Results for test apps

I was able to install both test apps without problems on the QEMU machine. As with VirtualBox, the ARize app consistently crashed after launch. The Immer app worked without any issues.

## Anbox

### Setup

I installed Anbox (version 4-56c25f1) by following the [official Anbox documentation](https://github.com/anbox/anbox/blob/master/docs/install.md). After the installation, fire up the "Anbox Application Manager" from (depending on your Linux desktop) the desktop menu or launch bar:

<figure class="image">
  <img src="{{ BASE_PATH }}/images/2021/02/anbox_launch.png" alt="Anbox launcher.">
  <figcaption>Anbox launcher in Linux Mint desktop menu.</figcaption>
</figure>

Unlike the other platforms covered in this post, Anbox doesn't try to "emulate" a single device, but rather provides a compatibility layer that allows you to run Android apps from the Application Manager. Each app is launched in its own window, as shown below: 

<figure class="image">
  <img src="{{ BASE_PATH }}/images/2021/02/anbox_desktop.png" alt="Anbox Application Manager with calculator, file manager and clock apps.">
  <figcaption>Anbox Application Manager with calculator, file manager and clock apps.</figcaption>
</figure>


Looking at the information under Settings, the current Anbox release is based on Android 7.1.1, which is quite an old version. A quick test with the pre-installed Webview Browser showed Anbox couldn't connect to the internet, which apparently is a [known issue](https://github.com/anbox/anbox/issues/1724). After some searching I found [a workaround here](https://wiki.archlinux.org/index.php/Anbox#Via_NetworkManager). I simply ran the following command:

```bash
nmcli con add type bridge ifname anbox0 -- connection.id anbox-net ipv4.method shared ipv4.addresses 192.168.250.1/24
```

I then closed and re-started the Anbox Application Manager, after which internet connectivity was working properly.

### App installation

The Anbox Application Manager automatically launches an ADB server process, so you don't need to manually run `adb connect`. Other than that, you can use the regular `adb install` commands to install the APK files. 

### Results for test apps

My attempt to install the ARize app failed with this error message:

```
adb: failed to install com.Triplee.TripleeSocial.apk:
Failure [INSTALL_FAILED_NO_MATCHING_ABIS: Failed to extract native libraries, res=-113]
```

According to [this StackOverflow answer](https://stackoverflow.com/a/24572239) this error can occur if an app uses native (e.g. ARM) libraries that are not compatible with the architecture of the (virtual) destination machine.

The Immer app installed without any problems, and I was also able to launch it. However, the text in the app is partially rendered outside the app window: 

<figure class="image">
  <img src="{{ BASE_PATH }}/images/2021/02/anbox_immer.png" alt="Immer welcome screen, Anbox.">
  <figcaption>Anbox Application Manager.</figcaption>
</figure>

All text disappeared altogether after I tried to re-size or maximize the app window. Clicking on the icon at the bottom allowed me to open a personal profile page, and I was able to edit the settings there, but ultimately I was unable to get the core book selection and reading functionality working (I just got stuck at empty screens).  

## Android Studio

### Setup

### App installation

### Results for test apps


[Terms and conditions](https://developer.android.com/studio/terms.html)

> 3.1 Subject to the terms of the License Agreement, Google grants you a limited, worldwide, royalty-free, non-assignable, non-exclusive, and non-sublicensable license to use the SDK solely to develop applications for compatible implementations of Android.

and:

> 3.2 You may not use this SDK to develop applications for other platforms (including non-compatible implementations of Android) or to develop another SDK. You are of course free to develop applications for other platforms, including non-compatible implementations of Android, provided that this SDK is not used for that purpose.

Would possibly rule out use for long-term preservation? Could this be negotiated with Google? Also not clear how these terms are compatible with the Apache 2.0 License under which source is published. Also, NOTICE.txt in Android Studio root folder states:

> Android Studio includes proprietary code subject to separate license, including
> JetBrains CLion(R) (www.jetbrains.com/clion) and IntelliJ(R) IDEA Community
> Edition (www.jetbrains.com/idea).
> Copyright (C) 2000 - 2017 JetBrains s.r.o. All Rights Reserved.
> CLion, IntelliJ, and JetBrains are the registered trademarks of JetBrains s.r.o

In addition, NOTICE.txt in Android/Sdk/emulator contains licensing info for all components of the emulator, + 3000 line file with numerous licenses. So licensing situation looks complex.


|Environment|Android version|
|:--|:--|
|Android-x86 + VirtualBox|Android-x86 9.0-R2, 64-bit|


## External dependencies

Pennock, May & Day write:

> If the app is to be acquired in the most robust and complete form possible then we must find some way to deal with apps which have an inherent reliance on content hosted externally to the app. These are likely to lose their integrity over time, particularly as linkage to archived web content does not yet (if at all) appear to have become standard practice in apps.

## Conclusions

- Tests covered here are not by any means exhaustive

- External dependencies - content on external server, out of scope

- "Best" emulator in this test (Android Studio) - primarily aimed at app developers: implications for long-term maintenance? Especially given complex looking license situation + restrictive terms? 


## Acknowledgements

## Further resources

- [Considerations on the Acquisition and Preservation of Mobile eBook Apps](https://zenodo.org/record/3460450)

- [Testing Android apps on a virtual machine](https://www.sjoerdlangkemper.nl/2020/05/06/testing-android-apps-on-a-virtual-machine/)

- [How to Run Android in QEMU to Play 3D Android Games on Linux](https://linuxhint.com/android_qemu_play_3d_games_linux/)

- [Android 8.1 in qemu and Burp Suite SSL interception](https://astr0baby.wordpress.com/2019/07/09/android-8-1-in-qemu-and-burp-suite-ssl-interception/)


[^1]: The proprietary nature of iOS severely constrains any emulation options; I may address this in a future blog post.

[^3]: Actually most of these aren't really emulators in the traditional sense at all.

[^4]: This is similar to how [WINE](https://www.winehq.org/) allows one to run Windows applications on Unix-like operating systems.

[^5]: This instruction video shows how this works <https://youtu.be/h4syCHftyCs>

[^6]: Finding the correct IP address can be a bit tricky. Langkemper's blog suggests to either look at Android's Wi-Fi preferences, or to run `ip a` or `ifconfig` in the Android terminal emulator app. However, in my case the value value shown in the Wi-Fi preferences is "10.0.2.15", which is not recognised by adb. The `ifconfig` command reports 3 different entries ("wlan0", "wifi_eth" and "lo"); eventually I found the value of the "lo" ("local loopback") entry ("127.0.0.1") did the trick. So you might need to experiment a bit to make things work.

[^7]: Compilation of QEMU requires Python [Ninja package](https://ninja-build.org/), so install this first by running `python3 -m pip install --user ninja`.

[^8]: E.g. see [here](https://forums.opensuse.org/showthread.php/539026-Can-t-enable-opengl-on-the-qemu-machine) and [here](https://bugzilla.redhat.com/show_bug.cgi?id=1867343).

[^9]: If you are connected to multiple devices at the same time, you'll need to add the `-s` switch to your *adb* calls to specify the target device. See [the documentation](https://developer.android.com/studio/command-line/adb#directingcommands) for details.