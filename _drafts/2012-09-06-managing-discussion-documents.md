---
date: 2012-09-06 10:08
title: Managing Discussion Documents
permalink: 2012-09-06-managing-discussion-documents
categories: business
layout: post
---

One of my clients, [Don McAllister](http://twitter.com/donmcallister), is tech savvy. Not just tech savvy in that he knows how to use a computer but tech savvy in that he can do some of the stuff developers do. He can use [GitHub](https://github.com) and can build code in Xcode allowing him to test things and submit apps to Apple. Today he became a guinea pig after inadvertently proposing an idea to me. I'd given him a first draft of an application outline document as a Markdown file. Don then replied and used [blockquotes](http://daringfireball.net/projects/markdown/syntax#blockquote) in the document to denote his comments. This was enough to trigger an idea about how we might be able to track our discussions.

{{more}}

Part of my job as a software developer involves discussing projects with clients. General discussion and discussions about specific issues can be done on the phone or via email, Skype or other services such as instant messaging. However, when you are hammering out the details of a lengthy document, something like a specification, things can quickly become very complicated. Some clients like to use Microsoft Word and its tracking features, some like to feed comments back and have me integrate them into the document and sometimes we just get into a horrible muddle. Today I started a new approach (well new for me... I don't think it's unknown) which I'll describe below.

Don's use of blockquotes prompted me to reply in kind. I actually went back and added his initials to the start of each of his comments to gives everything additional clarity and the end result was something like this (the below is an example, not actual content from our discussion) :

---

* Show general information about the app.

>DM: Can we have a link to the SCO web site?

>>SW: Sure, that's easy. Do you want to throw the user out to Safari or show the site in a web view? We can give them the option to open the site in Safari from that which means they can bookmark it, etc.

---

This is good in itself because it allows for some in-line conversation and make it clear who said what and in reply to what. Once details are agreed I'll clean up the document to remove the comments:

---

* Show general information about the app.
* Add a button to show a new view which displays the SCO web site and a toolbar with a button so that they can open the site in Safari itself.

---

However we're still left with a document being passed back and forth via email or in a shared Dropbox folder. The latter is certainly better than the former and is something I've done in the past but then it occurred to me that since the project's code would be in GitHub and since Don knows how to use GitHub, or at least their Mac app, then we could keep the document in GitHub too.

The whole approach has four big advantages:

1. If we both edit it and then commit our edits we have a good change that our various changes can all be merged together without too much manual intervention.
2. GitHub allows us to look at the changes between versions of the document so it is really easy to see what has changed between versions of the document. 
3. We get to keep a full history of the document. If I delete a chunk of it and we then decide at some point in the future that actually it was useful it can be retrieved and reinstated.
4. We're platform and software independent. Markdown only requires a text editor and not some proprietary word processor and GitHub work with OS X, Windows and (I assume) Linux.

There are, however, some down-sides to this approach:

1. It relies on the client having some knowledge of Markdown. They may not be willing to sacrifice the comfort zone of Word and embrace something new.
2. It relies on the client being able to use some form of version control system and they're really not designed to be simple enough for everyone.
3. If you use a service like GitHub you could potentially end up with a lot of repositories just for document management which could get very expensive very quickly.

This is by no means an ideal solution but it is one I'm going to try with a few of my more technically-capable clients and it will be interesting to see how it goes. If you do something similar please get in touch and tell me about it and I may do some follow-up blog posts.

---

I’m [@sgaw on Twitter](http://twitter.com/sgaw), [@sw on App.net](https://alpha.app.net/sw) and you can also email me at simon at this domain.