---
layout: post
title: Restoring Liesbet's Virtual Home, a digital treasure from the early Dutch web  
tags: [web-archaeology, web-archiving, xs4all]
comment_id: 72
---

<figure class="image">
  <img src="{{ BASE_PATH }}/images/2020/06/liesbet-door.png" alt="Liesbet door">
  <figcaption>Original artwork copyright &copy;Liesbet Zikkenheimer</figcaption>
</figure>

Introduction here ...

<!-- more -->

## XS4ALL

[XS4ALL](https://en.wikipedia.org/wiki/XS4ALL) is one of the oldest internet service providers in the Netherlands, and even one of the oldest providers in the whole world. The company was founded in 1993, and has its roots in the Dutch hacker scene. Since its inception, many pioneers of the Dutch internet hosted their homepages under the XS4ALL web domain. Some of these homepages have been online for more than 25 years, and as such they rank among the oldest born-digital publications of the Dutch web.

In 2019, parent company KPN (which bought XS4ALL in 1998) announced their intention to phase out the XS4ALL brand. Eventually, all of the company's services will continue under the KPN brand. This poses an acute threat to much of the (often unique) digital heritage that is hosted on the XS4ALL domain, as in the past a similar situation with provider [Euronet](https://nl.wikipedia.org/wiki/EuroNet) resulted in [the loss of thousands of early Dutch homepages](https://www.tandfonline.com/doi/full/10.1080/24701475.2019.1603951).

## Rescuing the XS4ALL homepages

So, the KB's [web archiving team](https://www.kb.nl/en/organisation/research-expertise/long-term-usability-of-digital-resources/web-archiving) started an initiative to rescue a selection of the XS4ALL homepages, and add them to the web archive in a special XS4ALL collection. This initiative is supported financially by the [SIDN Fund](https://www.sidnfonds.nl/excerpt) and [stichting Internet4all](https://www.stichtinginternet4all.nl/).

## Digital treasures

As of May 2020, the KB has selected [3370 homepages](https://www.kb.nl/blogs/duurzame-toegang/bewaren-voor-iedereen-de-opbouw-van-de-webcollectie-xs4all-homepages) for inclusion in XS4ALL collection. Out of the homepages that have been archived so far, 404 are marked as "digital treasures". These homepages are remarkable because of their age, or because of characteristics that are either unique, or, on the other hand, typical of a particular era or trend.

## Liesbet's Virtual Home

One of these "treasures" is [Liesbet's Virtual Home](https://ziklies.home.xs4all.nl/) (in Dutch: Liesbet's Atelier). This is the old homepage of [Liesbet Zikkenheimer](http://zicnet.nl/), a Dutch Internet pioneer with a background in industrial and graphic design. Her homepage is a "treasure" for several reasons. First of all, it has a history that goes back to 1995, which makes it one of the oldest Dutch homepages that are still available today. Second, Zikkenheimer is an important figure in the history of the Dutch internet. To mention a few examples, in 1997 she developed and published [the online version of popular women's magazine Libelle](https://web.archive.org/web/19980526231446/http://www.libelle.nl/libelle/dezeweek/dezeweek.html). She also created web sites for the Margriet and Viva magazines, and developed, published and managed several well-known web portals that were specifically targeted at women. Finally, Liesbet's Virtual Home is unique because of its structure and design. The site is literally structured like a physical house. Each page represents a particular room, and to get from, say, the living room to the loft, one needs to navigate through a hallway and two flights of stairs. It also has some interactive features that were quite unique at the time of is creation. So, the site meets every possible "digital treasure" criterion.

## Problems with the live site

Even though Liesbet's Virtual Home [is still online](https://ziklies.home.xs4all.nl/), several features of the site are no longer working. In particular:

- The navigation on several pages (including the landing page) is broken, because it is based on server-side [image maps](https://en.wikipedia.org/wiki/Image_map) that are no longer available.

- Some interactive features like [this bedroom mirror](https://ziklies.home.xs4all.nl/slaapk/e-slaap1.html) don't work anymore, because the underlying scripts are missing.

This raised the question whether it would be possible to create a "restored" version of Liesbet's Virtual Home.

## Local copy of site data

After my colleague Kees Teszelszky got in contact with Zikkenheimer, she sent us a ZIP file with a locally stored copy of the site's directory structure. However, that local copy had several issues as well, and it quickly became obvious that it couldn't be used as a basis for a restored version of the site. However, the ZIP file did contain both the image map files as well as the scripts that were missing from the live site.

## Crawling the toilet

I started out by crawling the live site with [this simple Bash script](https://github.com/KBNLresearch/xs4all-resources/blob/master/scripts/scrapesite.sh) that uses the [wget](https://www.gnu.org/software/wget/) tool. This worked reasonably well, but on closer inspection the [toilet](https://ziklies.home.xs4all.nl/e-toilet.html) pages of the site turned out to be missing from the result. Digging a bit deeper revealed the cause: these pages are simply not referenced from their [parent pages](https://ziklies.home.xs4all.nl/e-start.html). The solution was to repeat the crawl, using the toilet pages (both the Dutch and English-language version) as seed URLs. I did this by creating a text file (seed-urls.txt) with both URLs:

```
https://ziklies.home.xs4all.nl/toilet.html
https://ziklies.home.xs4all.nl/e-toilet.html
```

Then I ran [this Bash script](https://github.com/KBNLresearch/xs4all-resources/blob/master/scripts/scrape-toilet.sh), using the above text file as a command-line argument:

```
scrape-toilet.sh seed-urls.txt
```

I then ran a recursive diff on the output directories of both the original crawl and the "toilet" crawl:

```
diff -r ./wget-site/ziklies.home.xs4all.nl/ ./wget-toilet/ziklies.home.xs4all.nl/ > diff-site-toilet.txt
```

This confirmed that the "toilet" crawl contained everything that is also in the original crawl. So, I used the result of this "toilet" crawl as a basis for all subsequent restoration steps. In the following sections I will go through the whole restoration process. More details are available in a separate, minimally edited [Restoration notes](https://github.com/KBNLresearch/xs4all-resources/blob/master/doc/liesbets-atelier-restoration-notes.md) document.


## Missing links to toilet

As a first modification, I changed the erroneous "toilet" links on the [start.html](https://ziklies.home.xs4all.nl/start.html) and [e-start.html](https://ziklies.home.xs4all.nl/e-start.html) pages. Looking at the English-language page:

```HTML
<B><A HREF="http://imagine.xs4all.nl/ziklies/"> Go to the toilet</a></B>
```

Here, the URL points to an external domain that doesn't exist anymore. So, I changed this to the local file:

```HTML
<B><A HREF="e-toilet.html"> Go to the toilet</a></B>
```

I applied a similar fix to the Dutch-language page.

## Audit trail

Since a restoration like this involves making changes to a unique digital heritage work, it's a good idea to record these changes in a verifiable audit trail. To achieve this I simply set up the directory with the "toilet" crawl as a [Git](https://en.wikipedia.org/wiki/Git) repository, and then created a snapshot (Git commit) for each change. The following screenshot (from the [gitk](https://git-scm.com/docs/gitk) Git repository browser) illustrates this:

![Gitk screenshot]({{ BASE_PATH }}/images/2020/06/liesbet-git.png)

- The upper-left pane lists all snapshots/commits (newest at the top, oldest at the bottom).

- The lower-left pane shows all changes between the currently selected snapshot and the previous one. In this example, we can that in file "e-start.html" one line was removed (highlighted in red), and another was added (highlighted in green).

- The lower-right pane lists all files that were changed in this snapshot.

This way, the commit history provides a complete audit trail of all changes.

## Image maps

A number of pages on the site use HTML [image maps](https://en.wikipedia.org/wiki/Image_map). An example is the door image on the [front page](https://ziklies.home.xs4all.nl/). This is the corresponding HTML source:

```HTML
<A HREF="/cgi-bin/imagemap/~ziklies/deurtje1.map"><img src="deurtje1.gif" Border=0 ISMAP></A>
```

This is a *server-side* image map, where the URL points to a file (deurtje1.map). However, this file is not available anymore, which results in a [Page Not Found](https://en.wikipedia.org/wiki/HTTP_404) error. Fortunately, I was able to find this file in the ZIP archive provided by Zikkenheimer. Here's what it looks like:

```
default http://www.xs4all.nl/~ziklies/start.html
poly http://www.xs4all.nl/~ziklies/start.html 0,56 76,40 91,43 89,67 76,62 6,77 0,60 1,55
poly http://www.xs4all.nl/~ziklies/e-start.html 53,72 80,76 81,81 91,85 89,107 76,106 69,137 42,130 51,77
```

The file simply defines areas within the image that are linked to URLs. Since *server-side* image maps [come with some caveats](https://eager.io/blog/a-quick-history-of-image-maps/), I took the liberty of re-implementing the server-side image map with a *client-side* image maps. These are functionally identical, but simpler and less likely to break[^2]. Instead of using an external file, a client-side image map is simply an embedded element inside the page, which means we can replace the `<A>` element in the previous HTML snippet by this:

```HTML
<img src="deurtje1.gif" usemap="#deurtje1Map" alt="deurtje 1" border="0">
<map name="deurtje1Map">
    <area shape="poly" coords="0,56 76,40 91,43 89,67 76,62 6,77 0,60 1,55" href="http://www.xs4all.nl/~ziklies/start.html">
    <area shape="poly" coords="53,72 80,76 81,81 91,85 89,107 76,106 69,137 42,130 51,77" href="http://www.xs4all.nl/~ziklies/e-start.html">
    <area shape="default" href="http://www.xs4all.nl/~ziklies/start.html">
</map>
```

Note that the values of the "coords" attributes are identical to the area definitions in the server-side image map. Below is a short video that shows how the restored image map works. Ringing the upper doorbell leads to the Dutch version of the site, whereas the lower doorbell opens the English version.

 <video width="100%" height="100%" controls>
  <source src="{{ BASE_PATH }}/images/2020/06/imagemap.mp4" type="video/mp4" alt="Image map video">
  Your browser does not support the video tag.
</video>

The site contains 4 more broken server-side image maps. I replaced all of these with client-side image maps in the restored version. For [one page](https://ziklies.home.xs4all.nl/start.html) the corresponding image map from the ZIP file contained some odd errors, so here I took the liberty of using the image map of the page's [English-language counterpart](https://ziklies.home.xs4all.nl/e-start.html), and then updated all links accordingly. After these changes the image map navigation is fully functional again.

## Links to old website domain

Like all XS4ALL homepages, Liesbet's Virtual Home was originally hosted as a directory under XS4ALL's root domain (<http://www.xs4all.nl/~ziklies/>). At some point XS4ALL gave its customers their own sub-domain (in this case the current address at <https://ziklies.home.xs4all.nl/>), and redirected any URLs pointing to the "old" location to this sub-domain. Internally, Liesbet's Virtual Home uses a mixture of relative URLs and absolute ones that still use the old location. This causes several issues if the site is hosted locally on a web server. Although it may be possible to remedy these issues using some clever server configuration, I couldn't quite get this working. I ended up writing a [simple Bash script](https://github.com/KBNLresearch/xs4all-resources/blob/master/scripts/rewriteurls.sh) that replaces all references to the "old" location with relative links (which always work, irrespective of the domain). This had an unintentional side-effect for the [statistics page](https://ziklies.home.xs4all.nl/statistics.html), so I subsequently undid the change for this single page (which can be done with 1 single Git command).

## Interactive bedroom mirror

The [bedroom](https://ziklies.home.xs4all.nl/slaapk/e-slaap1.html) of Liesbet's Virtual Home features an "interactive mirror". It is a web form where the visitor can select combinations of clothing, hairstyle and earrings. After clicking on the "have a look into the mirror" button, the selected combination is shown as an image[^3]. However, as the underlying scripts are missing from the live site, it now gives a [Page Not Found](https://en.wikipedia.org/wiki/HTTP_404) error. As with the image maps before, the missing (Perl) scripts could be recovered from the ZIP file provided by Zikkenheimer. I added these to a (newly created) "cgi-bin" directory. I also had to make the scripts executable[^5], and adjust their [Shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) strings to a valid interpreter location on my local machine (which, in my case, was different from the location used by the original web server). In addition, an inspection of the scripts showed them to rely on a set of 21 GIF images that were not included in the crawl. Again, I used the local ZIP file to fill this gap[^4].

The main challenge was then to make the scripts play nicely with a web server. This is beyond the scope of this post, but I created an [Apache setup notes](https://github.com/KBNLresearch/xs4all-resources/blob/master/doc/liesbets-atelier-apache-notes.md) document that describes how I made this all work with a local instance of the Apache web server. The following video gives a brief glimpse of the restored interactive mirror:

 <video width="100%" height="100%" controls>
  <source src="{{ BASE_PATH }}/images/2020/06/mirror.mp4" type="video/mp4" alt="Mirror video">
  Your browser does not support the video tag.
</video>

## Missing items

On a number of occasions the site uses Javascript to open items in a popup window. [Here's](https://ziklies.home.xs4all.nl/woonk/woon03.html) an example:

```HTML
<A HREF="javascript:openit('tvplus.mov')">
```

These items are not picked up by wget, which means they are missing from the crawl. I identified these cases using this command:

```
grep -r "javascript:openit" ~/kb/liesbets-atelier/liesbets-atelier/ > javascript-open.txt
```

This identified 2 missing Quicktime movies, and an HTML file. The latter in turn contained a link to a Shockwave file, which was missing as well. I used wget to download these missing items:

```
wget https://ziklies.home.xs4all.nl/woonk/tvplus.mov
```

## Remaining issues

Although the above modifications restore most of the broken features, there are still an number of unresolved issues.

### Interactive toilet door

The [toilet page](https://ziklies.home.xs4all.nl/e-toilet.html) has an interactive feature where visitors can scratch a message onto a virtual toilet door. This works by way of an external form[^6] that sends the entered message by e-mail. The ZIP file that Zikkenheimer sent us contains a Python script that generates a GIF image of the toilet door with the received message added to it, but it isn't entirely clear how this script interfaces with the e-mail component[^7]. For now, I left this unchanged.

### Unsupported file formats

The site also uses a number of file formats that are not supported by modern browser. Some examples:

- The [living room](https://ziklies.home.xs4all.nl/woonk/e-woon03.html) features a clickable TV-set that is supposed to open a [Quicktime video](http://fileformats.archiveteam.org/wiki/Quicktime) in a pop-up window. In the latest (77.0.1) version of the Firefox browser, it triggers the following message:

  ![No video with supported format and MIME type found]({{ BASE_PATH }}/images/2020/06/quicktime-ff.png)

  In Chromium (83.0.4103.61), the pop-up window is empty, but it does download the file, so it can be played with external media player software.

- The clickable stereo on the same page links to a [Sun Audio](http://fileformats.archiveteam.org/wiki/AU) file. Depending on the browser used, clicking the link either activates a prompt to play the file in a external player (Firefox), or it is simply downloaded (Chromium)[^8].

- [This page](https://ziklies.home.xs4all.nl/slaapk/e-slaapa.html) features an embedded alarm clock in MIDI format, which is also not natively supported by modern web browsers. Chromium does download the file, whereas Firefox appears to ignore it altogether.

- Last but not least, the bedroom page links to [this](https://ziklies.home.xs4all.nl/slaapk/gspot/index.html), which in turn [links](https://ziklies.home.xs4all.nl/slaapk/gspot/gspot.dcr) to an embedded [Adobe Shockwave file](https://en.wikipedia.org/wiki/Adobe_Shockwave). Adobe [stopped supporting this format in 2019](https://helpx.adobe.com/shockwave/shockwave-end-of-life-faq.html). As a result, the format is no longer supported in web browsers. With no alternative rendering software available, the format is now functionally obsolete.

I haven't addressed any of these issues in the current restoration attempt. A possible solution would be to use emulation or virtualization to view the site in a late-'90s web browser[^9]. This may be worth further investigation.

## Serving the site

Throughout the restoration process I mostly used Python's built-in [http.server](https://docs.python.org/3/library/http.server.html) to test any changes I made. This is a lightweight web server that doesn't require any elaborate configuration, with no need to copy files to reserved locations on the file system. It does have some limitations that make it unsuitable for production use, so for serving the "completed" site I set up and configured an [Apache](https://httpd.apache.org/) web server instance. This allowed me to have the restored version of Liesbet's Virtual Home running on my local machine, accessible from its original URL:

![Screenshot of Liesbet's Virtual Home, served with local Apache instance]({{ BASE_PATH }}/images/2020/06/atelier-local-apache.png)

The installation and configuration process I followed is described in detail in this separate [Apache setup notes](https://github.com/KBNLresearch/xs4all-resources/blob/master/doc/liesbets-atelier-apache-notes.md) document.

## WARC capture

Like most web archives, the KB uses the [WARC](https://en.wikipedia.org/wiki/Web_ARChive) format for storing archived web sites. Since capturing offline web content is a subject I'd been [working on earlier as part of the NL-menu rescue operation]({{ BASE_PATH }}/2018/07/11/crawling-offline-web-content-the-nl-menu-case), I started with [this wget-based script](https://github.com/KBNLresearch/xs4all-resources/blob/master/scripts/scrape-local-site.sh), which is a modified version of the script I used for NL-menu. However, the wget crawl didn't adequately capture the script behind the [interactive bedroom mirror](https://ziklies.home.xs4all.nl/slaapk/e-slaap1.html). Some tests with the [Webrecorder Desktop App](https://github.com/webrecorder/webrecorder-desktop) showed that Webrecorder was able to capture individual input combinations of the form linked to the script, but this required manual input for each combination. With 512 possible combinations, this was not a viable solution, and I needed some way to automate this. Happily, several people responded to [my request for help on Twitter](https://twitter.com/bitsgalore/status/1275405890947108866). Webrecorder author Ilya Kreymer responded I might want to have a look at the [warcio library](https://github.com/webrecorder/warcio) (of which is he is also the lead developer), and even better he [provided some example code](https://twitter.com/IlyaKreymer/status/1275440674687471617) that showed how to do this. A quick test confirmed Ilya's approach worked, after which I re-wrote my existing wget-based Bash script into a Python script that uses only warcio. The script is [available here](https://github.com/KBNLresearch/xs4all-resources/blob/master/scripts/scrape-ziklies-local.py).

## Rendering the WARC with Pywb

I finally verified the WARC capture by importing it in [Pywb](https://github.com/webrecorder/pywb). More details on this can be found in my separate [WARC capture and rendering notes](https://github.com/KBNLresearch/xs4all-resources/blob/master/doc/liesbets-atelier-warc-notes.md). Rendering the WARC did not result in any problems, and to illustrate this below screenshot shows one output combination of the interactive bedroom mirror:

![Output of interactive bedroom mirror script, rendered from WARC capture]({{ BASE_PATH }}/images/2020/06/mirror-pywb.png)

## Conclusions

Restoring Liesbet's Virtual Home has been quite a laborious process, and the very length of this blog post is probably an indication of that. Nevertheless, this effort is justified given the value and historical significance of this homepage. It also involved some choices that, from an authenticity point of view, are somewhat ambiguous. An example is the replacement of the missing server-side image maps by the more modern client-side variety. The experience gained from this project will also be useful for an upcoming attempt to restore a collection of old corporate websites from source data [that we recovered from data tapes last year]({{ BASE_PATH }}/2019/09/09/recovering-90s-data-tapes-experiences-kb-web-archaeology).

## Acknowledgments

I would like to thank Liesbet Zikkenheimer for making the offline data of Liesbet's Virtual Home available to us for this project. Jak Boumans is thanked for alerting us to this unique homepage, and for establishing the contact with its creator. Ilya Kreymer is thanked for his suggestion on warcio, and more generally for creating the Webrecorder software suite, without which much of this work would simply be impossible. Thanks are also due to Kees Teszelszky. Finally, this work was financially supported by the [SIDN Fund](https://www.sidn.nl/en) and [Stichting Internet4all](https://www.stichtinginternet4all.nl/).

## Additional resources

The following notes provide more details on the restoration steps, the Apache server setup and the WARC capture process, respectively:

- [Liesbet's atelier restoration notes](https://github.com/KBNLresearch/xs4all-resources/blob/master/doc/liesbets-atelier-restoration-notes.md)

- [Liesbet's Atelier Apache setup notes](https://github.com/KBNLresearch/xs4all-resources/blob/master/doc/liesbets-atelier-apache-notes.md)

- [Liesbet's atelier WARC capture and rendering notes](https://github.com/KBNLresearch/xs4all-resources/blob/master/doc/liesbets-atelier-warc-notes.md)

- [Processing scripts](https://github.com/KBNLresearch/xs4all-resources/tree/master/scripts)

Below posts (both in Dutch) give some additional background information about the XS4ALL homepages rescue initiative:

- [Bewaren voor iedereen: de opbouw van de webcollectie XS4ALL-homepages](https://www.kb.nl/blogs/duurzame-toegang/bewaren-voor-iedereen-de-opbouw-van-de-webcollectie-xs4all-homepages)

- [Redden wat van waarde is: webarchivering homepages XS4ALL](https://www.sidnfonds.nl/nieuws/redden-wat-van-waarde-is-webarchivering-homepages-xs4all)


[^2]: Some purists may consider this a technological anachronism. Client-side image maps were first introduced in HTML 3.2, which was published in 1997, whereas Liesbet's ["what's new" page](https://ziklies.home.xs4all.nl/new.html) shows that most of the site's development activity took place between early 1995 and late 1997. This could be a problem for users who want to view the site in a period browser (e.g. inside an emulated environment), which may not support client-side image maps.

[^3]: Actually as a composite of 3 images.

[^4]: I later found out these images are still present on the live site (but since they are not referenced by any of its pages they aren't picked up by a web crawl).

[^5]: Under Linux this is simply a matter of issuing a command like `chmod 755 barbie.cgi`.

[^6]: Documented here: <https://www.xs4all.nl/service/installeren/hosting/mail-a-form-toevoegen/>.

[^7]: The script is also written for Python 1.4, and some of the code is incompatible with modern (Python 3) interpreters. It would probably be possible to update it to Python 3 pretty easily.

[^8]: I'm not actually sure if this behavior was any different on late-'90s browsers.

[^9]: See e.g. [oldweb.today](http://oldweb.today/).
