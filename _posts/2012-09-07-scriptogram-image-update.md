---
date: 2012-09-07 12:44
title: Scriptogram Image Handling - An Update
permalink: /posts/2012-09-07-scriptogram-image-update
categories: technology
layout: post
share: true
---

A couple of weeks ago I wrote about [my Scriptogram workflow](http://swwritings.com/post/2012-08-23-scriptogram). One of the areas I was still refining was how to handle images and after some further tinkering I'm using a slightly different approach so I though it would be worth posting a quick update about it.

Originally I tried using [Flickr](http://www.flickr.com) and then [SmugMug](http://www.smugmug.com) as image hosting solutions but both involved a fair amount of tinkering with web-based interfaces to upload and then retrieve URLs for images.

Today I decided to try using my Amazon S3 account. I created a new bucket to store the blog-related images so that I have some separation between them and other files I store on S3. I then turned to the excellent [Transmit](http://www.panic.com/transmit/) to make uploading images nice and easy.

The first thing I did was create a new favourite in Transmit for my new image-hosting folder. I could obviously navigate to it via my existing S3 favourite but having a dedicated one allows me to create a droplet. These are small applications that you can drag and drop file on which then upload the files to whatever the favourite is, well, favouriting. So now I can simply add the droplet to my Dock and drag and drop images I want to use on my blog onto it.

However, because Amazon S3 sets file permissions so that only the file's owner can read and write them I also created a rule in Transmit's preferences which makes any files with a jpg or png file extension world readable when it is uploaded. This isn't 100% perfect and I'd really like to be able to define this sort of rule on a per-favourite basis but it's not a critical issue for me.

I also defined a Skip Files rule whereby, if I synchronise a local folder to a remote one and apply the rule then any files with the 'md' extension are ignored. This allows me to sync my main Scriptogram posts folder in Dropbox with the images folder on S3 and only sync image files and ignore the markdown files.

<img src="http://images.swwritings.com/2012-09-07-scriptogram-image-update-01.png" alt="The Transmit permissions and sync rules" width="540" />

[<a href="http://images.swwritings.com/2012-09-07-scriptogram-image-update-01.png" target="_blank">Full Sized Version</a>]

[Mathew Waters](http://twitter.com/mathew_waters) [then suggested](https://twitter.com/mathew_waters/statuses/244059758028529664) that I should take a look at [CloudFront](http://aws.amazon.com/cloudfront/) which is an Amazon service that essentially distributes static files such as images across servers around the world so that they download faster. It hooks in to their S3 service and I was able to hook it in to my swwritings.com domain name so the images now appear to come from images.swwritings.com which makes everything nice and neat and fast.

A final step was to create a new [TextExpander](http://smilesoftware.com/TextExpander/) shortcut which will will generate the core HTML I need to display images in my blog.

<img src="http://images.swwritings.com/2012-09-07-scriptogram-image-update-02.png" alt="Textexpander Image Shortcut" width="540" />

[<a href="http://images.swwritings.com/2012-09-07-scriptogram-image-update-02.png" target="_blank">Full Sized Version</a>]

Because I want to have the browser resize images to fit the blog's width I'm using HTML rather than markdown's own image linking syntax. However, because I am also linking to full-sized images I can also easily provide a link to that version which then opens in a new window or tab.

I'd like to streamline things further by creating a service so that I can right-click on image files in Finder and have them uploaded and have the complete HTML needed to link to them generated.. However the new approach is faster to use than either of the the Flickr or SmugMug ones so even if I don't get around to doing that I've still got a fast, usable solution to hosting images.

---

*April 2014 Update*

John Henney contacted me after reading this post to ask why I'd not used Dropbox itself for image hosting. I actually don't have a reason and see no reason why this wouldn't work and is probably a much simpler solution too.

*May 2014 Update*

Dropbox have just [disabled publicly shared links](http://www.bbc.co.uk/news/technology-27285786) (which is what images hosted on it would have to use) so maybe John's suggestion should be avoided. This was totally unforeseen but does highlight that services such as Dropbox do have vulnerabilities and points of failure.
