---
date: 2012-08-23 20:43
title: Scriptogr.am
permalink: /post/2012-08-23-scriptogram
categories: technology
layout: post
share: true
---

Two days ago I decided to revamp my blogging methodology. You can read a little about it in my [A New Home](http://scriptogr.am/swwritings/post/2012-08-21-a-new-home) post. Since I started using [Scriptogr.am](http://scriptogr.am) several people have asked me about the experience and I thought I'd both write about it and a bit about my workflow. The latter will be evolving over the coming weeks I imagine however but it might give you some ideas and obviously if you have any tips or tricks you want to share let me know. My contact details are at the end of this (and every other) post.

<a id="contents"></a>To make this post a bit clearer I'll split it into a number of sections:

1. [Markdown & Markdown Editors](#markdown)
2. [Images](#images)
3. [Boilerplate Text](#boilerplate_text)
4. [Pages](#pages)
5. [RSS Feeds](#rss_feeds)
6. [Tags](#tags)
7. [Themes](#themes)
8. [Comments and Footers](#comments_footers)
9. [Concerns](#concerns)
10. [Conclusion](#conclusion)


##<a id="markdown"></a>Markdown & Markdown Editors
For many months now I've been using [Markdown](http://daringfireball.net/projects/markdown/) more and more. I'm a huge fan of the simplicity and clarity of it. Although you can write Markdown in any text editor, since I started using it I've tried out a lot of Markdown-focused or Markdown-friendly editors. Some offer toolbars to help you add Markdown elements to your documents but they all allow you to preview your Markdown document as it might appear properly displayed.

My current favourite is [Byword](http://bywordapp.com) for both iOS and OS X but I don't think it is perfect for editing blog posts on the go because Byword's iOS Dropbox support limits you by folder. Therefore I think I'll stick to using Byword's iCloud synchronisation between my Macs and iOS devices and use a separate editor for iOS-based blog posting.

[Textastic](http://www.textasticapp.com) is my current choice for editing Markdown files on my iPad because it gives me complete freedom of choice about where to save files within my Dropbox folders.

However these choices may well change over the next few months as I play around with new apps and who knows, maybe I'll use the fledgling Scriptogr.am API and write a native iOS client one day.

[Back to the contents list](#contents)

##<a id="images"></a>Images

**Updated: See [Scriptogram Image Handling - An Update](https://swwritings.com/post/2012-09-07-scriptogram-image-update)**

Adding images to blog posts is, in theory, very simple. Markdown supports linking to images so that aspect is no problem at all. Rather the problem is with image hosting.

There has been [some discussion](http://support.scriptogr.am/discussions/questions/4-how-can-display-an-image-from-my-dropbox) about this in the [Scriptogr'am support forum](http://support.scriptogr.am) and, as a result, I decided that I wanted to be able to host the images somewhere fairly robust. Initially I tried using [Flickr](http://www.flickr.com) but getting the actual image URL was more complex that I hoped it would be.

I'm now trying [SmugMug](http://www.smugmug.com) which also has the advantage of being a paid service. I'll expand on that comment in the [Concerns](#concerns) section. However it is really simple to upload and manage photos and, more importantly, obtaining links to the photos is trivial. We'll see how things go.

Finally, I keep the original images in the same Dropbox folder as the posts and they share the same name as the post's Markdown file. This means that all of the content for my blog, words and pictures, are all in one convenient place.

[Back to the contents list](#contents)

##<a id="boilerplate_text"></a>Boilerplate Text
One thing that is proving to be really useful is [TextExpander](http://smilesoftware.com/TextExpander/). There are various 'shortcut expansion' applications but I've been using TextExpander for years now and am a huge fan. They have a Dropbox sync feature so that all my Macs and iOS devices can share all of the shortcuts I define.

I use TextExpander to generate boilerplate text my blog posts. The first thing I do is add the basic 'structure' to a new Markdown file:

<img src="https://www.swwritings.com/images/2012-08-23-scriptogram-01.png" alt="The basic blog structure" width="540" />

[<a href="https://www.swwritings.com/images/2012-08-23-scriptogram-01.png" target="_blank">Full Sized Version</a>]

The last item actually embeds another snippet into this one:

<img src="https://www.swwritings.com/images/2012-08-23-scriptogram-02.png" alt="The basic blog structure" width="540" />

[<a href="https://www.swwritings.com/images/2012-08-23-scriptogram-02.png" target="_blank">Full Sized Version</a>]

Finally, I also have a snippet that adds the basic Markdown for linking to an image:

<img src="https://www.swwritings.com/images/2012-08-23-scriptogram-03.png" alt="The basic blog structure" width="540" />

[<a href="https://www.swwritings.com/images/2012-08-23-scriptogram-03" target="_blank">Full Sized Version</a>]

I'll undoubtedly be adding to this initially short list but just being able to add chunks of text to each post via a few keystrokes is a fantastic time-saver.

[Back to the contents list](#contents)

##<a id="pages"></a>Pages
Scriptogr.am supports pages as well as posts. Depending on the template you use, these generally appear at the top of your blog. I think that all of the themes include an Archive page which simply lists all of the posts in your blog. I've so far added three new pages.

I always like blogs to have an About page of some sort so that you can easily find out a bit about the blog author, where else you might find them on the web, how to contact them, etc. so this one was a no-brainer for me.

I also added [Tags](#tags) and [RSS Feed](rss_feeds) pages which are covered in their own sections below.

[Back to the contents list](#contents)

##<a id="rss_feeds"></a>RSS Feeds
Scriptogr.am supports RSS feeds, even if most themes hide the fact. There is [a handy support post](http://support.scriptogr.am/discussions/suggestions/85-rss-feed) that gives you the relevant information.

However, since one of my goals was portability I'm actually using [FeedBurner](http://feedburner.google.com) which effectively pipes your RSS feed through their servers and gives you an RSS URL you publish on your blog. This means that if you move your blog you only need to update the URL that FeedBurner points to and existing subscribers, who are actually using a FeedBurner URL, don't have to change anything.

To add an RSS link to my blog I created a [page](#pages) and in that I store the FeedBurner URL in the 'Link' header item. This means that clicking on the 'RSS Feed' page link in my blog's header doesn't display the page but rather links to the specified URL.

[Back to the contents list](#contents)

##<a id="tags"></a>Tags
Scriptogr.am doesn't support categories but it does support tags. I therefore decided to split my blog posts into a limited number of tags and effectively use them as categories. That means that someone who is interested in my cycling posts can simply look at posts tagged as cycling and avoid the rest.

At the moment I have defined six tags which currently cover most of the areas I write blog posts about and I list them all on my Tags page and provide a link to the list of blog posts for each tag.

I'll probably not always be brilliant at accurately tagging posts and occasionally some posts will fit into more than one category. I will probably also occasionally mis-tag something but until categories or, more likely, some sort of automatically generated tags index is added to the system it's the best I can do.

[Back to the contents list](#contents)

##<a id="themes"></a>Themes
Scriptogr.am supports a limited number of themes and they are encouraging people to submit more so the library will grow.

On the plus-side the themes seem fairly easy to customise and I will be doing some digging into that and seeing what I can come up with.

More on all of that soon.

[Back to the contents list](#contents)

##<a id="comments_footers"></a>Comments and Footers
Scriptogr.am doesn't support comments which is a good thing as far as I am concerned. My old [SquareSpace](http://www.squarespace.com) blog attracts way more spam than actual comments so I'm not concerned that I'm losing out on anything. In fact, bloggers such as [Matt Gemmell](http://www.mattgemmell.com) are positively [anti-comments](http://mattgemmell.com/2011/11/29/comments-off/).

One other thing I did borrow from Matt's blog is a sign-off at the end of my posts. I use a [TextExpander snippet](#boilerplate_text) to make this a trivial matter but but gives readers links to me on Twitter, App.net and also tells them how to email me.

[Back to the contents list](#contents)

##<a id="concerns"></a>Concerns
So having waxed lyrical about Scriptogr.am do I have any issues or concerns about it? Only two really.

I do worry that the service will just go away or that performance and reliability will become an issue. I'm a great believer in paying for services so that they are funded properly (hence [my preference for SmugMug](#images) over a free image hosting service) and don't become swamped by annoying adverts, become slow and unreliable because they cannot afford the necessary servers or just vanish because the backers have run out of funds. Obviously this can happen to paid services but it is less likely. An obvious business model is more appealing to me than a hidden one. However one of my aims was to give me back control over my blog and since all of the posts are in Markdown files and all of the images are stored with them this is not a deal-breaker, obviously.

My other concern is whether the people behind Scriptogr.am are prepared to handle high volume blogs. I would guess that most of their users are running small ones like mine but if a popular blogger took to the platform or a blog post got linked to by a site such as [Daring Fireball](http://daringfireball.net) then I wonder if their servers will go into melt-down or handle it all graceful.

[Back to the contents list](#contents)

##<a id="conclusion"></a>Conclusion
A few days ago I decided to take more control over the contents of my blog and I have managed that. I'm very impressed and very happy with Scriptogr.am so far. The service has allowed me to achieve my aims quickly and easily without having to set up additional software on my web servers or go to any real effort.

The people behind Scriptogr.am are not providing a unique service in using Dropbox and Markdown for blog hosting but, after looking at a couple of alternative, they seem to be the best at the moment but this is obviously a concept that is only really starting out and hopefully has a bright future.

I'll add new posts as I discover new things and adapt my workflow but hopefully it will have perhaps tempted you to take a look at a service like this.

[Back to the contents list](#contents)
