---
date: 2013-11-25 16:40
title: Xcode Bots, Build Numbers & Git
permalink: /post/2013-11-25-xcode-bots-build-numbers-and-git
categories: cocoa
layout: post
share: true
---

Following up on [my recent post](https://swwritings.com/post/2013-11-14-setting-up-xcode-bots) about Xcode Bots I wanted write up some brief notes about how I have configured a project so that an archive build Bot bumps the project's build number and then commits that back to the Git repository. Normally I would do things like this using a Run Script build phase but here's a slightly different approach using Xcode's Schemes.

If you edit your project's scheme you will see that you can add pre-action and post-action scripts:

<img src="https://www.swwritings.com/images/2013-11-25-xcode-bots-build-numbers-and-git-01.png" alt="Xcode Schemes" width="540" />

[<a href="https://www.swwritings.com/images/2013-11-25-xcode-bots-build-numbers-and-git-01.png" target="_blank">Full Sized Version</a>]

*[This is a client's project so some parts of the image have had to be obscured]*

However, as you can see, I've create a script which runs before the project is archived and in the script the bundle version number, which is the project's build number, is incremented and then the changes to the Info.plist file are committed and pushed to the remote, source repository.

The code is reproduced below so you can copy and paste it:

    echo "Bumping build number..."
    plist=${PROJECT_DIR}/${INFOPLIST_FILE}
    
    # increment the build number (ie 115 to 116)
    buildnum=$(/usr/libexec/PlistBuddy -c "Print CFBundleVersion" "${plist}")
    if [[ "${buildnum}" == "" ]]; then
    echo "No build number in $plist"
    exit 2
    fi
    
    buildnum=$(expr $buildnum + 1)
    /usr/libexec/Plistbuddy -c "Set CFBundleVersion $buildnum" "${plist}"
    echo "Bumped build number to $buildnum"
    
    echo "Committing the build number..."
    cd ${PROJECT_DIR};git add [SOME PATH]/Info.plist
    cd ${PROJECT_DIR};git commit -m "Bumped the build number."
    cd ${PROJECT_DIR};git push -u origin master
    echo "Build number committed."

Obviously replace [SOME PATH] with something valid or simply do 'git add .' to commit all changes.

This approach will work for this particular client where they test builds and, when they are happy with one, release it to the end users. For other projects I may have to amend this slightly (which is one of the reasons I also wanted to document it!).

Possibly the only other thing that needs sorting out is Git user which pushes the new version number. My build server does it from a user called 'TeamServer' which is fine for me and it shows me that it was an automated commit. You might want to dig into it all a bit further.

---

A big thanks to [Made By Many](http://madebymany.com) for [their blog post about CI for iOS Development](http://madebymany.com/blog/continuous-integration-for-ios-development) which pointed me down the schema path.
