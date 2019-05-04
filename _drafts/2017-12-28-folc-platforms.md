---
date: 2018-01-01 13:00
title: FOLC - The Platforms
permalink: /post/2018-01-01-folc-platforms
categories: folc
layout: post
share: true
---

For an overview of the FOLC project please see the [Introduction](https://www.swwritings.com/post/2017-12-28-folc-introduction) post. To see all of the posts relating to the FOLC project please see the [article list](https://www.swwritings.com/categories/#folc).

---

## Apple's Ecosystem
The vast majority of my professional work involves developing software for Apple's ecosystem so when I was initially approached about the project my immediate thought was to develop an iOS app and have it run on an iPad which could be securely mounted somewhere in the community space. However the screen size (even on a 12" iPad Pro) isn't ideal and FOLC said that they would prefer the ability to use a large screen, physical keyboard and mouse.

I therefore thought about donating an old Mac and a monitor but decided that developing a Mac application (and the same would have been true for an iOS one) would tie FOLC into costly hardware for an application with modest requirements.

## The Software Platform
Having realised that the application would have to be platform-agnostic (I last wrote any code for Windows in 2008 so .Net wasn't an option either) I focused on browser-based solution. Over the last three years I've written a fair amount of [JavaScript](https://www.javascript.com), both [Node](https://nodejs.org/en/) and the [Meteor framework](https://www.meteor.com). However, I've more recently been playing around with [Python](https://www.python.org) and [Elixir](https://elixir-lang.org) and thought that it might be fun to use one of those.

I'm still very new to Elixir and I don't think that it might be over-kill for a single-user application running locally. It is really more useful for distributed, scalable systems.

Python however seems like a great choice. Whilst it can be used for multi-user, high-demand system it is also well suited for local, less demanding applications. I also have a bit more experience with it which makes me more comfortable picking it right now and, as an added bonus, it can help with another related job which is the cleaning up the parish records which are stored in multiple Excel files.

To help create the actual application I will be using a web application framework and I've chosen [Flask](http://flask.pocoo.org/) . I've played around with Flask before and it is very capable but more light-weight than [Django](https://www.djangoproject.com) which is targeted towards large, complex applications. Fortunately, to get me back up to speed and following the most recent best practices, [Miguel Grinberg](https://twitter.com/#!/miguelgrinberg) has updated his [Flask Mega-Tutorial](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world) and I will be working through it before I start writing the FOLC application.

## The Hardware Platform
As mentioned above, I originally thought about donating an old Mac to FOLC and whilst a Python application will work on a Mac I don't actually have an old machine I can donate to the project right now.

The application is not going to be terribly resource-hungry so the computer it runs on doesn't need to be hugely powerful although it should be reliable. In addition, at some point the computer will need to be replaced (either due to hardware issues or possibly theft) and the cost of doing so should be as low as possible.

I therefore thought about the computer which had encouraged me to start learning Python, the [Raspberry Pi](https://www.raspberrypi.org). The Pi is a small, cheap, single-board computer which will happily run Python and be sufficient for the needs of the application I'll be developing. The Pi Zero W is tiny and might have worked but I'm opting to use a Pi 3 Model B for a few reasons:

* It contains four full-sized USB ports so plugging in a mouse and keyboard is very straightforward.
* It contains an ethernet port so it can be plugged into a router rather than relying on wifi.
* The HDMI post is full-sized so there is no adaptor to lose.
* I've found a [fabulous case](http://www.element14.com/news/premier-farnell-introduces-pi-desktop/) which includes a power switch.

## The Database
The application is going to need a database to hold all of the parish records. Whilst I've been doing quite a lot of work with [MongoDB](https://www.mongodb.com) recently and whilst the more relaxed document-based structure might help with parish records of mixed quality I think that a traditional SQL database will be a better fit.

There are three databases that might be considered using for an application such as the one I will be building. They are [SQLite](http://www.sqlite.org), [mySQL](https://www.mysql.com), and [PostgreSQL](https://www.postgresql.org).

I've discounted SQLite because the application could potentially be used by multiple people concurrently and SQLite is really only safe for single-user use.

PostgreSQL is tempting because I've never really used it but my familiarity with mySQL is leading me towards at least initially using it. I plan to use [SQLAlchemy](http://www.sqlalchemy.org), an Object Relational Mapper, to interface between the database and main application so switching over to PostgreSQL should be achievable if necessary.