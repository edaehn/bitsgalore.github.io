---
layout: post
title: Archiving web pages - 
tags: [web-archiving]
---

<!-- NOTE: images for this post are in images/2019/12 folder, change this! -->

<figure class="image">
  <img src="{{ BASE_PATH }}/images/2019/09/tapes-dds-dlt.jpg" alt="DDS-1 (left) and DLT-IV (right) tape">
</figure>

There are various situations where you might want to save a copy of a web page, and add it to a web archive like [*Internet Archive*](https://archive.org/) or [*archive.today*](https://archive.today/). In 2014 I wrote [a short tutorial on how to save web pages to *Internet Archive*]({{ BASE_PATH }}/2014/08/02/How-to-save-a-web-page-to-the-Internet-Archive). Most of the information there is now outdated, and it also doesn't mention *archive.today*. What's more, over the last few years some new tools have emerged that are highly effective for saving types of web content that are difficult or even impossible to archive with *Internet Archive* or *archive.today*. Because of this, an up-to-date version of the tutorial was long overdue.

Like the 2014 post, this tutorial is meant to be an accessible introduction to a non-technical audience that is not familiar with web archiving tools. Also, it only covers saving *single web pages*, not *entire sites*![^1]

<!-- more -->

## Method 1: web forms

Perhaps the simplest way to save a web page to *Internet Archive* or *archive.today* is to use the web forms on both sites.

### Internet Archive

For *Internet Archive*, follow these steps:

1. Go to the *Internet Archive* website: <https://archive.org/web/>.
2. Paste the URL of the page you want to archive into the *Save Page Now* box (at the bottom-right).
3. Click on the *Save Page* button (or press *enter*).
4. Wait while the page is being saved. Once the archiving process is complete, your browser shows the archived page. Done!

### Archive.today

For *archive.today*, the steps are similar:

1. Go to the *archive.today* website: <https://archive.today/>.
2. Paste the URL of the page you want to archive into the red box at the top of the page (titled *My url is alive and I want to archive its content*).
3. Click on the *save* button (or press *enter*).
4. If the page was already saved before, you may get a confirmation dialog. Click on the *save* button to create a new snapshot.
5. Wait while the page is being saved. Once the archiving process is complete, your browser shows the archived page. Done!

## Method 2: browser add-ons and extensions

While the web form methods work perfectly well, they can become a bit of a hassle when you want to save multiple pages, or when you want to save a page to both *Internet Archive* and *archive.today*. For these cases you might want to use a browser add-on or extension.

### Save URL to Wayback add-on (Firefox)

If you're using *Firefox*, your best choice is probably Jonathan McCann's [*Save URL to Wayback Machine*](https://addons.mozilla.org/en-US/firefox/addon/save-url-to-wayback-machine/) add-on, which allows you to save a web page to either *Internet Archive*, *archive.today*, or both at the same time. To archive a page, the steps are:

1. Open the page you want to archive in *Firefox*.
2. Open the context menu by right-clicking anywhere on the page, and hover your mouse to the *Archive URL* context menu item.
3. Select one of the three available options (*Archive to archive.today*, *WayBack Machine* or *both*):
    <figure class="image">
    <img src="{{ BASE_PATH }}/images/2019/12/addon-firefox.png" alt="Screenshot of Firefox add-on">
    </figure>
4. This opens 1 or 2 new tabs, in which you can monitor the progress of the archiving action(s). The *archive.today* tab may again display a confirmation dialog, so make sure to click *save* if this happens. Done!

If you hover the mouse over a hyperlink on a web page, you can archive the linked page by right-clicking on the link, and then use the *Archive URL* context menu item as shown above. This is useful for e.g. archiving individual Tweets from a Twitter stream:

<figure class="image">
<img src="{{ BASE_PATH }}/images/2019/12/addon-firefox-hyperlink.png" alt="Screenshot of Firefox add-on used on hyperlink">
</figure>

For the above example the results can be seen [here (*Internet Archive*)](https://web.archive.org/web/20191213161751/https:/twitter.com/bitsgalore/status/1205162604047618048) and [here (*archive.today*)](https://archive.ph/RL4O9).

### Chrome

## Webrecorder 

*Rhizome*'s [*Webrecorder*](https://webrecorder.io/) tool set).


[](https://chrome.google.com/webstore/detail/wayback-machine/fpnmgdkabkmnadcjpehmlllkndpkmiak)


[webrecorder instruction videos](https://www.youtube.com/watch?v=yX2RrfNPQjg&list=PLYsh6x06KhqM_53nLViIAnBRoGtNbCyB5)

Legal:

[](https://www.courtlistener.com/?q=%22archive.org%22+OR+%22Internet+Archive%22+or+%22Wayback+Machine%22+-casename%3A%22Internet+Archive%22&type=r&order_by=score+desc)

## Additional reading

- [Save Pages in the Wayback Machine](https://help.archive.org/hc/en-us/articles/360001513491-Save-Pages-in-the-Wayback-Machine)

- []()

[^1]:  For archiving entire sites you would need to use web crawler software such as [*Heritrix*](https://en.wikipedia.org/wiki/Heritrix) or [*Wget*](https://en.wikipedia.org/wiki/Wget), but this is beyond the scope of this post.