---
date: 2013-11-14 14:31
title: Setting Up Xcode Bots
permalink: 2013-11-14-setting-up-xcode-bots
categories: cocoa
layout: post
---

For some time I've been wanting to set up a Continuous Integration server but, as a solo developer, I've never had the overwhelming need to do so. At least not to make me jump through all the hoops necessary to set up a Jenkins server. However when Apple announced Xcode Bots at WWDC 2013 it became a feature I really wanted to try. Unfortunately during the Mavericks beta period I kept hearing tales of anguish and woe so I avoided it all until yesterday.

Setting up Mavericks server is really quite easy and if you are a registered developer you can [get it for free](http://appleinsider.com/articles/13/10/24/apple-offers-developers-free-copies-of-os-x-server-for-mavericks-in-bid-to-keep-apps-fresh). Apple's [Xocde Continuous Integration Guide](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/xcode_guide-continuous_integration/000-About_Continuous_Integration/about_continuous_integration.html#//apple_ref/doc/uid/TP40013292-CH1-SW1) is clear and simple and covers the basics. Note that the link may well break since Apple love to shuffle their documentation files around so do a search for the title and you're sure to find it.

The other source I found really useful was Daniel Kennet's [Xcode Bots: Common Problems and Workarounds](http://ikennd.ac/blog/2013/10/xcode-bots-common-problems-and-workarounds/) blog post. I opted to use HTTPS to access my remote repositories but the tips on code signing and Provisioning Profiles were invaluable.

One extra thing I will chuck into the mix was that even after moving my certificates and private keys into the System keychain I still got an error: '/usr/bin/codesign failed with exit code 1'. The solution for me lay in the Access Control settings of the private keys in Keychain Access. I needed to give Xcode access to it.

Finally, once things were up and running I had one other issue which I thought was going to be a deal-breaker. Every time a Bot ran it would do a full clone of the repository. For larger projects this was really slow. Fortunately [Daniel Tull](https://twitter.com/danielctull) figured out that if you don't have 'Clean before each integration' enabled then only the changes are pulled down to the server.
