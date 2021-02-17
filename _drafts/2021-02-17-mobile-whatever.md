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

## Downloading Android packages

Android apps are distributed through the [Google Play Store](https://play.google.com/store/apps?hl=en). However, the Play Store doesn't allow you to download a local copy of an app's [Android Package (APK)](https://en.wikipedia.org/wiki/Android_application_package) on a non-Android device, which is a problem within a preservation workflow. Various third-party websites exist that offer the possibility to download APK installers, but it is often difficult to establish their trustworthiness. Some of these sites also do some re-packaging of the original data, which makes it near impossible to verify the authenticity of the downloaded packages. It also introduces security concerns, and I would advise against using any of these services within a preservation workflow.

### Virtual machine method

A better (but somewhat cumbersome) solution would be to set up a virtual machine or emulator with Android[^1], and use that to install the app directly from the Play store. Once installed, it is possible to transfer the APK installer file from the virtual machine to the host machine using the [Android Debug Bridge (adb) tool](https://developer.android.com/studio/command-line/adb). This involves the following steps (all after installation of the app inside the virtual machine):


1. Get a list of all installed packages on the virtual machine (redirecting output to a text file):
```bash
adb shell pm list packages > packages.txt
```
2. Look up the package id of the installed app in this file. Taking the [ARize app](https://play.google.com/store/apps/details?id=com.Triplee.TripleeSocial) as an example, the identifier is `com.Triplee.TripleeSocial`. We can then use the following command to find the full file path of the package on the VM:
```bash
adb shell pm path com.Triplee.TripleeSocial
```
  Result:
```
package:/data/app/com.Triplee.TripleeSocial-r8iVFUp1MOSAc6LmHA1MDQ==/base.apk
```
3. Use the above file path to download the package to the host machine:
```bash
adb pull /data/app/com.Triplee.TripleeSocial-r8iVFUp1MOSAc6LmHA1MDQ==/base.apk
```

In this case, this results in a file "base.apk".

### Gplaycli method

As the above method is a bit clumsy, I started looking for tools that allow downloading packages from the Play Store directly. Several such open-source tools exist, but many of these are abondoned projects that no longer work. After trying out a few of them, I utimately had success with [gplaycli](https://github.com/matlink/gplaycli), which is "a command line tool to search, install, update Android applications from the Google Play Store". It allows you to download an APK, using the App ID as an identifier. Taking the ARize app as an example again, we can download the APK with the following command[^2]:

```bash
gplaycli -d com.Triplee.TripleeSocial
```

This resulted in a file "com.Triplee.TripleeSocial.apk". I verified the file by doing a bitwise comparison against the APK obtained from the "virtual machine method" described in the previous section[^3]. This confirmed both files were identical.

## Technical checks and metadata extraction

### Apkanalyzer

The [apkanalyzer](https://developer.android.com/studio/command-line/apkanalyzer.html) tool is part of [Android Studio](https://developer.android.com/studio/). It can be used to query an APK's file attributes, inspect its contents, and to cross-compare APKs. Unfortunately, I was unable to make the tool work on my machine, and running apkanalyzer only resulted in a sequence of Java exceptions[^4]. That aside, it's not entirely clear if [terms and conditions](https://developer.android.com/studio/terms.html) of Android Studio permit the use of this tool in an archival workflow[^5].

### Androguard

[Androguard](https://github.com/androguard/androguard) is a Python-based tool that is primarily aimed at reverse-engineering Android apps. Some of its functionality is also very useful for extracting technical metadata. It is particularly useful for extracting and decoding the [App Manifest](https://developer.android.com/guide/topics/manifest/manifest-intro). The manifest contains general information about the package, its components, and its hardware and software requirements. The manifest uses a [binary XML](https://en.wikipedia.org/wiki/Binary_XML) format for which [no publicly available documentation exists](https://reverseengineering.stackexchange.com/questions/21806/where-is-android-binary-xml-format-documented). Using the command below, Androguard will extract and decode an APK's app manifest, resulting in human-readable XML:

```bash
androguard axml com.Triplee.TripleeSocial.apk -o arize-manifest.xml
```

It is beyond the scope of this post to cover the Android App Manifest in any detail, but I'll highlight some elements that are immediately relevant to long-term preservation.

The (confusingly named) `android:minSdkVersion` and `android:targetSdkVersion` attributes (part of the [uses-sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element) element) define the minimum and target API levels of the app, respectively. For the ARize app the values are 24 and 29: 

```xml
<uses-sdk android:minSdkVersion="24" android:targetSdkVersion="29"/>
```

These API levels directly correspond to Android versions. In [the table here](https://developer.android.com/guide/topics/manifest/uses-sdk-element#ApiLevels) we see that API level 24 (the minimum level) corresponds to Android 7.0, and level 29 (the target level) to Android 10. So, this information will allow us to figure out the required emulated environment to to run any Android app in the future.

[uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element) element - external dependency:

> If this element is present and its android:required attribute is set to true, the PackageManager framework won't let the user install the application unless the library is present on the user's device. 

## Misc ideas

Collect additional materials that show what the app does and how, and in what context it was used. Example: videos related to the ARize app:


<https://www.youtube.com/channel/UCPEDDkRVC7jegjC02-7VsUw/videos>

"Hoe werkt mijn boek?"

<https://youtu.be/h4syCHftyCs>

Download + store as supporting documentation / context information. May also help futuer emulation efforts.


## Acknowledgements

## Further resources

[^1]: See my [previous blog on Android emulation options]({{ BASE_PATH }}/2021/02/09/four-android-emulators-two-apps).


[^2]: Note that the current (3.29) version of the tool has a small bug that results in a warning, which is [documented here](https://github.com/matlink/gplaycli/issues/272). Other than that it does work as expected.

[^3]: I used the [cmp tool](https://linux.die.net/man/1/cmp) for this, with the command `cmp base.apk com.Triplee.TripleeSocial.apk`.

[^4]: Tried with Android Studio 4.1.2, running under Linux Mint 19.3.

[^5]: See also the "Android Emulator for long-term access" section in my [previous blog post]({{ BASE_PATH }}/2021/02/09/four-android-emulators-two-apps).