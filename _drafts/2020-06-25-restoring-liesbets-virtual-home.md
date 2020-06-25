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

[XS4ALL](https://en.wikipedia.org/wiki/XS4ALL) is one of the oldest internet service providers in the Netherlands. The company was founded in 1993, and has its roots in the Dutch hacker scene. Since its inception, many pioneers of the Dutch internet hosted their homepages under the XS4ALL web domain. Some of these homepages have been online for more than 25 years, and as such they rank among the oldest born-digital publications of the Dutch web.

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

## Crawl live site, start in the toilet

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

This confirmed that the "toilet" crawl contained everything that is also in the original crawl. So, I used the result of this "toilet" crawl as a basis for all subsequent restoration steps.

<!--
## Lightweight web server for testing

For testing I also made extensive use of Python's built-in [http.server](https://docs.python.org/3/library/http.server.html). It can be started by simply running the following command from the directory that contains the local site data:

```
python3 -m http.server
```

Subsequently the contents of the directory can be accessed from <http://127.0.0.1:8000/>[^1]. Note that http.server is  a lightweight web server that is not suitable for production use (for that use [Apache](https://httpd.apache.org/) instead), but it is really useful for fast and easy testing[^1].
-->

## Fix links to toilet

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

## Restore image maps

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

The site contains 4 more broken server-side image maps. I replaced all of these with client-side image maps in the restored version, which makes the image map navigation fully functional again.

## Fix links to old website domain

Like all XS4ALL homepages, Liesbet's Virtual Home was originally hosted as a directory under XS4ALL's root domain (<http://www.xs4all.nl/~ziklies/>). At some point XS4ALL gave its customers their own sub-domain (in this case the current address at <https://ziklies.home.xs4all.nl/>), and redirected any URLS pointing to the "old" location to this sub-domain. Internally, Liesbet's Virtual Home uses a mixture of relative URLs and absolute ones that still use the old location. This causes several issues if the site is hosted locally on a web server. Although it may be possible to remedy these issues using some clever server configuration, I couldn't quite get this working. I ended up writing a [simple Bash script](https://github.com/KBNLresearch/xs4all-resources/blob/master/scripts/rewriteurls.sh) that replaces all references to the "old" location with relative links (which always work, irrespective of the domain). This had an unintentional side-effect for the [statistics page](https://ziklies.home.xs4all.nl/statistics.html), so I subsequently undid the change for this single page (which can be done with 1 single Git command).

## Restore interactive bedroom mirror

The [bedroom](https://ziklies.home.xs4all.nl/slaapk/e-slaap1.html) of Liesbet's Virtual Home features an "interactive mirror". It is a web form where the visitor can select combinations of clothing, hairstyle and earrings. After clicking on the "have a look into the mirror" button, the selected combination is shown as an image[^3]. However, as the underlying scripts are missing from the live site, it now gives a [Page Not Found](https://en.wikipedia.org/wiki/HTTP_404) error. As with the image maps before, the missing (Perl) scripts could be recovered from the ZIP file provided by Zikkenheimer. I added these to a (newly created) "cgi-bin" directory. I also had to make the scripts executable[^5], and adjust their [Shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) strings to a valid interpreter location on my local machine (which, in my case, was different from the location used by the original web server). In addition, an inspection of the scripts showed them to rely on a set of 21 GIF images that were not included in the crawl. Again, I used the local ZIP file to fill this gap[^4].

The main challenge was then to make the scripts play nicely with a web server. This is beyond the scope of this post, but I created an [Apache setup notes](https://github.com/KBNLresearch/xs4all-resources/blob/master/doc/liesbets-atelier-apache-notes.md) document that describes how I made this all work with a local instance of the Apache web server. The following video gives a brief glimpse into how the resurrected interactive mirror works.

 <video width="100%" height="100%" controls>
  <source src="{{ BASE_PATH }}/images/2020/06/mirror.mp4" type="video/mp4" alt="Mirror video">
  Your browser does not support the video tag.
</video>

## Remaining issues

Toilet door, midi files, Shockwave file, QuickTime movies (toilet page, can be played by external players, so could be fixed by migrating).

## Serve with web server

## Crawl restored site to warc

Wget, doesn't capture behavior of mirror script, asked on Twitter, Ilya, warcio.

<!--

Eerste contact met Liesbet via Jak Boumans:

[Twitter](https://twitter.com/DigiVerleden/status/1131885560908406784). Noemen in dankwoord.

Noem ook financiële bijdragen van het [SIDN Fonds](https://www.sidnfonds.nl) en [Stichting Internet4all](https://www.stichtinginternet4all.nl/).

-->

## Acknowledgments

Liesbet Zikkenheimer, Jak Boumans, Ilya Kreymer, SIDN Fund, Stichting Internet4all, Kees Teszelszky.

## Additional resources

The following notes provide more details on the restoration steps, the Apache server setup and the WARC capture process, respectively:

- [Liesbet's atelier restoration notes](https://github.com/KBNLresearch/xs4all-resources/blob/master/doc/liesbets-atelier-restoration-notes.md)
- [Liesbet's Atelier Apache setup notes](https://github.com/KBNLresearch/xs4all-resources/blob/master/doc/liesbets-atelier-apache-notes.md)
- [Liesbet's atelier WARC capture and rendering notes](https://github.com/KBNLresearch/xs4all-resources/blob/master/doc/liesbets-atelier-warc-notes.md)

Below posts (both in Dutch) give some additional background information about the XS4ALL homepages rescue initiative:

- [Bewaren voor iedereen: de opbouw van de webcollectie XS4ALL-homepages](https://www.kb.nl/blogs/duurzame-toegang/bewaren-voor-iedereen-de-opbouw-van-de-webcollectie-xs4all-homepages)
- [Redden wat van waarde is: webarchivering homepages XS4ALL](https://www.sidnfonds.nl/nieuws/redden-wat-van-waarde-is-webarchivering-homepages-xs4all)


[^2]: Some purists may consider this a technological anachronism. Client-side image maps were first introduced in HTML 3.2, which was published in 1997, whereas Liesbet's ["what's new" page](https://ziklies.home.xs4all.nl/new.html) shows that most of the site's development activity took place between early 1995 and late 1997. This could be a problem for users who want to view the site in a period browser (e.g. inside an emulated environment), which may not support client-side image maps.

[^3]: Actually as a composite of 3 images.

[^4]: I later found out these images are still present on the live site (but since they are not referenced by any of its pages they aren't picked up by a web crawl).

[^5]: Under Linux this is simply a matter of issuing a command like `chmod 755 barbie.cgi`.