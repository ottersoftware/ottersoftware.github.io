---
date: 2013-04-15 10:00
title: Searching for Speedy Searching - The Prelude
categories: cocoa
layout: post
share: true
---

At [NSConference 5](http://nsconference.com) I presented a talk entitled 'Searching for Speedy Searching'. The talk was about how you can use SQLite's [Full Text Search](http://www.sqlite.org/fts3.html) (FTS) to add fast text searching to your Cocoa applications quickly and easily. During the talk I said that I'd be publishing a blog post going into some of the details I brushed over. This isn't it. This is a pre-post post to correct a couple of things I said in the talk and to let you know where you can get the video and slides from.

The video of my talk is available as part of the $149 pack from the [NSConference web site](http://nsconference.com). The conference videos are fantastic and well worth getting and the talks were all excellent although [some might be more excellent than others](https://alpha.app.net/acf/post/4743505).

I'm also making the slides and notes available [as a zipped up PDF document](http://images.swwritings.com/SearchingForSpeedySearchingSlides.zip) (22.7 MB).

In the talk I said a few things that I would now like to correct:

1. It appears that the version of SQLite that ships with iOS 6 and OS X 10.8 actually have FTS enabled, are compiled to be threadsafe and have the more advanced searching enabled by default. Therefore it is not necessary to include your own custom build of SQLite in your application.
2. Some processes such as a database migration can cause managed object identifiers change so you should either use your own unique IDs to relate your data to the FTS database or regenerate your FTS data after a migration.
3. Apparently there isn't actually a conference called WWCD. I've now added a comment at the end of to my [Speaking at NSConference](http://swwritings.com/post/2013-02-24-speaking-at-nsconference) blog post about having someone else proof read your slides.

The main blog post with an example application will be published in the next day or two.
