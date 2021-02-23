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

My [previous post]({{ BASE_PATH }}/2021/02/09/four-android-emulators-two-apps) addressed the emulation of mobile Android apps. In this follow-up, I'll explore some other aspects of mobile app preservation, with a focus on acquisition and ingest processes. The [2019 iPres paper on the Acquisition and Preservation of Mobile eBook Apps](https://zenodo.org/record/3460450) by Maureen Pennock, Peter May and Michael Day again was the departure point. In the its concluding section, they recommend:

> In terms of target formats for acquisition, we reach the undeniable conclusion that acquisition of the app in its packaged form (either an IPA file or an APK file) is optimal for ensuring organisations at least acquire a complete published object for preservation.

And:

> \[T\]his form should at least also include sufficient metadata about inherent technical dependencies to understand what is needed to meet them.

In practical terms, this means that the workflows that are used for acquisition and (pre-)ingest must include components that are capable of dealing with the following aspects:

1. Acquisition of the app packages (either by direct deposit from the publisher, or using the app store).
2. Identification of the package format (APK for Android, IPA for iOS).
3. Identification of metadata about the app's technical dependencies.

The main objective of this post is to get an idea of what would be needed to implement these components. Is it possible to do all of this with existing tools? If not so, what are the gaps?

As for the acquisition component, Pennock, May and Day recommend direct publisher deposit, as this may avoid some potential problems related to digital rights management and dependencies on content that is hosted remotely. Since we've only started exploring mobile app preservation at this stage, it's too early to make any assumptions about what acquisition route would work best in our case. Because of this, I started out by investigating to what extent it is possible to download APK and iOS packages from the Google Play Store and the Apple App Store, respectively.

