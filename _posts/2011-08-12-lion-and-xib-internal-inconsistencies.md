---
date: 2011-08-12 12:12
title: Lion and XIB Internal Inconsistencies
permalink: /posts/2011-08-12-lion-and-xib-internal-inconsistencies
categories: cocoa
layout: post
share: true
---

If you have developed a Mac application and Xcode 4 under Lion starts to complain that a XIB file contains internal inconsistencies then you may need to remove a couple of items from your toolbar.

The alert looks like this:

<img src="http://images.swwritings.com/2011-08-12-lion-and-xib-internal-inconsistencies.png" alt="Lion XIB Internal Inconsistency Warning" />

Apple decided to remove the Customize Toolbar and the Separator toolbar items in Lion. From the Max OS X Lion release Notes:

<blockquote>In 10.7, the Customize Toolbar item and the Separator item (with the vertical dots) have been removed from toolbars and customization palettes, and their item identifiers are ignored.</blockquote>

Deleting these items from your toolbar should solve the problem but if it doesn't then it would be worth committing your XIB in your version control system, taking a snapshot of the project or making a copy of the XIB file and deleting each toolbar item in turn, selecting a different file in Xcode and then re-selecting your XIB until the offending item is found. You can then restore your XIB and just handle the individual problem item.

This has worked for me and a couple of other people but if it doesn't work for you please let me know and, if you find one, the solution, so that I can update this post.
