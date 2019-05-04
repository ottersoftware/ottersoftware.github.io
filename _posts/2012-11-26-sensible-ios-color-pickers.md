---
date: 2012-11-26 18:17
title: Sensible iOS Colour Pickers
permalink: /post/2012-11-26-sensible-ios-color-pickers
categories: cocoa
layout: post
share: true
---

I'm working on a small iOS project and one of the requirements was to allow users to pick colours for various elements within the UI. Some initial digging revealed that there are a number of open-source options such as [ios-color-picker](https://github.com/fcanas/ios-color-picker), [RSColorPicker](https://github.com/RSully/RSColorPicker) and [ios-color-wheel](http://code.google.com/p/ios-color-wheel/). They all provide a colour wheel or rectangle and allow the user to pick any colour and adjust the brightness. [Color Picker for iOS](https://github.com/hayashi311/Color-Picker-for-iOS) takes a slightly different approach and instead offered a grid of 400 colours although it still allows the user to adjust the brightness:

I then realised that the complete freedom of choice that these colour pickers offered was going to be a negative for me. I needed to limit the colours that the user could use for a couple of reasons.

The first is that I wanted to be able to eliminate confusion. The colours in my app are used to differentiate data and allowing a user to pick any colour would inevitably lead to situations where a background colour is set to a foreground colour and suddenly the user things that their data has vanished. I wanted to have some control over the colours that are used and also warn the users if there are any 'clashes'.

The second is that one of these near-limitless colour pickers is almost a cop-out. You're giving your user complete freedom of choice because you don't want to take responsibility for this aspect of the application. There will obviously be scenarios and application where you need to give the user complete freedom but where you are using colours as interface elements is probably not one of them.

I then did a Google image search for 'color picker' to see if I could get some inspiration. If you skip over the various wheels or huge grids then you start to discover some interesting ideas.

[w3schools.com](http://www.w3schools.com) uses hexagons as the basis for allowing people to [pick HTML-friendly colours](http://www.w3schools.com/tags/ref_colorpicker.asp). After picking a core colour you pick a variant based on brightness.

A [simpler colour chart](http://coolmaxhot.com/graphics/hex-color-palette.htm) can be found on the [coolmaxhot](http://coolmaxhot.com) site uses an overall hexagonal shape but actually presents colours in squares is simpler, offers fewer colours and presents the colours beautifully so that they 'flow'.

However, recreating this as tappable elements on in iOS screen would result in a large, scrollable view which was not ideal because it may not be terribly intuitive. I was also worried that there were still too many colour options.

Apple's own OS X colour picker then provided the next bit of inspiration. Their colour picker includes a 'crayons' view which looks like this:

<img src="https://www.swwritings.com/images/2012-11-26-sensible-ios-color-pickers-03.png" alt="Apple's 'Crayons' Colour Picker" />

I liked the simplicity of this and, although I knew that some of the colours were too close to other colours the idea of 48 colours laid out in a grid of hexagons appealed. It offered limited colours and the interface would fit comfortably on an iPhone's screen. My first version looked like this:

<img src="https://www.swwritings.com/images/2012-11-26-sensible-ios-color-pickers-02.png" alt="Initial colour picker attempt" width="540" />

[<a href="https://www.swwritings.com/images/2012-11-26-sensible-ios-color-pickers-02.png" target="_blank">Full Sized Version</a>]

The closeness of some of the colours is very apparent and I will be asking a designer for some advice about providing a better palette of colours.

Once things have been tidied up and finished off I'll post an update to show off the final version.

---

**Update on 4 September 2018**

Originally this post ended with a recommendation to have a look at a Mac application called ColorSchemer Studio. David Eubanks from [Digital.com](https://digital.com) emailed me today to say that ColorSchemerStudio is no more and suggested [a blog post](https://digital.com/blog/web-colors/) written by Claire Broadley who works with him. It is a reference on colors on web and includes some links to some useful resources.