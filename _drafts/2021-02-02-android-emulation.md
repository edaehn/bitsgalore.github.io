---
layout: post
title: Android emulation revisited
tags: [Android, emulation, virtualization]
comment_id: 74
---

<figure class="image">
  <img src="{{ BASE_PATH }}/images/2021/02/wwwspace.png" alt="Satellite image of Wadden Sea">
  <figcaption><a href="https://commons.wikimedia.org/wiki/File:World_Wide_Web_-_Digital_Preservation.png">“World Wide Web - Digital Preservation”</a> by <a href="https://www.wikidata.org/wiki/Q55754361">Jørgen Stamp</a>, used under <a href="https://creativecommons.org/licenses/by/2.5/dk/deed.en">CC BY 2.5 DK</a>; <a href="https://commons.wikimedia.org/wiki/File:Wadden_Sea.jpg">“Wadden Sea”</a> by Envisat satellite, used under <a href="https://creativecommons.org/licenses/by-sa/3.0-igo">CC BY-SA 3.0-IGO</a>.</figcaption>
</figure>

So far the KB hasn't actively pursued the preservation of mobile apps. However, born-digital publications in app-only form have become increasingly common, as well as "hybrid" publications, with apps that are supplemental to a paper book. We cannot ignore mobile apps any longer, and at the request of our Digital Preservation department I've started looking into how to preserve them. The [2019 iPres paper on the Acquisition and Preservation of Mobile eBook Apps by the British Library's Maureen Pennock, Peter May and Michael Day](https://zenodo.org/record/3460450) provided an excellent starting point for this, and it highlights many of the challenges involved.

However, before we can start archiving mobile apps ourselves, some additional aspects need to be addressed in more detail. One of these is the question of how to ensure long-term access. [Emulation](https://en.wikipedia.org/wiki/Emulator) is the obvious strategy here, but I couldn't find much information on the emulation of mobile platforms in a digital preservation context.So, I ran some simple experiments, where I tried to emulate two selected apps. The main objective here was twofold:

1. Get a better idea of the current state of emulation of mobile devices.

2. Get a first impression of the strengths and weaknesses of some popular emulation solutions.

For practical reasons I've limited myself to the Android platform[^1]. Attentive readers may recall I [briefly touched on this subject back in 2014]({{ BASE_PATH }}/2014/10/23/running-archived-android-apps-pc-first-impressions), but the information in that blog post is now largely outdated[^2]. 

<!-- more -->


## The apps

For the experiments, I limited myself to these two apps:

1. The Dutch-language children's book "De avonturen van Max - Op zoek naar F…" comes with an ["augmented reality" app](https://arize.io/). By pointing the mobile device's camera to selected pages in the book, the app adds 3-D visualizations and animations[^5]. 

2. [Immer](https://immer.app/) is book reading app that claims to "help\[ing\] you read more often, easily and enjoyably on your phone or tablet".

## Android emulation options

The Emulation General Wiki contains [a good overview of Android "emulators"](https://emulation.gametechwiki.com/index.php/Android_emulators)[^3]. Most of these are closed-source, and the Wiki warns that some may come with malicious apps pre-installed. For the purposes of this study these are of no interest. The listed open-source solutions are:

- [Android-x86](https://www.android-x86.org/) is a port of the [Android open source project](https://source.android.com/) to the [x86](https://en.wikipedia.org/wiki/X86) architecture. It is not an emulator, but rather an operating system that can be installed on either a physical device, or within a virtual machine (e.g. using VirtualBox or QEMU).

- [Android Studio](https://developer.android.com/studio/) is Google's offial development environment for Android. It includes an [Android Emulator](https://developer.android.com/studio/run/emulator), which "simulates Android devices on your computer so that you can test your application on a variety of devices and Android API levels without needing to have each physical device".

- [Anbox](https://github.com/anbox/anbox) is "a container-based approach to boot a full Android system on a regular GNU/Linux system like Ubuntu". It is not an emulator, but rather a [compatibility layer](https://en.wikipedia.org/wiki/Compatibility_layer)[^4].

- [shashlik](http://www.shashlik.io/) is another project for running Android apps on Linux. Going by [its description](http://www.shashlik.io/what-is/), it uses a stripped-down Android base that is run within a modified version of QEMU, combined with graphics rendering on the host machine. The project has been [inactive since 2016](https://github.com/shashlik).

Si





## Android Studio


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


<figure class="image">
  <img src="{{ BASE_PATH }}/images/2020/09/accuracyradius.png" alt="Bar chart of distribution of accuracy radius values for active domains">
  <figcaption>Distribution of accuracy radius values for active domains.</figcaption>
</figure>


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


[^1]: The proprietary nature of iOS severely constrains any emulation options; I may address this in a future blog post.

[^2]: For various reasons I never followed up on this work at the time.

[^3]: Actually most of these aren't really emulators in the traditional sense at all.

[^4]: This is similar to how [WINE](https://www.winehq.org/) allows one to run Windows applications on Unix-like operating systems.

[^5]: This instruction video shows how this works <https://youtu.be/h4syCHftyCs>