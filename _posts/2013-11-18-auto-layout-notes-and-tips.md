---
date: 2013-11-18 13:28
title: Auto Layout Notes & Tips
permalink: /posts/2013-11-18-auto-layout-notes-and-tips
categories: cocoa
layout: post
share: true
---

For me the Auto Layout improvements in Xcode 5's Interface Builder made it a usable replacement to the old springs and struts mechanism. Over the last few weeks I've been learning a lot about Auto Layout so I thought it would be a good idea to pass on some of the tricks I've picked up but mainly I wanted to document some of the bits I struggle to remember.

I'll add to this list as my Auto Layout skills improve.

### Free Video Guide
Watch [Scotty's](https://twitter.com/macdevnet) [Auto Layout video](http://ideveloper.co/autolayout-in-ios-6/) (also available on [Vimeo](http://vimeo.com/77600524))which he recorded with [Matt Tancock](https://twitter.com/mtancock). It gives you a good overview and is particularly useful when you get to setting up constraints in code.

### Finding Constraints
Sometimes you want to find a constraint so that you can edit it. The easiest way is to select the relevant object in Interface Builder and then switch to the Size Inspector which lists all of the constraints associated with that object. If you want to have an actual constraint in the Document Outline selected so that you can, for example, associate it with an NSLayoutConstraint property then simply select the constraint in the Size Inspector and then choose 'Select and Edit...' from the popup menu.

### Constraints Look Wrong
If the constraints listed in the Size Inspector look wrong (you see duplicates or the details are unclear) then simply adjust the width of the Utilities pane which will cause the constraint details to re-draw and sort themselves out.

### Manipulating Constraints
If you want to change a constraint you set up in Interface Builder then you can safely change the constraint's constant value but nothing else. Assuming you have created an NSLayoutConstraint property and associated it with the constraint in Interface Builder then you can just set a new float value for the constraint's constant property.

### Animating Constraint Changes
To animate a constraint change you simply need to set the new value of the constraint and then invoke the layoutIfNeeded method of the superview within an animation block:

    [[self buttonWidthConstraint] setConstant:100.0f];
    [UIView animateWithDuration:0.4 animations:^(void) {
        [[self view] layoutIfNeeded];
    }

### Before Adding Constrains In Code
Before you add constraints to an object in code be sure you've added the object to a parent view first. This is remarkably easy to forget if you are creating the object in code too.

### translatesAutoresizingMaskIntoConstraints
If you are creating the object in code then be sure to set the translatesAutoresizingMaskIntoConstraints property to NO.
