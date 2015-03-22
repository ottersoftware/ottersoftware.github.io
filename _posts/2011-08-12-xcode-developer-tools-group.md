---
date: 2011-08-12 12:42
title: Xcode and the Developer Tools Group
categories: cocoa
layout: post
share: true
---

If the account you use for development is not an administrative account you will be asked to enter the name and password for a user who is in the Developer Tools group once per login. To get around this you can add your account's username to the *_developer* group.

1. To make life simple log in to an administrative account.
2. Launch Terminal.
3. Type: *sudo dscl . append /Groups/_developer GroupMembership **&lt;username&gt;***.
4. Quit Terminal and log out of the administrative account.
