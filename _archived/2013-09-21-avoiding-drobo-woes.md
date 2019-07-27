---
date: 2013-09-21 12:03
title: Avoiding Drobo Woes
permalink: /post/2013-09-21-avoiding-drobo-woes
categories: technology
layout: post
share: true
---

***Updated:** Please see an update at the end of this post about how it all turned out.*

I've had a Drobo for around four years now and until yesterday I've never experienced any hardware failure or data loss issues. That changed yesterday afternoon and I'm sorry to say that it is something I could have avoided.

On Tuesday my Drobo warned me that it was running out of space and that I should replace a drive soon. On Wednesday I did just that, replacing a 1TB drive with a 2TB one. It then started the process of shuffling the data around across the four drives, estimating that it would take around 48 hours. Yesterday lunchtime, about 40 hours into the process one of the other drives failed meaning that my data is now effectively lost. I've looked into a data recovery service (the immensely helpful [Retrodata](http://www.retrodata.co.uk)) but the data on the Drobo isn't worth the £1,000s of pounds it might cost me to recover it.

I've now found out that this sort of data protection process on any RAID system is a potential danger point since a lot of data is being moved around so any drive which is in a fragile state is likely to be pushed over the edge into total failure.

If you have a Drobo S, DroboPro or DroboElite you can enabled Dual Disk Redundancy whereby losing two drives simultaneously is now something a Drobo can recover from. If you're interested, Drobo have a [knowledgebase article](http://support.drobo.com/app/answers/detail/a_id/6/kw/two%20drives/session/L3RpbWUvMTM3OTc2MTkwMi9zaWQvLUNEWV9TQWw%3D) about it.

As with most things in life there is good news and bad news. The bad news is that I'd not done a backup of my Drobo for a long, long time. To rub salt into the wound I ordered a 4TB hard drive on Friday morning so that I could do a full backup of my Drobo once the data rearranging process had finished.

The good news is that my Drobo was used for two main types of data. The first type is large file downloads, things such as versions of Xcode, betas of operating systems, etc. This is all replaceable. The second was my iTunes media library. This has been built up over four years and is mainly a collection of DVDs and CDs I own but have created digital versions of (and can do so again). There were quite a few things I'd recorded using an EyeTV which I can't easily replace unless they are re-broadcast again but none of it is vital.

However, before I start rebuilding my media library I am going to try cloning the failed drive to see if the Drobo will accept it and carry on with the data protection process. It's an extremely long-shot but worth a go since I've got a new 4TB drive arriving on Monday morning.

So, the take-away advice for the future is to have a solid backup strategy for my Drobo (or any other RAID box). On my Macs I backup to a Time Capsule, to an off-site server (using [Arq](http://www.haystacksoftware.com/arq/) and Amazon) and clone my drives to external hard disks regularly. I've been complaisant about my Drobo.

I'm going to now do regular clones of the data on the Drobo and when I reach the point where a single drive can't store all of the data I'll spread it over two or more drives. I'll also keep the drives off-site, probably at a friend's house. This protects me if there was ever a flood or fire.

I'll also be sure to make a backup before I replace a drive to increase capacity. If you're replacing a failed drive you're a bit stuck but replacing a good drive with a larger capacity one is certainly a scenario where you can snapshot your data before you enter a period when your data is vulnerable.

I've learnt my lesson and fortunately nothing critical has been lost but, if applicable, please learn from my mistake too.

---

**Update**

I came across a [blog post](http://www.prosofteng.com/blog/cloning-a-drive-from-a-drobo-array/) by [Prosoft Engineering](http://www.prosofteng.com) where they said that they had successfully advised someone who had a Drobo with an unmountable drive to clone it using their [Data Rescue 3](http://www.prosofteng.com/products/data_rescue.php) tool. With nothing to lose I decided to give it a go myself.

Three days ago I started the clone and it finished a little while ago. During the process over a thousand slow drive read warnings were generated and around 100MB of data was unreadable. I performed a [slot flush](http://support.drobo.com/app/answers/detail/a_id/665/kw/) on the Drobo, put newly cloned drive in with the old drives and turned it on.

'Data protection is in progress... 7 minutes remaining...'

Seven minutes later, and almost exactly a week after I swapped in a new drive, the process was complete and my Drobo presented four beautiful green lights.

My next job... back my Drobo up properly.
