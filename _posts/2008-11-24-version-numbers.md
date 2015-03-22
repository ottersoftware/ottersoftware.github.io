---
date: 2008-11-24 12:00
title: Version Numbers
permalink: /post/2008-11-24-version-numbers
categories: cocoa
layout: post
share: true
---

### The Core Intuition Podcast
In episode four of Core Intuition, Daniel Jalkut's and Manton Reece's excellent podcast, the conversation evolved from Apple's Design Awards to application version numbering. Daniel and Manton raised some very good points and this has prompted me to use their thoughts as the basis of writing an article about it. If you want to listen to this particular episode then you can download it via [http://coreint.org/audio/CoreInt_4.mp3](http://coreint.org/audio/CoreInt_4.mp3). The section about version numbers starts at 15:20 but I would strongly recommend that you start at the beginning, listen all the way through and then subscribe to the podcast and listen to all of the other back-episodes. Many thanks to Daniel and Manton for allowing me to plagiarise their work.

### The Basic Concept
Most developers these days base their application version numbers on three sets of numbers which represent the overall version number, the major release number and the minor release number. The version number will increment when significant new features are added to the application, the major release number changes as smaller features are added and the minor release number increases as bug fixes are implemented.

For example, when you first release a new application it would be version 1.0 (not 1.0.0 -- see below for comments about this). If some bug fixes are made then the application would become version 1.0.1. More bug fixes would result in 1.0.2. A fairly minor feature is added to the application which pushes it to 1.1 (note that the minor release number is reset because the bug fixes are wrapped up in the version which features the new functionality) and this is followed by the addition of some fairly major features which push the application version number to 2.0.

### Point-Ten
There is a strange resistance to going beyond a point-ten version number, whether this be the major release number such as 1.10.0 or the minor release number such as 1.0.10. In the podcast Daniel confesses to having an almost allergic reaction to hitting a point-ten release number because he feels that it is "unholy and not Macintosh". Both Daniel and Manton believe that before Apple released versions 10.4.10 and 10.4.11 of OS X this had not really happened and that before OS X itself was released it was not even possible to flag a version as point-ten or higher. This desire not to go above a point-nine release even resulted in Daniel delaying the release of a bug fix for Black Ink so that rather than releasing a 1.0.10 it could roll over to the 1.1 version.

Normally I would put this down to be an Apple-ingrained ideology or something which is peculiar to developers whose logical brains process version numbers in some strange, base ten manner but a couple of years ago I was working on a Windows application where the project manager expressed concern and confusion that we were talking about a 1.10 release of an application rather than a 2.0 release. On the basis that the project manager in question was far from technically minded (he always appeared to have sort of stumbled into IT project management like someone would gratefully happen upon a cave when escaping a blizzard ) the logical, base ten-friendly argument must be discounted and since he had never used a Mac it cannot have been anything to do with Apple's own ideals.

Perhaps the issue is simply that people are not used to seeing major and minor release numbers higher than nine. Maybe if it becomes more common it will stop seeming strange and having to try to devise new major or minor features for an application simply because there cannot be more then ten bug fix releases seems a little too restrictive to me. As someone who has broken this artificial ceiling before I certainly will not have a problem doing so again and I can at least rest easy, backed up by the fact that Apple themselves have done so.

### The Google Insecurity Factor
Both Daniel and Manton reflect on the practice of releasing software using zero-point numbers such as 0.9 and say that this is similar to Google's practice of releasing everything under the beta banner. Zero-point numbers simply results in the impression that you are not confident about your own application which is never a good impression to give potential users. Whilst this sort of version numbering does go on, and it is most commonly seen in the open source community, it is not to be encouraged. If your application is not complete then it is still in beta and probably should not be openly available.

### Alpha, Beta, Developer Builds
Whilst applications are being developed it is quite common to want to flag versions as alpha, beta or even private developer builds. Most developers will use a notation such as 1.1b124 to denote that this is beta build 124 of version 1.1 of the application. This is very useful because is a customer contacts you about a problem it is easy to know if they have somehow ended up with a non-released build.

### One-Dot-Oh-Dot-Oh
Version number with excessive zeros are, both Daniel and Manton believe, very Windows-centric and this is definitely not a Mac developer practice. Releasing your application as version 1.0.0 implies that you know there are bug fixes on the way so you have prepared for them in advance. Be confident and use 1.0, recognise that there are going to be feature additions and do not be pessimistic about there being impending bug fixes.

### Build Numbers and Marketing Version Numbers
In addition to having build numbers associated with alpha, beta and developer versions, developers are starting to include a more global build number with their version numbers, something which Apple themselves began to do with some of their own application with OS X. This number usually appears in parenthesis after the version number, something like '1.0 (36)'. Daniel recognises that this is not ideal in that it is inelegant and can be confusing to users but the benefit is that when someone reports a bug they can give you the actual build number of the application which can help to track the bug down or tell you if the user is running an older version of the application. Whilst this may seem irrelevant if you are determined to release different versions of an application with unique version numbers it is inevitable that at some point there will be a tiny bug which needs to be fixed and rather than having to jump from a version number such as 1.0 or 2.1 to 1.0.1 or 2.1.1 you may prefer to just release a new build of 1.0 or 2.1. Imagine, for example, a situation where you have sent out some review copies of your application a couple of days before your official release and one of the reviewers spots a spelling mistake, would you prefer to rebuild version 1.0 with a new build number or have your first release as 1.0.1 because 1.0 is already out in the wild, albeit with a restricted user-base?

### Summary
1. Do not release your software to the general public with a version number below 1.0. If it is still in beta at least flag it as being a being a 1.0 beta.
2. Until you actually have a bug fix release do not use the minor release number. Use 1.0 or 1.3 rather than 1.0.0 or 1.3.0.
3. Marking versions as being alpha, beta, developer releases, etc. is very useful and allows you to know whether a customer is using a valid release or not.
4. Feel free to include a build number in your version number if it suits the way you work.
5. It is a personal choice as to whether version numbers can be greater than point-nine. Do not feel forced to add new functionality just to avoid a point-ten release.
