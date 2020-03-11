---
layout: post
title: Does Microsoft OneDrive export large ZIP files that are corrupt?
tags: [ZIP, preservation-risks, Microsoft, OneDrive]
comment_id: 70
---

<figure class="image">
  <img src="{{ BASE_PATH }}/images/2020/03/archive-manager-onedrive.png" alt="Screenshot of opening a large OneDrive ZIP file in Linux Mint archive manager">
</figure>

We recently started using [Microsoft OneDrive](https://en.wikipedia.org/wiki/OneDrive) at work. The other day a colleague used OneDrive to share a folder with a large number of ISO images with me. Since I wanted to work with these files on my Linux machine at home, and no official OneDrive client for Linux exists a this point, I used OneDrive's web client to download the contents of the folder. Doing so resulted in a 6 GB [ZIP](https://en.wikipedia.org/wiki/Zip_(file_format)) archive. When I tried to extract this ZIP file with my operating system's (Linux Mint 19.3 MATE) archive manager, this resulted in an error dialog, saying that "An error occurred while loading the archive". The output from the underlying extraction tool ([*7-zip*](https://en.wikipedia.org/wiki/7-Zip)) reported a "Headers Error", with an "Unconfirmed start of archive". It also reported a warning that "There are data after the end of archive". No actual data were extracted whatsoever. This all looked a bit worrying, so I decided to have a more in-depth look at this problem. 

<!-- more -->

## Extracting with Unzip

As a first test I tried to extract the file from the terminal using *unzip* (v. 6.0) using the following command:

```
unzip kb-4d8a2f9a-5e0b-11ea-9376-40b0341fbf5f.zip
```

This resulted in the following output:

```
Archive:  kb-4d8a2f9a-5e0b-11ea-9376-40b0341fbf5f.zip
warning [kb-4d8a2f9a-5e0b-11ea-9376-40b0341fbf5f.zip]:  1859568605 extra bytes at beginning or within zipfile
  (attempting to process anyway)
error [kb-4d8a2f9a-5e0b-11ea-9376-40b0341fbf5f.zip]:  start of central directory not found;
  zipfile corrupt.
  (please check that you have transferred or created the zipfile in the
  appropriate BINARY mode and that you have compiled UnZip properly)
```

So, according to *unzip* the file is simply corrupt. *Unzip* wasn't able to extract any actual data.

## Extracting with 7-zip

Next I tried to  to extract the file with *7-zip* (v. 16.02) using this command:

```
7z x kb-4d8a2f9a-5e0b-11ea-9376-40b0341fbf5f.zip
```

This resulted in the following (lengthy) output:

```
7-Zip [64] 16.02 : Copyright (c) 1999-2016 Igor Pavlov : 2016-05-21
p7zip Version 16.02 (locale=en_US.UTF-8,Utf16=on,HugeFiles=on,64 bits,4 CPUs Intel(R) Core(TM) i5-6500 CPU @ 3.20GHz (506E3),ASM,AES-NI)

Scanning the drive for archives:
1 file, 6154566547 bytes (5870 MiB)

Extracting archive: kb-4d8a2f9a-5e0b-11ea-9376-40b0341fbf5f.zip

ERRORS:
Headers Error
Unconfirmed start of archive


WARNINGS:
There are data after the end of archive

--
Path = kb-4d8a2f9a-5e0b-11ea-9376-40b0341fbf5f.zip
Type = zip
ERRORS:
Headers Error
Unconfirmed start of archive
WARNINGS:
There are data after the end of archive
Physical Size = 4330182775
Tail Size = 1824383772

ERROR: CRC Failed : kb-4d8a2f9a-5e0b-11ea-9376-40b0341fbf5f/afd3f61a-5e0e-11ea-ab97-40b0341fbf5f/08.wav
                                                                              
Sub items Errors: 1

Archives with Errors: 1

Warnings: 1

Open Errors: 1

Sub items Errors: 1
```

Here we see the familiar "Headers Error" and "Unconfirmed start of archive" errors, as well as a warning about a [cyclic redundancy check](https://en.wikipedia.org/wiki/Cyclic_redundancy_check) that failed on an extracted file. Unlike *unzip*, *7-zip* does succeed in extracting some of the data, but seeing that the size of the extracted folder is only 4.3 GB, the extraction is incomplete (the size of the ZIP file is 6 GB!).

## 4 GiB size limit and ZIP64

At this point I started wondering if these issues could be related to the *size* of this particular ZIP file, especially since I have been able to process zipped OneDrive folders before without any problems. The [WikiPedia entry on *ZIP*](https://en.wikipedia.org/wiki/Zip_(file_format)#ZIP64) states that originally the format had a 4 GiB limit on the total size of the archive (as well as both the uncompressed and compressed size of a file). To overcome these limitations, a "ZIP64" extension was added to the format in [version 4.5 of the ZIP specification](https://web.archive.org/web/20011203085830/http://www.pkware.com/support/appnote.txt) (which was published in 2001). To be sure, I verified that both *unzip* and *7-zip* on my machine support ZIP64[^1].

## Some more tests

I did some additional tests to verify if my problem could be a ZIP64-related issue. First I downloaded a smaller (<4 GB) folder from OneDrive, and tried to extract the resulting ZIP file with *unzip* and *7-zip*. Both tools were able to extract the file without any issues. Next I created two 8 GB ZIP files from data on my local machine with both the *zip* and *7-zip* tools. I then tried to extract both files with both *unzip* and *7-zip* (i.e. I extracted each file with both tools). Again, both tools extracted these files without any problems. Since these tests demonstrate that both *unzip* and *7-zip* are able to handle both large ZIP files (the use ZIP64) as well as smaller OneDrive ZIP files, this suggests that something odd is going on with OneDrive's implementation of ZIP64. Most likely, it doesn't follow the format specification in one way or another, with the result that format-conformant reader applications are unable to open these files. A more in-depth analysis of these ZIP files in a Hex editor against the ZIP format specification should be able to pinpoint the exact cause of the problem, but at the time of writing I don't really have time for that.

## Testing the ZIP file integrity

The *zip* tool has a switch that can be used to test the integrity of a ZIP file. I ran it on the problematic file like this:

```
zip -T kb-4d8a2f9a-5e0b-11ea-9376-40b0341fbf5f.zip
```

Here's the result:

```
Could not find:
  kb-4d8a2f9a-5e0b-11ea-9376-40b0341fbf5f.z01

Hit c      (change path to where this split file is)
    q      (abort archive - quit)
 or ENTER  (try reading this split again): 
```

So, apparently the *zip* utility thinks this is a multi-volume file (which it isn't). Running this command on any of my other test files (the small OneDrive file, and the large files created by *zip* and *7-zip*) didn't result in any errors.

## Other reports on corrupt OneDrive ZIP archives

If a widely-used platform by a major vendor such as Microsoft indeed produces corrupt ZIP archives, I would expect others to have run into this before. [This question on *Ask Ubuntu*](https://askubuntu.com/questions/1115238/unable-to-extract-onedrive-zip-file-on-ubuntu-18-10) appears to describe the same problem, and [here's another report on corrupted large ZIP files](https://onedrive.uservoice.com/forums/913528-onedrive-on-the-web/suggestions/35321278-fix-the-corrupted-zip-download-or-be-honest-about) on a OneDrive-related forum. On Twitter Tyler Thorsted [confirmed](https://twitter.com/CHLThor/status/1237416651995283457) my results for a 5 GB ZIP file downloaded from OneDrive, adding that the Mac OS X archive utility didn't like the file either. Still, I'm surprised I couldn't find much else on this issue.

## Similarity to Apple Archive utility problem

The problem looks eerily similar to an older issue with Apple's Archive utility, which would write corrupt ZIP archives for cases where the ZIP64 extension is needed. From the [WikiPedia entry on *ZIP*](https://en.wikipedia.org/wiki/Zip_(file_format)#ZIP64):

> Mac OS Sierra's Archive Utility notably does not support ZIP64, and can create corrupt archives when ZIP64 would be required.

More details about this are available [here](https://web.archive.org/web/20140331005235/http://www.springyarchiver.com/blog/topic/topic/203) and [here](https://github.com/thejoshwolfe/yauzl/issues/69). Interestingly a [Twitter thread by Kieran O'Leary](https://twitter.com/kieranjol/status/1200118816686137344) put me on track of this issue (which I hadn't heard of before). It's not clear to me if the OneDrive problem is identical or even related, but because of the similarities I thought it was worth a mention here.

## Conclusion

Based on the tests presented here, it seems that something is seriously wrong with large ZIP files that are exported from the Microsoft OneDrive web client. The problem is most likely related to OneDrive's implementation of the ZIP64 extension.

## Feedback

Since I could find so little information about this issue online, I'm curious if others can reproduce it, or have more details. If one of the most widely-used cloud storage platforms exports ZIP files that are corrupt or otherwise malformed, this could have pretty serious consequences, so any additional info is very welcome. Note that I recently added the possibility to post comments to my blog using Github (just click on the *Comments* button, log in to Github and then add you comment in the Github issue tracker). Otherwise get in touch via [Twitter](https://twitter.com/bitsgalore) or [Mastodon](https://digipres.club/@bitsgalore).

[^1]: For unzip you can check this this by running it with the `--version` switch. If the output includes `ZIP64_SUPPORT` this means ZIP64 is supported.
