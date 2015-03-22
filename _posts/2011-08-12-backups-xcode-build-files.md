---
date: 2011-08-12 12:47
title: Backups and Xcode 4 Build Files
permalink: /post/2011-08-12-backups-xcode-build-files
categories: cocoa
layout: post
share: true
---

Xcode 4 recommends storing your build products in a derived data location. This location stores a fair amount of data and can grow pretty large (it's over 4GB for me at the moment) so it may be worth excluding it from some of your backups, particularly versioned ones. I'll continue to keep a copy in the clone that [SuperDuper!](http://www.shirt-pocket.com/SuperDuper) creates but exclude it from Time Machine and [Arq](http://www.haystacksoftware.com/arq/) backups.

The derived data location is:

~/Library/Developer/Xcode/DerivedData
