---
layout: post
title: Offline digital data carriers in the KB deposit collection
tags: [optical-media, floppy-disks, tapes]
---

<figure class="image">
  <img src="{{ BASE_PATH }}/images/2020/02/carriers-stillife.jpg" alt="Still life of assorted data carriers">
</figure>

Following earlier work on the preservation of optical media and data tapes, I recently got a request to make an inventory of offline digital data carriers in the KB's deposit collection. The goal was to obtain approximate figures on the various carrier types in the collection. This was partially prompted by [a project on at-risk digital heritage on physical carriers](https://www.netwerkdigitaalerfgoed.nl/activiteiten/digitaal-erfgoed-houdbaar/bedreigd-digitaal-erfgoed-op-fysieke-dragers/) by the [Dutch Digital Heritage Network](https://www.netwerkdigitaalerfgoed.nl/en/) (NDE) that the KB is participating in. In this blog post I will share the results.

<!-- more -->

## Data carriers in the KB catalog

The KB catalog is the primary source of information about carriers in our collection. It is fully searchable using the [SRU protocol](https://www.loc.gov/standards/sru/).

## Carrier type codes not based on controlled vocabulary

The main piece of information to go by here is the value of the Dublin Core *extent* field[^1]. However, in our catalog the values of this field are not set from a controlled vocabulary, which means that one particular carrier type can be represented by different values. For example, here are some variations I found for CD-ROMs:

- cdrom
- cd-rom
- 1 cd-rom
- 2 cd-roms

For 5.25" floppy disks I came across:

- 1 diskette 5.25"
- 1 floppydisk 5.25"
- 1 floppydisk 5.25" in doos

This lack of consistency makes searching for specific carrier types rather difficult, and as a result most of the queries I used for this inventory are the result of trial and error.

## Catalog record can represent multiple carriers

In the examples above you may have noticed how in some cases the value represents multiple carriers (e.g. "2 cd-roms"). This is because our catalog provides access at the level of publications, but not at the level of individual carriers that are part of a publication! A typical example to illustrate this, is a physical book with 2 supplemental CD-ROMs. In this case, the catalog record represents the whole publication, and the *extent* field will have a value like "2 cd-roms". As a result, the catalog cannot be readily used to get precise figures on carrier types. In most cases the number of carriers for a specific carrier type will be considerably greater than the number of matching records, since an individual may include multiple carriers. This is not really a problem for the current exercise, as its objective is limited to getting approximate figures only.

## Results

I summarised the results in 3 tables. Here I made a subdivision between optical, magnetic and electronic carrier types. Each table contains 3 columns:

- **Carrier type:** the types listed here largely follow those proposed for an upcoming survey that will be launched as part of the NDE project on at-risk digital heritage on physical carriers (with some additions).

- **Query:** this is the [SRU](https://www.loc.gov/standards/sru/cql/) query I used to estimate the number of catalog records for this carrier type. Follow the underlying hyperlinks to run the query yourself. Note that this field is empty for carrier types that -to the best of our knowledge- are not present in our deposit collection.

- **Matching records:** this is the number of matching records in the catalog. As explained in the previous section this does not necessarily reflect the actual number of carriers, which may be greater by a factor 2 (or even more).

### Optical  carriers

|Carrier type|Query|Matching records|
|:--|:--|:--|
|CD-ROM|[extent any "cdrom\* cd-rom\*"](http://jsru.kb.nl/sru/sru?query=extent%20any%20"cdrom*%20cd-rom*&x-collection=GGC&maximumRecords=10)|8109|
|DVD|[extent any "dvd\*"](http://jsru.kb.nl/sru/sru?query=extent%20any%20"dvd*"&x-collection=GGC&maximumRecords=10)|711|
|Blu-Ray|[extent any "bluray\* blu-ray\*"](http://jsru.kb.nl/sru/sru?query=extent%20any%20"bluray*%20blu-ray*"&x-collection=GGC&maximumRecords=10)|4|
|Audio CD|[type any "geluidsdrager" and extent any "cd\* compact"](http://jsru.kb.nl/sru/sru?query=type%20any%20"geluidsdrager"%20and%20extent%20any%20"cd*%20compact"&x-collection=GGC&maximumRecords=10)|4605|
|CD-i|[extent any "cdi\* cd-i\*"](http://jsru.kb.nl/sru/sru?query=extent%20any%20"cdi*%20cd-i*"&x-collection=GGC&maximumRecords=10)|44|
|Optical carrier, unspecified|[extent any "optisch\* schijf"](http://jsru.kb.nl/sru/sru?query=extent%20any%20"optisch*%20schijf"&x-collection=GGC&maximumRecords=10)|23|

### Magnetic carriers

|Carrier type|Query|Matching records|
|:--|:--|:--|
|Tape, unspecified|[extent any "tape\*"](http://jsru.kb.nl/sru/sru?query=extent%20any%20"tape*"&x-collection=GGC&maximumRecords=10)|2|
|Compact cassette, data (datassette)|[extent any "cassetteband\*"](http://jsru.kb.nl/sru/sru?query=extent%20any%20%22cassetteband*%20datacassette*%22&x-collection=GGC&maximumRecords=10)|6|
|Floppy disk, 8"|-|0|
|Floppy disk, 5.25"|[extent any "flop\* diskette\*" and extent any "5.25\* 5,25\*"](http://jsru.kb.nl/sru/sru?query=extent%20any%20"flop*%20diskette*"%20and%20extent%20any%20"5.25*%205,25*"&x-collection=GGC&maximumRecords=10)|184|
|Floppy disk, 3.5"|[extent any "flop\* diskette\*" and extent any "3.5\* 3,5\*"](http://jsru.kb.nl/sru/sru?query=extent%20any%20"flop*%20diskette*"%20and%20extent%20any%20"3.5*%203,5*"&x-collection=GGC&maximumRecords=10)|1194|
|Floppy disk, unspecified|[extent any "flop\* diskette\*" not extent any "5.25\* 3.5\*"](http://jsru.kb.nl/sru/sru?query=extent%20any%20"flop*%20diskette*"%20not%20extent%20any%20"5.25*%203.5*"&x-collection=GGC&maximumRecords=10)|822|
|Zip Disk|[extent any "zipdis\* zip-dis\*"](http://jsru.kb.nl/sru/sru?query=extent%20any%20"zipdis*%20zip-dis*"&x-collection=GGC&maximumRecords=10)|0|
|Hard disk|[extent any "hard\*" and extent any "schijf schijv\* disk\*"](http://jsru.kb.nl/sru/sru?query=extent%20any%20"hard*"%20and%20extent%20any%20"schijf%20schijv*%20disk*"&x-collection=GGC&maximumRecords=10)|2|

### Electronic carriers

|Carrier type|Query|Matching records|
|:--|:--|:--|
|Compact Flash card|-|0|
|Sony Memory stick|-|0|
|SD card|-|0|
|USB thumb drive|[extent any "usb\*"](http://jsru.kb.nl/sru/sru?query=extent%20any%20"usb*"&x-collection=GGC&maximumRecords=10)|44|
|Solid-State drive|-|0|

## Development of optical carriers since 2015

Since I had already queried the catalog for optical carriers [as part of an investigation I did in 2015](https://zenodo.org/record/292341), I also compared the current figures against those in 2015. The following figure shows the result for the most prevalent optical carrier types:

!["Optical carrier types, 2015 vs 2020"]({{ BASE_PATH }}/images/2020/02/optical-2015-2020.png)

And here in table form:

|Carrier type|Matching records (2015)|Matching records (2020)|Increase|
|:--|:--|:--|:--|
|CD-ROM|7980|8109|129|
|Audio CD|4065|4605|540|
|DVD|554|711|157|

The increase of the number of DVD records (an 28% increase) is particularly noteworthy. In absolute terms the number of audio CD records has increased even more. The growth of the CD-ROM collection has clearly levelled off at an increase of only 129 records over 5 years.

## Conclusions

### Optical carriers

Unsurprisingly, optical carriers make up the majority of offline digital carriers in the KB deposit collection. An effort to preserve the contents of these carriers using the [*Iromlab*]({{ BASE_PATH }}/2017/06/19/image-and-rip-optical-media-like-a-boss) software is currently ongoing, but this does not cover Blu-Ray discs. At the current numbers we could probably just image them manually. The comparison against the 2015 figures shows that this collection is still growing, in particular audio CDs and DVDs.

### Magnetic carriers

In this category the number of publications with floppy disks is noteworthy. Assuming each of these publications contains between 1 and 2 disks on average, the total number of floppy disks would be in the range 2200 - 4400. The majority of these are 3.5" disks, but for about 38% the exact type cannot be established from the catalog alone. In any case the preservation of these won't be trivial at numbers like these.

An interesting curiosity is a handful of compact cassettes with software for the [*ZX Spectrum*](https://en.wikipedia.org/wiki/ZX_Spectrum) home computer.

We also appear to have 2 tapes of unknown format, and 2 hard disks. These need further investigation.

### Electronic carriers

In this category we have 44 USB thumb drives, and we should probably consider imaging them sooner rather than later. We don't appear to have any of the other carrier types in this category.

[^1]: Oddly, this field is meant to record ["The size or duration of the resource"](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/terms/extent/) as per the Dublin Core specification.

