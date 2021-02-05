---
layout: post
title: Android emulation experiments
tags: [Android, emulation, virtualization]
comment_id: 75
---

<figure class="image">
  <img src="{{ BASE_PATH }}/images/2021/02/wwwspace.png" alt="Satellite image of Wadden Sea">
  <figcaption><a href="https://commons.wikimedia.org/wiki/File:World_Wide_Web_-_Digital_Preservation.png">“World Wide Web - Digital Preservation”</a> by <a href="https://www.wikidata.org/wiki/Q55754361">Jørgen Stamp</a>, used under <a href="https://creativecommons.org/licenses/by/2.5/dk/deed.en">CC BY 2.5 DK</a>; <a href="https://commons.wikimedia.org/wiki/File:Wadden_Sea.jpg">“Wadden Sea”</a> by Envisat satellite, used under <a href="https://creativecommons.org/licenses/by-sa/3.0-igo">CC BY-SA 3.0-IGO</a>.</figcaption>
</figure>

Whatever.

<!-- more -->

## Installing and downloading Android packages

A regular Android user would just use the [Google Play Store](https://play.google.com/store/apps?hl=en) to install an app. Within a typical preservation context, you're more likely to have a local copy of the app's [Android Package (APK)](https://en.wikipedia.org/wiki/Android_application_package). Installing from a local APK can be a bit tricky, as most Android emulators don't allow you to set up shared folders between the host machine and the emulated device. The easiest method (which works for *all* of the environments covered by this post) uses the [Android Debug Bridge (adb)](https://developer.android.com/studio/command-line/adb), which is part of [Android Studio](https://developer.android.com/studio/). I will show some examples of how to use it in the sections below.

Related to this, I wanted to "emulate" the preservation-specific workflow (i.e. installation from a local APK file) as much as possible in my experiments, but then ran into the problem that the Google Play Store doesn't provide an option to download APK files to a non-Android device. Various websites exist that do offer the possibility to download APK installers. I found that many of these sites re-package the original data, which makes it near impossible to verify the authenticity of the downloaded packages. This could imply various security concerns, and I would not advise using any of these services.

There are also a number of open-source tools for downloading packages from the Play Store, but many of these are abondoned projects that no longer work. After trying out a few of them, I finally ran into [gplaycli](https://github.com/matlink/gplaycli), which is "a command line tool to search, install, update Android applications from the Google Play Store". It allows you to download an APK, using the App ID as an identifier. As an example, here's the link to the ARize app in the Play Store:

<https://play.google.com/store/apps/details?id=com.Triplee.TripleeSocial>

From the URL we can derive the app ID (`com.Triplee.TripleeSocial`). We can then download the APK like this:

```bash
gplaycli -d com.Triplee.TripleeSocial
```

Note that the current version of the tool has a small bug that results in a warning ([documented here](https://github.com/matlink/gplaycli/issues/272)), but otherwise it does work as expected, and the downloaded APKs are identical to those downloaded from the Play Store on an Android device.

## Acknowledgements

## Further resources
