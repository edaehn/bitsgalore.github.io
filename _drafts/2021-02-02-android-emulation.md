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

So far the KB hasn't actively pursued the preservation of mobile apps. However, born-digital publications in app-only form have become increasingly common, as well as "hybrid" publications, with apps that are supplemental to a paper book. We cannot ignore mobile apps any longer, and at the request of our Digital Preservation department I've started looking into how to preserve them. The [2019 iPres paper on the Acquisition and Preservation of Mobile eBook Apps by the British Library's Maureen Pennock, Peter May and Michael Day](https://zenodo.org/record/3460450) provided an excellent starting point, and it highlights many of the challenges involved.

However, before we can start archiving mobile apps ourselves, some additional aspects need to be addressed in more detail. One of these is the question of how to ensure long-term access. [Emulation](https://en.wikipedia.org/wiki/Emulator) is the obvious strategy here, but I couldn't find much information on the emulation of mobile platforms in a digital preservation context. This blog post presents the results of some preliminary app emulation experiments. For now I've limited myself to Android apps[^1] only. Attentive readers may recall I [briefly touched on this subject back in 2014]({{ BASE_PATH }}/2014/10/23/running-archived-android-apps-pc-first-impressions), but the information in that blog post is now largely outdated[^2].


<!-- more -->

## Android emulation options

[Android emulators entry on Emulation General Wiki](https://emulation.gametechwiki.com/index.php/Android_emulators)


## Test apps

 but recently the subject arose again. This time, the initial occasion was the children's book "De avonturen van Max - Op zoek naar F…". The [app](https://arize.io/) that accompanies this "augmented reality" book allows readers to unlock 3-D visualizations and animations, by pointing the mobile device's camera to pages in the physical book. Then, another [](https://immer.app/)


## Identification of Frisian sites in the .nl domain


<figure class="image">
  <img src="{{ BASE_PATH }}/images/2020/09/accuracyradius.png" alt="Bar chart of distribution of accuracy radius values for active domains">
  <figcaption>Distribution of accuracy radius values for active domains.</figcaption>
</figure>


## External dependencies

Pennock, May & Day write:

> If the app is to be acquired in the most robust and complete form possible then we must find some way to deal with apps which have an inherent reliance on content hosted externally to the app. These are likely to lose their integrity over time, particularly as linkage to archived web content does not yet (if at all) appear to have become standard practice in apps.

## Acknowledgements

## Further resources

- [Considerations on the Acquisition and Preservation of Mobile eBook Apps](https://zenodo.org/record/3460450)


[^1]: The proprietary nature of iOS severely constrains any emulation options; I may address this in a future blog post.

[^2]: For various reasons I never followed up on this work at the time.