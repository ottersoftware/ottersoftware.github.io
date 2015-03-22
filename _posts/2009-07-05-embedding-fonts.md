---
date: 2009-07-05 12:00
title: Embedding Fonts
permalink: 2009-07-05-embedding-fonts
categories: cocoa
layout: post
share: true
---

I'm working on an application that needs to display the current timecode of a movie file. I wanted to create a display that looked something like an old-school VCR clock and found a font whose license allowed me to distribute it freely. The next problem was being able include the font in my application's bundle rather than having to have it installed in one of the traditional font folders. Fortunately this turned out to be easier than I expected.

There is a key you can add to your Info.plist file called 'Application Fonts Resource Path' or ATSApplicationFontsPath. This simply specifies the folder within the application bundle's Resources folder where any additional fonts you want to use are stored. Once this is done you can use the usual NSFont methods such as fontWithName:size: to use the fonts.

Below is a step-by-step guide to doing this:

1. Add a font to your project's resources.

2. Add a new Copy Files Build Phase to the Target project. Ensure the destination is Resources and specify a path.

<img src="http://images.swwritings.com/2009-07-05-embedding-fonts-01.png" alt="Copy Files Build Phase" />

In this case the font will be stored in the bundle's Resources/AppFonts folder.

3. Add the ATSApplicationFontsPath key to the Info.plist file. This can either be done via Xcode or the Property List editor or via a text editor.

<img src="http://images.swwritings.com/2009-07-05-embedding-fonts-02.png" alt="Info.plist" />

<img src="http://images.swwritings.com/2009-07-05-embedding-fonts-03.png" alt="Raw Info.plist" />

4. Drag the font from the Resources group to the new build phase. You may need to delete the font from the 'Copy Bundle Resources' build phase.

<img src="http://images.swwritings.com/2009-07-05-embedding-fonts-04.png" alt="Build Phases" />

5. After building your project the font file should be stored in the designated font folder.

<img src="http://images.swwritings.com/2009-07-05-embedding-fonts-05.png" alt="Finder Window" />

### Download
[Download](http://images.swwritings.com/2009-07-05-embedding-fonts.zip) a sample project.

### Crystal Font
The font used in this example is [Crystal](http://www.fontspace.com/allen-r-walden/crystal) by Allen R Walden.

### Please Note
This approach works under 10.5 but I am not sure if it works with earlier versions of OS X.
