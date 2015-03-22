---
date: 2010-05-22 16:06
title: Core Animation - Animator Proxy, CALayers, Layer-Backed and Layer Hosting
permalink: /posts/2010-05-22-core-animation-animator-proxy-calayers-layer-backed-layer-hosting
categories: cocoa
layout: post
share: true
---

### Introduction
One of the aspects of Core Animation that can be confusing is that there are degrees of it. To start with there is the animator proxy which is available to NSWindows and NSViews and allows you to animate some property transitions. Next there is the concept of layer-backed views which allow you to perform additional animation effects to your views whilst still essentially working with the familiar AppKit objects. Finally there are layer-hosting views, a scenario where you essentially use Core Animation layers to create the user interface, simply using an NSView to contain the layers. This article explains each of these in further detail and will hopefully make the distinction between them clearer.

### The Animator Proxy
The Animator proxy is the quickest way to implement Core Animation effects in your user interface. A one word change in your code can evolve a property change from happening immediately into one that is animated and both looks great and also helps the user understand what is going on.

As an example, imagine a scenario where you want to move an image from one side of a view to another. If you simply call something like <code>[imageWell setFrameOrigin:NSMakePoint(150.0, 25.0)]</code> then image will jump straight to the new position. If however you call <code>[[imageWell animator] setFrameOrigin:NSMakePoint(150.0, 25.0)]</code> the image will glide to its new position over a quarter of a second.

The animator proxy can be enhanced by customising the animations that take place in terms of their duration and their timing and this extends to keyframe animations.

Finally, being completely accurate, the animation caused via the animator proxy is termed Cocoa Animation or Cocoa Animatable Proxy Animation. This kind of animation does not require Core Animation layers.

The animator proxy is covered in detail in the [article about it](http://swwritings.com/post/2010-05-23-core-animation-the-animator-proxy).

### CALayers
Core Animation layers, CALayers, are light-weight model classes that store data. This might seem contradictory on the grounds that Core Animation is all about display and moving things and you might expect them to be part of the view in the model-view-controller design pattern but the reality is that the NSView or UIView that the layer is attached to is the object that is the view. CALayers essentially store properties that facilitate the drawing or animation of objects.

The official definition from Apple is:

<blockquote>The CALayer class is the model class for layer-tree objects. It encapsulates the position, size, and transform of a layer, which defines its coordinate system. It also encapsulates the duration and pacing of a layer and its animations by adopting the CAMediaTiming protocol, which defines a layer’s time space.</blockquote>

You can think of CALayers as being a kind of skin that either sits behind or on top of a view. If it sits behind the view its main purpose is to allow the view to perform additional drawing and animation effects upon the contents of the view and its subviews. If it sits on top of the view it effectively conceals it and the drawing and animation is done on the layer rather than on the view itself.

Core Animation layers are not needed in order to use the animator proxy although if your views are layer-backed then the performance of the animations can be improved.

### Layer-Backed Views
Layer backed views are views where their Core Animation layers, and the Core Animation layers of their subviews, are, for want of a better phrase, turned on. Enabling a view's backing layer allows you to perform some animations and effects that are beyond the animator proxy, things like rotation, shadows and transparency.

You can turn on layer-backing for a view in Interface Builder or in code using the view's setWantsLayer: method.

You would use layer-backed views when you want to enhance your 'traditional' interface beyond what the animator proxy offers.

### Layer-Hosting Views
A layer-hosting is when you place a layer over the top of a view. You can resize and reposition the view but all of the drawing should be done on the layer or layers that are being hosted by the view.

Layer-hosting views generally come into play when you want to provide a bespoke interface or interface element which is created from one or more Core Animation layers. A good example of this is Apple's Front Row application.

One of the important features of a Core Animation layer is that it is not a subclass of NSResponder and therefore they cannot process events. To work around this you catch the events in the underlying view and use the layer's hitTest: method to determine which layer was clicked and react accordingly.

Layer-hosting is set up by creating a Core Animation layer, assigning it to a view and then calling the setWantsLayer: method on the view.

You would use layer-hosting views when you want a highly customised, animated interface or when what you are trying to achieve simply suits Core Animation layers more than it does NSViews or UIViews. For example, if you want to be able to display a video and allow the user to flip it horizontally and/or vertically it's a snap doing it in a QTMovieLayer rather than in a QTMovieView.

### Conclusion
All three of these types of Core Animation will be covered in more detail in their own posts. I'll tag posts with 'animator proxy', 'layer-backed' or 'layer-hosting' as and when appropriate to help you find related articles more easily.
