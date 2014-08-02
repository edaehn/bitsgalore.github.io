---
layout: post
title: How to save a web page to the Internet Archive
tags: English archiving webpage wayback
---
This short tutorial shows how to take a snapshot of a web page, and save it to the Internet Archive's [Wayback Machine](http://en.wikipedia.org/wiki/Wayback_Machine).

<!-- more -->

##Method 1: web interface

1. Open the Wayback website:   
    <https://archive.org/web/>
2. Paste the URL of the page you want to archive into the *Save Page Now* box (at the bottom-right).
3. Click on the *Save Page* button (or press *enter*).
4. Wait while the page is being crawled. Once the archiving process is complete, the URL of the archived page appears.

##Method 2: bookmarklet
This method is faster than using the web interface, but you will first need to install a [bookmarklet](http://en.wikipedia.org/wiki/Bookmarklet) (which is just a browser bookmark that contains some JavaScript).

###Installation
1. Go to the *Save Page to Wayback Machine Bookmarklet* link here: 
    <http://marklets.com/Save%20Page%20to%20Wayback%20Machine.aspx>

2. Click at the left-hand site of the URL bar, and drag it to the bookmarks toolbar of your browser. The figure below shows how this works in FireFox: 

    ![Installation of bookmarklet]({{ BASE_PATH }}/images/wbmarkletInstall.png)

    Alternatively you can also use *Add Bookmark* in the *Bookmarks* menu.

###Using the bookmarklet
1. Open the web page that you want to save in your browser.
2. Click on *Save Page to Wayback Machine* in the bookmarks toolbar.
3. Wait while the page is being crawled. Once the archiving process is complete, the URL of the archived page appears.

##Limitations
* Webmasters can use [*robots.txt*](http://en.wikipedia.org/wiki/Robots_exclusion_standard) to prevent web crawlers from crawling/saving anything on their website. 
* If a webmaster decides to change the *robots.txt* permissions at some point in the future, a saved page may be removed from the Wayback Machine. For details see: <https://archive.org/about/exclude.php>.

##Acknowledgement
This tutorial partially draws from a [blog post](http://searchengineland.com/save-urls-wayback-machine-demand-191150) by Gary Price on [*Search Engine Land*](http://searchengineland.com/).