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

This resulted in a file "com.Triplee.TripleeSocial.apk". I verified the file by doing a bitwise comparison against the APK obtained from the "virtual machine method" described in the previous section[^3]. This confirmed both files were identical. It's worth mentioning that gplaycli is also [reported to work for downloading paid apps](https://github.com/matlink/gplaycli/issues/8) (provided the proper login credentials are used), but I haven't tested this.

## Format identification

As most archival ingest workflows include a format identification component, I tried to identify the ARize and Immer Android packages with 3 widely used format identification tools. The table below shows the results:

|Tool|Version|ID (ARize)|ID (Immer)|
|:--|:--|:--|:--|
|[Siegfried](https://www.itforarchivists.com/siegfried/)|1.9.1; DROID Signature File V97[^7]|x-fmt/412 (Java Archive Format)|x-fmt/263 (ZIP Format)|
|[Unix File](http://darwinsys.com/file/)|5.32|application/zip|application/zip|
|[Apache Tika](http://tika.apache.org/)|1.23|application/vnd.android.package-archive|application/vnd.android.package-archive|

Apache Tika was the only tool that identified both files as Android packages. Siegfried (which uses the [PRONOM](https://www.nationalarchives.gov.uk/PRONOM/Default.aspx) format signatures) identified one file as a regular ZIP file, and the other one as a [Java Archive](https://en.wikipedia.org/wiki/JAR_(file_format)). Since the Android package format is based on the Java Archive format (which is in turn a subset of the ZIP format) this result is not necessarily wrong, but it lacks specificity. At the time of writing, the [PRONOM](https://www.nationalarchives.gov.uk/PRONOM/Default.aspx) technical registry does have an entry for the Android package format[^6], so this result is not surprising.

## Metadata extraction

To ensure long-term access, it is vital that an archived app installer is accompanied by [preservation metadata](https://en.wikipedia.org/wiki/Preservation_metadata) about the technical environment that is needed to render it. At the very minimum this would include details about the required Android version(s), hardware features, and shared software libraries. This information (and much more) is stored in an Android Package's [App Manifest](https://developer.android.com/guide/topics/manifest/manifest-intro). The App Manifest is stored in a [binary XML](https://en.wikipedia.org/wiki/Binary_XML) format for which [no publicly available documentation exists](https://reverseengineering.stackexchange.com/questions/21806/where-is-android-binary-xml-format-documented). This makes reading it somwhat challenging, although [various software solutions for decoding the App Manifest exist](https://stackoverflow.com/q/4191762/1209004). [Apkanalyzer](https://developer.android.com/studio/command-line/apkanalyzer.html), which is part of [Android Studio](https://developer.android.com/studio/), is the officially supported tool by Google for this.However, running apkanalyzer only resulted in a sequence of Java exceptions for me[^4]. Besides, it's not entirely clear if the [terms and conditions](https://developer.android.com/studio/terms.html) of Android Studio permit is use in an archival workflow[^5]. I eventually found [Androguard](https://github.com/androguard/androguard), which is a Python-based tool that is primarily aimed at reverse-engineering Android apps. Using the command below, it will extract and decode an APK's app manifest, resulting in a human-readable XML file:

```bash
androguard axml com.Triplee.TripleeSocial.apk -o arize-android.xml
```

The decoded app manifest can be found in full [here](https://github.com/KBNLresearch/mobile-apps/blob/main/sample-files/arize-androidManifest.xml). A detailed discussion of the app manifest is beyond the scope of this post, but it's worth highlighting a few elements that are particularly interesting:

- The [uses-sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element) element contains information about the app's compatibility with one or more Android versions:
  ```xml
  <uses-sdk android:minSdkVersion="24" android:targetSdkVersion="29"/>
  ```
  Here, the (confusingly named) `minSdkVersion` and `targetSdkVersion` attributes define the minimum and target API levels of the app, respectively. In [the table here](https://developer.android.com/guide/topics/manifest/uses-sdk-element#ApiLevels) we see that API level 24 (the minimum level) corresponds to Android 7.0, and level 29 (the target level) to Android 10.

- The [uses-feature](https://developer.android.com/guide/topics/manifest/uses-feature-element) element is used to declare hardware or software features that are used by the app:
  ```xml
  <uses-feature android:name="android.hardware.camera" android:required="true"/>
  ```
  In the above example (taken from the ARize app), it informs us that the app needs a camera.

- The [uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element) element tells us about any shared libraries that the app depends on. 

The above information largely defines the (emulated) technical environment that is required to run the app. Even though I've only skimmed the surface of the App Manifest here, its importance as a source for deriving technical and preservation metadata about an Android app should be clear.

## Downloading iOS packages

Apple iOS apps are distributed through the [Apple App Store](https://www.apple.com/app-store/). Much like Google's Play Store, it doesn't allow you to download the installer packages (published in the [iOS App Store Package (IPA)](https://en.wikipedia.org/wiki/.ipa) format) on anything but an Apple device. Unlike the Android situation, there don't appear to be any tools that are able to get around this limitation, and this seriously limits the possibilities to incorporate downloading iOS packages as part of a preservation workflow. It might be possible to work around these limitations to some degree by installing the app on a (physical) iOS device, and then transfer the app to another machine. The open-source [libimobiledevice](https://libimobiledevice.org/) library appears to be capable of file transfers between iOS and other platforms. However, according to various online sources iOS doesn't actually keep the original IPA files after installation[^8]! I'm unable to confirm this, as I don't currently have an iOS device available for further testing.

## Format identification

As most archival ingest workflows include a format identification component, I tried to identify the ARize and Immer Android packages with 3 widely used format identification tools. The table below shows the results:

|Tool|Version|ID (ARize)|ID (Immer)|
|:--|:--|:--|:--|
|[Siegfried](https://www.itforarchivists.com/siegfried/)|1.9.1; DROID Signature File V97[^7]|x-fmt/412 (Java Archive Format)|x-fmt/263 (ZIP Format)|
|[Unix File](http://darwinsys.com/file/)|5.32|application/zip|application/zip|
|[Apache Tika](http://tika.apache.org/)|1.23|application/vnd.android.package-archive|application/vnd.android.package-archive|

[](https://en.wikipedia.org/wiki/.ipa)

<https://libimobiledevice.org/>

<https://wiki.debian.org/iPhone>

## Misc ideas

Collect additional materials that show what the app does and how, and in what context it was used. Example: videos related to the ARize app:

<https://www.youtube.com/channel/UCPEDDkRVC7jegjC02-7VsUw/videos>

"Hoe werkt mijn boek?"

<https://youtu.be/h4syCHftyCs>

Download + store as supporting documentation / context information. May also help futuer emulation efforts.

Same for Immer app:

<https://www.youtube.com/channel/UCnrnDrJ5MXccQJxaicEi7cA>

See also (ht Andrew Berger via Twitter):

[Saving Mementos from Virtual Worlds](https://blogs.loc.gov/thesignal/2014/02/saving-digital-mementos-from-virtual-worlds/)


From [DPC Bit List 2020](https://www.dpconline.org/docs/miscellaneous/advocacy/2390-bitlist2020/file):

> Perhaps ios is more critical - at least with Android you can often get .apk from the
internet separate from the marketplace.

Via Euan:

[Mobile apps added to NIST’s National Software Reference Library (NSRL)](https://gcn.com/Articles/2016/12/20/NIST-mobile-software-library.aspx?m=1)


## Acknowledgements

## Further resources

- [gplaycli](https://github.com/matlink/gplaycli)
- [Androguard](https://github.com/androguard/androguard)

[^1]: See my [previous blog on Android emulation options]({{ BASE_PATH }}/2021/02/09/four-android-emulators-two-apps).


[^2]: Note that the current (3.29) version of the tool has a small bug that results in a warning, which is [documented here](https://github.com/matlink/gplaycli/issues/272). Other than that it does work as expected.

[^3]: I used the [cmp tool](https://linux.die.net/man/1/cmp) for this, with the command `cmp base.apk com.Triplee.TripleeSocial.apk`.

[^4]: Tried with Android Studio 4.1.2, running under Linux Mint 19.3.

[^5]: See also the "Android Emulator for long-term access" section in my [previous blog post]({{ BASE_PATH }}/2021/02/09/four-android-emulators-two-apps).

[^6]: PRONOM version at the time of writing: DROID_SignatureFile_V97.xml, 1st October 2020.

[^7]: DROID Container Signature File 20201001.xml

[^8]: See e.g. [here](https://stackoverflow.com/a/29743193/1209004), [here](https://www.reddit.com/r/jailbreak/comments/4dhbtb/question_ipa_location_in_ios_9/) and [here](https://medium.com/@lucideus/extracting-the-ipa-file-and-local-data-storage-of-an-ios-application-be637745624d).