I then tried to do automatic format identification on some sample APK and IPA packages, using recent versions of [Siegfried](https://www.itforarchivists.com/siegfried/), [Unix File](http://darwinsys.com/file/) and [Apache Tika](http://tika.apache.org/).

Next, I looked at how the APK and IPA formats store metadata about an app's technical dependencies, and how to extract this information.

The following sections discuss these aspects for the Android and iOS platforms.  

<!-- more -->

## Downloading Android packages

Android apps are distributed through the [Google Play Store](https://play.google.com/store/apps?hl=en). However, the Play Store doesn't allow you to download a local copy of an app's [Android Package (APK)](https://en.wikipedia.org/wiki/Android_application_package) on a non-Android device, which is a problem within a preservation workflow. Various third-party websites exist that offer the possibility to download APK installers, but it is often difficult to establish their trustworthiness. Some of these sites also do some re-packaging of the original data, which makes it near impossible to verify the authenticity of the downloaded packages. It also introduces security concerns, and I would advise against using any of these services within a preservation workflow.

### Virtual machine method

A better (but somewhat cumbersome) solution would be to set up a virtual machine or emulator with Android[^1], and use that to install the app directly from the Play store. Once installed, it is possible to transfer the APK installer file from the virtual machine to the host machine using the [Android Debug Bridge (adb) tool](https://developer.android.com/studio/command-line/adb). This involves the following steps:


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

## Android package identification

As most archival ingest workflows include a format identification component, I tried to identify the ARize and Immer Android packages with 3 widely used format identification tools. The table below shows the results:

|Tool|Version|ID|
|:--|:--|:--|
|[Siegfried](https://www.itforarchivists.com/siegfried/)|1.9.1; DROID Signature File V97[^7]|x-fmt/412 (Java Archive Format)<br>x-fmt/263 (ZIP Format)|
|[Unix File](http://darwinsys.com/file/)|5.32|application/zip|
|[Apache Tika](http://tika.apache.org/)|1.23|application/vnd.android.package-archive|

Apache Tika was the only tool that identified both files as Android packages. Siegfried (which uses the [PRONOM](https://www.nationalarchives.gov.uk/PRONOM/Default.aspx) format signatures) identified one file as a regular ZIP file, and the other one as a [Java Archive](https://en.wikipedia.org/wiki/JAR_(file_format)). Since the Android package format is based on the Java Archive format (which is in turn a subset of the ZIP format) this result is not necessarily wrong, but it lacks specificity. At the time of writing, the [PRONOM](https://www.nationalarchives.gov.uk/PRONOM/Default.aspx) technical registry does not have an entry for the Android package format[^6], so this result is not surprising.

## Android package metadata

To ensure long-term access, it is vital that an archived app installer is accompanied by [preservation metadata](https://en.wikipedia.org/wiki/Preservation_metadata) about the technical environment that is needed to render it. At the very minimum this would include details about the required Android version(s), hardware features, and shared software libraries. This information (and much more) is stored in an Android Package's [App Manifest](https://developer.android.com/guide/topics/manifest/manifest-intro). The App Manifest is stored in a [binary XML](https://en.wikipedia.org/wiki/Binary_XML) format for which [no publicly available documentation exists](https://reverseengineering.stackexchange.com/questions/21806/where-is-android-binary-xml-format-documented). This makes reading it somwhat challenging, although [various software solutions for decoding the App Manifest exist](https://stackoverflow.com/q/4191762/1209004).

## Extraction of App Manifest

[Apkanalyzer](https://developer.android.com/studio/command-line/apkanalyzer.html), which is part of [Android Studio](https://developer.android.com/studio/), is the officially supported tool by Google for this.However, running apkanalyzer only resulted in a sequence of Java exceptions for me[^4]. Besides, it's not entirely clear if the [terms and conditions](https://developer.android.com/studio/terms.html) of Android Studio permit is use in an archival workflow[^5]. I eventually found [Androguard](https://github.com/androguard/androguard), which is a Python-based tool that is primarily aimed at reverse-engineering Android apps. Using the command below, it will extract and decode an APK's app manifest, resulting in a human-readable XML file:

```bash
androguard axml com.Triplee.TripleeSocial.apk -o arize-android.xml
```

## Interesting App Manifest elements 

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

Apple iOS apps are distributed through the [Apple App Store](https://www.apple.com/app-store/). Installer packages are published in the [iOS App Store Package (IPA)](https://en.wikipedia.org/wiki/.ipa) format. A good overview of the format can be found [here](https://web.archive.org/web/20200714200020/https://blog.razb.me/pulling-apart-an-ios-app/). Like Google's Play Store, Apple doesn't allow you to download the packages on anything but an Apple device. Unlike the Android situation, there don't appear to be any tools that are able to get around this limitation, and this seriously limits the possibilities to incorporate downloading iOS packages as part of a preservation workflow. It might be possible to work around these limitations to some degree by installing the app on a either a physical or virtual[^10] iOS device, and then transfer the app to another machine. The open-source [libimobiledevice](https://libimobiledevice.org/) library appears to be capable of file transfers between iOS and other platforms. However, according to various online sources iOS doesn't actually keep the original IPA files after installation[^8]! I'm unable to confirm this, as I don't currently have an iOS device available for further testing.

## iOS package identification

As I was unable to obtain any IPA installers from the Apple App Store, I downloaded some random IPA files from an [iOS jailbreaking](https://en.wikipedia.org/wiki/IOS_jailbreaking) website[^9]. The table below shows the results:

|Tool|Version|ID|
|:--|:--|:--|
|[Siegfried](https://www.itforarchivists.com/siegfried/)|1.9.1; DROID Signature File V97[^7]|x-fmt/263 (ZIP Format)|
|[Unix File](http://darwinsys.com/file/)|5.32|application/zip|
|[Apache Tika](http://tika.apache.org/)|1.23|application/x-itunes-ipa|

These results are similar to the situation for Android packages. Only Apache Tika was able to identify these files as IPA packages. Both Siegfried and File could only detect the container format. An inspection of PRONOM confirmed that it doesn't include the IPA format yet.

## iOS package metadata

For iOS apps, the [information property list file](https://developer.apple.com/documentation/bundleresources/information_property_list) (Info.plist) in the root of the bundle directory[^11] contains various metadata about the app, including information about the required technical environment. Confusingly, [Apple property lists](https://en.wikipedia.org/wiki/Property_list) can be implemented in both XML and binary formats. For both test files I analyzed, the format was XML, but I'm not entirely sure if this is always the case. The Apple developer's site provides [a brief explanation of the format](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/PropertyLists/UnderstandXMLPlist/UnderstandXMLPlist.html#//apple_ref/doc/uid/10000048i-CH6-SW1), and I've uploaded [an example file here](https://github.com/KBNLresearch/mobile-apps/blob/main/sample-files/Info.plist). Unlike any other XML-based format I've seen, the correspondences between the key-value pairs in these files are not defined by the XML hierarchy, but by the order of appearance of the XML elements. See for example the following fragment:

```xml
<key>MinimumOSVersion</key>
<string>7.0</string>
<key>UIDeviceFamily</key>
<array>
  <integer>1</integer>
  <integer>2</integer>
</array>
```

In this example, the value of *MinimumOSVersion* is defined by the *string* element that directly follows the *key* element; likewise, the value of *UIDeviceFamily* is defined by the *array* element. This unusual layout means that simple parsing of these files with an XML library is not enough to interpret them in a meaningful way. 

## Extraction of information property list

I was unable to find any tools that directly extract and process the information property list (similar to what [Androguard](https://github.com/androguard/androguard) does for Android packages)[^12]. However, Python has a built-in [plistlib](https://docs.python.org/3/library/plistlib.html) module that is able to read and write property lists in both binary and XML format. I did some tests with it, and at first sight it appears to work well: reading the XML property lists for each of my test apps resulted in a Python dictionary that accurately represented the key-value pairs[^13]. Using this module, it would be fairly straightforward to write a tool that extracts the property list items directly out of an IPA file, and transform them into a more manageable format. 

## Interesting property list elements

As with the Android App Manifest before, I won't go into a detailed discussion of all the items inside the information property list, but following ones caught my immediate attention:

- The [UIRequiredDeviceCapabilities](https://developer.apple.com/library/archive/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html) key declares "the hardware or specific capabilities" that an app needs in order to run.
- The [MinimumOSVersion](https://developer.apple.com/documentation/bundleresources/information_property_list/minimumosversion) key defines the minimum operating system version required for the app to run.

Both are directly relevant for emulation purposes.

## Misc ideas

Pennock, May and Day also propose "alternative solutions such as recording or documentation" in case the end access solution (e.g. the emulator) does not provide a sufficiently 'authentic' experience.

Similar to [Saving Mementos from Virtual Worlds](https://blogs.loc.gov/thesignal/2014/02/saving-digital-mementos-from-virtual-worlds/) ((ht Andrew Berger via Twitter).

Collect additional materials that show what the app does and how, and in what context it was used. Example: videos related to the ARize app:

<https://www.youtube.com/channel/UCPEDDkRVC7jegjC02-7VsUw/videos>

"Hoe werkt mijn boek?"

<https://youtu.be/h4syCHftyCs>

Download + store as supporting documentation / context information. May also help futuer emulation efforts.

Same for Immer app:

<https://www.youtube.com/channel/UCnrnDrJ5MXccQJxaicEi7cA>



From [DPC Bit List 2020](https://www.dpconline.org/docs/miscellaneous/advocacy/2390-bitlist2020/file):

> Perhaps ios is more critical - at least with Android you can often get .apk from the
internet separate from the marketplace.

Via Euan:

[Mobile apps added to NIST’s National Software Reference Library (NSRL)](https://gcn.com/Articles/2016/12/20/NIST-mobile-software-library.aspx?m=1)


## Acknowledgements

## Further resources

- [Considerations on the Acquisition and Preservation of Mobile eBook Apps](https://zenodo.org/record/3460450)
- [gplaycli](https://github.com/matlink/gplaycli)
- [Androguard](https://github.com/androguard/androguard)
- [Pulling apart an iOS App](https://web.archive.org/web/20200714200020/https://blog.razb.me/pulling-apart-an-ios-app/)



[information property list file](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html) (Info.plist).
<https://wiki.debian.org/iPhone>


[^1]: See my [previous post on Android emulation options]({{ BASE_PATH }}/2021/02/09/four-android-emulators-two-apps).


[^2]: Note that the current (3.29) version of the tool has a small bug that results in a warning, which is [documented here](https://github.com/matlink/gplaycli/issues/272). Other than that it does work as expected.

[^3]: I used the [cmp tool](https://linux.die.net/man/1/cmp) for this, with the command `cmp base.apk com.Triplee.TripleeSocial.apk`.

[^4]: Tried with Android Studio 4.1.2, running under Linux Mint 19.3.

[^5]: See also the "Android Emulator for long-term access" section in my [previous blog post]({{ BASE_PATH }}/2021/02/09/four-android-emulators-two-apps).

[^6]: PRONOM version at the time of writing: DROID_SignatureFile_V97.xml, 1st October 2020.

[^7]: DROID Container Signature File 20201001.xml

[^8]: See e.g. [here](https://stackoverflow.com/a/29743193/1209004), [here](https://www.reddit.com/r/jailbreak/comments/4dhbtb/question_ipa_location_in_ios_9/) and [here](https://medium.com/@lucideus/extracting-the-ipa-file-and-local-data-storage-of-an-ios-application-be637745624d).

[^9]: I used the [ioninja.io](https://iosninja.io/ipa-library) site. I have no idea about the site's legal status or the safety of the downloads on offer, so proceed with caution! I only used the downloaded IPAs for some simple technical tests without installing them.

[^10]: For example using a service like [Corellium](https://corellium.com/).

[^11]: This is typically the `Payload/Application.app` folder (where "Application" is replaced with the app's name).

[^12]: There is [Ipa-metadata](https://github.com/matiassingers/ipa-metadata), which is a tool for extracting "metadata and provisdioning info about an .ipa file". Although I was able to install it, running it on any of my test files would just return a "Callback must be a function" error, and nothing else.

[^13]: The demo script that I wrote for my tests is [available here](https://github.com/KBNLresearch/mobile-apps/blob/main/scripts/readplist.py).
