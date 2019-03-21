---
layout: post
title: A simple workflow tool for imaging optical media using readom and ddrescue
tags: [optical-media, omimgr, isolyzer]
---

In 2015 I wrote [a blog post on preserving optical media from the command-line]({{ BASE_PATH }}/2015/11/13/preserving-optical-media-from-the-command-line). Among other things, it suggested a rudimentary workflow for imaging CD-ROMs and DVDs using the [*readom*](http://linux.die.net/man/1/readom) and [*ddrescue*](http://linux.die.net/man/1/ddrescue) tools. Since *readom* (which is part of the [*cdrkit*](https://en.wikipedia.org/wiki/Cdrkit) library) is specifically designed for reading optical media, it is generally preferable over generic block device recovery tools such as [*Guymager*](https://guymager.sourceforge.io/), [*dd*](http://linux.die.net/man/1/dd) or [*ddrescue*](http://linux.die.net/man/1/ddrescue). However, *readom* gives up rather easily on discs that are damaged or otherwise degraded, and for these cases *ddrescue* is often capable of recovering surprising amounts of data. For example, our earlier success at [resurrecting the first Dutch web index]({{ BASE_PATH }}/2018/04/24/resurrecting-the-first-dutch-web-index-nl-menu-revisited) was largely thanks to *ddrescue*'s ability to work its magic on the degraded CD-recordable that contained the source data. Even though we now have a [highly automated workflow in place]({{ BASE_PATH }}/2017/06/19/image-and-rip-optical-media-like-a-boss) for bulk processing optical media from our deposit collection, *readom* and *ddrescue* still prove to be useful for various special cases that don't fit into this workflow. This applies in particular to materials that that we are currently receiving within the context of our recent web archaeology activities. These are typically small collections of recordable CD-ROMs that are often quite old, and such discs are highly likely to be degraded to some extent. For these cases a highly automated, [*iromlab*](https://github.com/KBNLresearch/iromlab)-like workflow is unnecessary, and possibly even impractical. However, processing such discs by hand from the command-line is not ideal either. The addition of metadata (fixity information, descriptive metadata, and technical and event metadata) gets particularly cumbersome in this case. I've been thinking of ways to streamlining the tried-and-tested *readom*  / *ddrescue* workflow for some time, but I simply never got round to it. However, my recent work on [*tapeimgr*](https://github.com/KBNLresearch/tapeimgr) made me think it could serve as a workable template for a similar tool for optical media. So, starting from the *tapeimgr* code I created [*omimgr*](https://github.com/KBNLresearch/omimgr), which is a simple workflow tool for imaging optical media. In the the remainder of this blog post I will give an overview of this tool and how to use it.

<!-- more -->

## The command-line workflow

The basic command-line workflow on which *omimgr* is based looks like this:

1. Unmount the disc:

        umount /dev/sr0

2. Try to create an ISO image of the disc with *readom*:

        readom retries=4 dev=/dev/sr0 f=disc.iso

3. If *readom* fails, try to image the disc with *ddrescue*:

        ddrescue -b 2048 -r4 -v /dev/sr0 disc.iso disc.map

4. If *ddrescue* was unable to recover all the data on the disc, try to improve the result by re-running *ddrescue* in direct disc mode:

        ddrescue -d -b 2048 -r4 -v /dev/sr0 disc.iso disc.map

5. If there are still read errors after the above command, try to improve the result by re-running *ddrescue* with another optical drive (e.g. an external USB-drive):

        ddrescue -b 2048 -r4 -v /dev/sr1 disc.iso disc.map

    (Note that steps 4 and 5 can be repeated for mutiple optical drives, if needed).

6. Check the extracted ISO image for completeness with [*isolyzer*](https://github.com/KBNLresearch/isolyzer):

        isolyzer disc.iso

    If the value of the *smallerThanExpected* element equals *False*, this is an indication that the ISO image is probably intact.

In most cases we will also want to capture some metadata with the created disc image. A pretty minimal set includes:

- Fixity information, such as a SHA-512 checksum of the ISO image.
- Descriptive metadata, such as an identifier that is associated with the disc, a descriptive title (e.g. derived from an inlay card or the writing on the disc), any notes (e.g. on the disc's condition).
- Event metadata about the image acquisition process: the imaging software (and its associated version) that was used, the options it was invoked with, the status of the imaging process, and the outcome of any quality checks on the generated ISO image.

Adding the above metadata by hand is extremely cumbersome, especially when multiple discs need to be processed. It is also prone to all sorts of errors.

## Omimgr workflow

*Omimgr* wraps all of the above steps in a single workflow, using a simple graphical front-end. On start-up, *omimgr* shows the following entry form:

![Screenshot of omimgr interface]({{ BASE_PATH }}/images/2019/03/omimgr-1.png)

The entry fields are largely self-explanatory (but they are all [documented here](https://github.com/KBNLresearch/omimgr)). The user can select an output directory, specify the device path that points to the optical drive (by default it uses the internal drive at `/dev/sr0`), and a  preferred read method (by default *omimgr* starts with *readom*). There is also a set of entry fields for basic descriptive metadata. Note that most of the default values shown here can be easily changed by modifying a configuration file. 

![CD-ROM with writing]({{ BASE_PATH }}/images/2019/03/cd-writing.jpg)

## Limitations

Currently, *omimgr* only works on Linux-based systems. It does not support audio CDs at this stage, and can only be used for CD-ROMs and DVDs. The behaviour with [*Blue Book / CD-Extra*](https://en.wikipedia.org/wiki/Blue_Book_(CD_standard)) discs hasn't been tested yet; however, even if the extraction of the data track works, *omimgr* will report their processing as unsuccessful because of a failed *isolyzer* check on the ISO image[^1]. It is also worth noting that *omimgr* only exposes a limited subset of *readom*'s  and *ddrescue*'s functionality to the user. Those who are looking for a full graphical front-end to *ddrescue* with access to all of its options should probably check out [*DDRescue-GUI*](https://launchpad.net/ddrescue-gui) (from which *omimgr* incidentally borrowed a few lines of code) instead. Finally, the current version of *omimgr* is an initial release which so far has had limited testing, so use at your own risk (as always), and feel free to [report any issues](https://github.com/KBNLresearch/omimgr/issues) that you may come across. 

## Link to omimgr

[*omimgr](https://github.com/KBNLresearch/omimgr)


[^1]: See [here]({{ BASE_PATH }}/2017/04/25/imaging-cd-extra-blue-book-discs) for a detailed discussion of why this happens. This may change in future versions of *omimgr* (a fix is actually pretty easy, but it adds additional dependencies which I decided to avoid for this initial release).