#About jpylyzer
*Jpylyzer* is a validator and feature extractor for *JP2* images. *JP2* is the still image format that is defined by [Part 1](http://www.jpeg.org/public/15444-1annexi.pdf) of the [JPEG 2000](http://www.jpeg.org/jpeg2000/) image compression standard (ISO/IEC 15444-1).

This tool was specifically created to answer the following questions that you might have about a *JP2* file:

1. Is this really a *JP2*, and does it conform to the format&#8217;s specifications (validation)?
2. What are the technical characteristics of the image (feature extraction)?

#Installation
The easiest option is to use the binary builds that exist for both *Windows*- and *Linux*-based systems. These are completely stand-alone, without any dependencies on other software (i.e. you don&#8217;t need *Python* to use them). 

##Windows
Download the Windows binaries using the link in the right-hand bar. Then extract the contents of the *ZIP* file to any directory you like. You should now be able to run *jpylyzer* from your command prompt. For example, assuming that you installed the contents  of the *ZIP* file to the directory *C:\jpylyzer\\*, type or paste the following line into a command prompt window:

    C:\jpylyzer\jpylyzer

This should give you the *jpylyzer* helper message.

###Running jpylyzer without typing the full path
Optionally, you may also want to add the full path of the jpylyzer installation directory to the Windows *Path* environment variable. Doing so allows you to run jpylyzer from any directory on your PC, without having to type the full path. In Windows 7 you can do this by selecting *settings* from the *Start* menu; then go to *control panel*/*system* and click on  *advanced system settings*. Then click on the *environment variables* button. Finally, locate the *Path* variable in the *system variables* window, click on *Edit* and add the full *jpylyzer* path (this requires local Administrator privileges). The settings take effect once you open a new command prompt.

##Linux
Debian packages of jpylyzer exist for *AMD6* and *i386* Linux architectures. To install, simply download the Debian package using the link in the right-hand bar, double-click on it and select *Install Package*. Alternatively you can also do this in the command terminal by typing:

    sudo dpkg -i jpylyzer_1.10.1_amd64.deb

In both cases you need to have superuser privileges.

##Python (any system)
Instead of using the binary builds, you can also download *jpylyzer*&#8217;s source code to run *jpylyzer* as a [*Python*](http://www.python.org/) script. This should work on any platform, but note that this requires either *Python* 2.7 (earlier versions won&#8217;t work), or *Python* 3.2 or later (3.0 and 3.1 won&#8217;t work either!).

# Basic usage
    usage: jpylyzer [-h] [--verbose] [--wrapper] [--version] ...

## Positional arguments

`...` : input JP2 image(s), may be one or more (whitespace-separated) path expressions; prefix wildcard (\*) with backslash (\\) in Linux..

## Optional arguments

`-h, --help` : show this help message and exit;

`-v, --version` : show program's version number and exit;

`--verbose` : report test results in verbose format;

`--wrapper, -w` : wrap the output for individual image(s) in 'results' XML element.

## Output 
Output is directed to the standard output device (*stdout*).

## Example

    jpylyzer rubbish.jp2 > rubbish.xml

In the above example, output is redirected to the file &#8216;rubbish.xml&#8217;.

#License
*Jpylyzer* is free software: you can redistribute it and/or modify
it under the terms of the [GNU Lesser General Public License](https://www.gnu.org/licenses/lgpl.html) as published by the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

#Funding
The development of jpylyzer was partially funded by the EU FP 7 project [*SCAPE* (SCAlabable Preservation Environments)](http://www.scape-project.eu/).
