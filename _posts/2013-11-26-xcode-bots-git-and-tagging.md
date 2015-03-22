---
date: 2013-11-26 12:34
title: Xcode Bots, Git and Tagging
permalink: 2013-11-26-xcode-bots-git-and-tagging
categories: cocoa
layout: post
share: true
---

Following on from [my last post](http://swwritings.com/post/2013-11-25-xcode-bots-build-numbers-and-git) about incrementing build numbers when using Xcode's Bots and pushing the changes to your Git repository, this post contains a small script to tag your build.

Please refer to [my previous post](http://swwritings.com/post/2013-11-25-xcode-bots-build-numbers-and-git) for details about where this script lives and some notes and when you might want to run it. Note that the *plist* and *$buildnum* values are set in that earlier script.

    echo "Tagging the Archive..."
    versionnum=$(/usr/libexec/PlistBuddy -c "Print CFBundleShortVersionString" "${plist}")
    tagnumber="$versionnum-$buildnum"
    descriptiveversionnumber="$versionnum ($buildnum)"
    cd ${PROJECT_DIR};git tag -a "$tagnumber" -m "Version $descriptiveversionnumber."
    cd ${PROJECT_DIR};git push origin $tagnumber
    echo "Tag created and pushed $descriptiveversionnumber."

The gist of the script is that it obtains the short bundle version string which I use for the version number and the bundle version which I use for the build number. The script then creates a tag number which conforms to Git's rules for tag numbering and a more descriptive version number. Git's tag command is then used to tag the build and the new tag is pushed up to the source repository.
