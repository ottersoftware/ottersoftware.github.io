---
date: 2011-11-03 12:10
title: AVFoundation Overview
permalink: 2011-11-03-avfoundation-overview
categories: cocoa
layout: post
---

###Introduction

A couple of weeks ago I was on episode 32 of the <a href="http://www.ideveloper.tv/podcast/ideveloperlive.html">iDeveloperLive podcast</a> speaking about AVFoundation. The actual conversation veered way from the nodes I'd made in advance and from what I had intended to cover so I thought it was worth publishing the notes which you can find below.

{{more}}

###Overview

AVFoundation started life on iOS back in iOS 2.2 and was an Objective-C wrapper for audio playback. One class: AVAudioPlayer.

iOS 3 added audio recording via two new classes: AVAudioRecorder and AVAudioSession.

iOS 4 audio and video capture, editing, playback and export spread over 57 classes.

iOS 5 refines this but the big change this year was that AVFoundation came to Lion.

AVFoundation appears to be one of the technologies that was developed for iOS and then made the jump to OS X. QTKit and the QuickTime framework never existed on iOS and fortunately, rather than keeping things separated Apple decided to bring some compatibility to the two platforms and  make AVFoundation common to both.

At WWDC 2011 Apple said that this is what you should be using from here on. QTKit is effectively dead.

###What's Better Than QTKit

AVFoundation sits above CoreAudio, CoreMedia and CoreAnimation but it is below UIKit and AppKit.

This makes it easy to use but low-level enough that you can do things like specify that you want to inspect some media rather than prepare it for playback which is a faster process.

It is 64-bit native, it includes hardware acceleration as standard which includes hardware decoding and it is all GPU-backed.

Takes into account that media these days can be stored remotely and streamed or that media files can be many gigabytes in size. It doesn't shy away from this, rather it forces you to accept this. It all works asynchronously. However asynchronous behaviour can be split into two models. The first is where, for example, you are querying the duration property of a movie. The request is sent off and you wait for the answer to come back. The return value will not change for that movie. The second is where you want to do something like track the movie's playback rate. This can change after you have retrieved the value so you use key-value observing to monitor it.

###What Can You Use It For

* Media Inspection
* Playback and Presentation
* Media Composition / Editing
* Export
* Capture

###What Are You Playing Back or Inspecting?

AVAsset represents the media resource, essentially video and audio, which is arranged into tracks, each represented by AVAssetTrack. Tracks contain metadata such as which parts of the media to use, volume levels, etc.

An AVAsst can also contain metadata where it is common to all tracks.

###Playback Theory

On iOS there is a fairly high-level media player framework which is not part of AVFoundation so I'm not going to discuss it now. It's a quick and easy solution and will work for a lot of people but it is not in Lion.

So, assuming you are using AVFoundation, playback occurs via an AVPlayer. An AVPlayer has an AVPlayerItem which represents the presentation state of an AVAsset. For example, an AVPlayerItem can seek to a time in a movie and it can report the current playback time. If AVAsset is static media data then AVPlayerItem is the dynamic playback state of the media.

When you instantiate an AVPlayer you can either pass it a URL which in turn creates the AVPlayerItem and AVAsset or you can create your AVPlayerItem and then associate it with an AVPlayer.

An AVPlayer is really a controller. You can play the movie, change its rate, jump to a particular point, etc. but it doesn't display it. For this you use an AVPlayerLayer which is a Core Animation layer.

This is all getting a bit complex so a simplified view is that you have an AVPlayerLayer to display the video and an AVPlayer which plays the video which is being displayed, an AVPlayerItem which is the movie being played and an AVAsset which is the movie data. 

###Playback Practice

* Create an AVURLAsset via URLAssetWithURL:options:
* Create an array of 'keys' you want to inspect. These would be things like whether the media is playable or hasProtectedContent.
* Invoke AVAsset's loadValuesAsynchronouslyForKeys:completionHandler: method. Pass in the array of keys and a block to invoke once the values for the keys are available.
* Create an AVPlayerItem with AVPLayerItem's playerItemWithAsset: method.
* Create the AVPlayer with AVPlayer's playerWithPlayerItem: method.
* Create an AVPlayerLayer with AVPlayerLayer's playerLayerWithPlayer: method.


###Capture

The AVFoundation classes are based on the QTKit classes. Makes sense since QTKit capture elements are relatively new (compared to some of QTKit and the QuickTime frameworks) and were introduced in 10.5 Leopard.

QTCaptureConnection
QTCaptureDevice
QTCaptureFileOutput
QTCaptureMovieFileOutput

####Basic Workflow

* Create an AVCaptureSession which manages the capture.
* Investigate the available AVCaptureDevices.
* Create AVCaptureDeviceInputs and connect them to the session.
* Optionally set up an AVCaptureVideoPreviewLayer.
* Optionally connect AVCaptureOutputs.
* Tell the session to begin recording.

###AVComposition

An AVAsset that gets its tracks from multiple file-based sources.

AVMutableComposition is usually used to create a movie.

Can do things like insertTimeRange:ofAsset:atTime:error:

Editing means you'll meet CMTime which is the Core Media sibling of QTTime. Have a time value and a time scale.

####Track-level editing

Add an AVMutableCompositionTrack to the AVMutableComposition.

Methods such as insertTimeRange:ofTrack:atTime:error:

###Export

* Create an AVAssetExportSession. You define an export preset name such as 'AVAssetExportPreset1280x720'.
* Must set the outputURL and outputFileType properties. File type is something like 'AVFileTypeAppleM4V'.
* This will create an M4V file at 1280x720.
* Begin export with exportAsynchronouslyWithCompletionHander: which uses blocks. In the block check the exportSession's status to check for failure or completion.

###Further Information

Go and look at the WWDC 2011 videos. Sessions 405, 415 and 417.

---

I’m [@sgaw on Twitter](http://twitter.com/sgaw), [@sw on App.net](https://alpha.app.net/sw) and you can also email me at simon at this domain.