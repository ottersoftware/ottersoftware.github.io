---
date: 2015-03-21 13:26
title: Objective-C or Swift? Or Both...
categories: cocoa
layout: post
share: true
---

I have been writing Cocoa applications for seven years and until a couple of weeks ago this had been exclusively using Objective-C. For the last fortnight however I have been tinkering with Swift, Apple's new programming language which was unveiled at WWDC 2014.

When Swift was announced I, like most Cocoa developers I know, took a look at it. However the initial release was buggy and caused all sorts of issues in Xcode and I decided to leave it alone for a while. Then at the start of March Apple announced Swift 1.2 and a new beta version of Xcode. Reports were that the two were now working together much more nicely and also that Swift 1.2 was quite a big step forward in its own right. This, coupled with a feeling that Apple might well use Swift heavily throughout their talks at WWDC 2015 encouraged me to take a look.

It is worth digging into Swift if only because it is fun to try something new. I am a bit of a stick-in-the-mud when it comes to Objective-C. I like the square brackets and never really took to dot notation. I eschewed Garbage Collection but finally embraced ARC when it got thumbs up from other developers, I did not start using Auto Layout until Xcode 6 came along and made it much easier. This is probably why I had dodged Swift until recently too. I am not a huge fan of change. However I am thoroughly enjoying playing around with Swift and it was remarkably easy to pick up, at least on a superficial level.

Talking of picking it up, amongst the plethora of books available I would suggest that established Cocoa developers pick up a copy of Maurice Kelly's 'Swift Translation Guide for Objective-C Developers' ([Amazon UK](http://www.amazon.co.uk/Swift-Translation-Guide-Objective-C-Users/dp/013404469X) & [Amazon US](http://www.amazon.com/Swift-Translation-Guide-Objective-C-Users/dp/013404469X/ref=sr_1_1?ie=UTF8&qid=1426945648&sr=8-1&keywords=Swift+Translation+Guide+for+Objective-C+Developers)). It is a great 'migration' book. Daniel Steinberg's 'A Swift Kickstart' ([iBooks](https://itunes.apple.com/gb/book/a-swift-kickstart/id891801923?mt=11)) is also excellent and goes into things in a bit more detail too.

At the moment I am not planning on dropping Objective-C or migrating existing code over to Swift but on some smaller, personal projects I will start to integrate it into my existing Objective-C projects and I might even start new projects as Swift and integrate Objective-C as and when necessary.

As an example of the latter scenario, I generally use Core Data in my projects and I use [mogenerator](http://rentzsch.github.io/mogenerator/) whenever I do (it is rare for me to use third-party libraries unless I really have to so this should be taken as high praise) and the generated Objective-C code is probably more robust than the Swift code so, for now, I will keep those classes in Objective-C, even in an otherwise Swift project. 

This links back into one of the main reasons I will not be jumping into Swift whole-heartedly yet. Swift 1.2 introduced several changes and some of them break older Swift code. This happened in my own test projects and it happened in the code mogenerator creates. It is something Apple warned developers about and I do not imagine that Swift 1.2 will be the last time this happens. Swift still feels like a beta at the moment and with big evolutionary changes taking place as the language matures it might not be an ideal time to rely on it unless you have time to modify your own Swift code after updates break things. I hope that Swift will settle down soon (maybe around WWDC 2015) but the rapid evolution at least shows that Apple are listening and responding to feedback.

I am also not going to say that Swift is better than Objective-C or vice versa. Both languages have their foibles and both have their strengths and weaknesses. The best thing to do is try Swift and see what you think.

And here is the important bit. Please do not blindly accept someone else's opinion about Swift and follow that. However, even if you don't like Swift and even if you don't plan on using it I would suggest getting to know the basics if only because blogs, books, talks at conferences, etc are likely to contain Swift code these days. If you do not know the basics of Swift you may be the one to lose out.

I will finish with, if not a prediction, then a hope. If Swift is to become the standard Apple development language and if Objective-C is to be mothballed then I would like to see Apple re-write the Cocoa frameworks in Swift and take that opportunity to standardise them across iOS and OS X. If UIImage and NSImage were to evolve into a single class and if frameworks such as AVFoundation had 100% parity across the platforms then developers win and if we win so do the people who use our software.
