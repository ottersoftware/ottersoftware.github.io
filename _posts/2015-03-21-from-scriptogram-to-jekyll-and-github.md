---
date: 2015-03-21 18:09
title: From Scriptogram to Jekyll and GitHub
permalink: 2015-03-21-from-scriptogram-to-jekyll-and-github
categories: general
layout: post
share: true
---

Since August 2012 I've been hosting my blog on [Scriptgr.am](http://scriptogr.am), a service which pulls Markdown files out of a specific Dropbox folder and publishes them. Sadly the Scriptgr.am service seems to have been abandoned by its creators and the service is now suffering from database errors and poor performance. I'm sad about this because it was a fabulously simple system to use and I think that it could have been made into a comercially viable service (they never charged for it).

Over the last few days I've been looking for an alternative service and I've settled on [Jekyll](http://jekyllrb.com) for the content generation and [GitHub Pages](https://pages.github.com) for the hosting.

This setup essentially involves me tweaking my old Scriptogr.am Markdown files slightly and keeping them into a folder which is a GitHub repository rather than a Dropbox folder (although they could conceivably be the same it's unnecessary and I remember stories of repositories becoming corrupt if they were in Dropbox). When I add a post or change an old one I simply need to commit it and GitHub re-publishes the blog. Really it's very similar to what Scriptogr.am was doing with Dropbox but a bit less user-friendly.

Over the next few days I will be migrating my old blog files and tweaking the layout so please bear with me if things are a bit odd for a little while.