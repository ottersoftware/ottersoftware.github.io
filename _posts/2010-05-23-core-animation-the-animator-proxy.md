---
date: 2010-05-23 11:49
title: Core Animation - The Animator Proxy
permalink: /posts/2010-05-23-core-animation-the-animator-proxy
categories: cocoa
layout: post
share: true
---

### Introduction
The animator proxy is available to NSViews and NSWindows and is completely free of Core Animation layers. As such is technically not Core Animation but is rather Cocoa Animation. However the two are so closely tied together that it is worth considering it part of Core Animation. This article explains what the animator proxy does behind the scenes when you invoke it and how to customise the animations is uses.

### A Quick Definition
<blockquote>The Animator proxy is the quickest way to implement Core Animation effects in your user interface. A one word change in your code can evolve a property change from happening immediately into one that is animated and both looks great and also helps the user understand what is going on.

As an example, imagine a scenario where you want to move an image from one side of a view to another. If you simply call something like <code>[imageWell setFrameOrigin:NSMakePoint(150.0, 25.0)]</code> then image will jump straight to the new position. If however you call <code>[[imageWell animator] setFrameOrigin:NSMakePoint(150.0, 25.0)]</code> the image will glide to its new position over a quarter of a second.

The animator proxy can be enhanced by customising the animations that take place in terms of their duration and their timing and this extends to keyframe animations.

Finally, being completely accurate, the animation caused via the animator proxy is termed Cocoa Animation or Cocoa Animatable Proxy Animation. This kind of animation does not require Core Animation layers.</blockquote>

<cite>Taken from ‘<a href="http://www.ottersoftwareblog.com/blog/2010/5/22/core-animation-animator-proxy-calayers-layer-backed-layer-ho.html">Core Animation - Animator Proxy, CALayers, Layer-Backed & Layer Hosting</a>’</cite>

### Using The Animator Proxy
As the above may suggest you can invoke the animator proxy simply by telling it to change your window or view's property rather than having the window or view do it itself.

A simple example of implementing the animator proxy is changing this:
<pre>[myView setOrigin:NSMakePoint(100.0f, 100.0f)];</pre>

to this:
<pre>[[myView animator] setOrigin:NSMakePoint(100.0f, 100.0f)];</pre>

Once the animator proxy is used the view will animate the change to its origin meaning that it glides into position rather than jumping there. 

### The Animator Proxy Explained
In the above example the magic of Core Animation is invoked via the use of ‘animator’ which is a proxy object which belongs to the NSAnimatablePropertyContainer protocol. Clear? No? Good.

In plain English what is happening is that rather than the view simply setting its own origin to the new value and the display being updated with the view appearing to jump from its old position to the new one, the animator proxy object steps in and moves the view using an animation effect. The view is essentially asking the animator object to move it to its new location in a beautiful and elegant way and the animator asks the NSAnimatablePropertyContainer protocol how it should do this, gets some instructions back and then performs the move according to those instructions.

The NSAnimatablePropertyContainer knows how to perform simple moves, rotations and things like that, because the developers at Apple have taught it some basic animation effects but you also have the ability to teach it new ones. The built-in and any new animations that you define are stored in dictionaries, one for your animations, one for the built-in ones. The values in the dictionaries are the details about how the animation is to be performed whilst the key is, as usual, a string which names the animation. This is where key-value coding comes in because when you ask the animator proxy to perform an animation is used key-value coding to check first your dictionary of user-defined animations and then the built-in dictionary of animations.

So, using the above example of changing a view’s origin, what actually happens is that your view asks the animator proxy to move its origin. The animator proxy asks the NSAnimatablePropertyContainer to check your dictionary of animations, unsurprisingly called animations, to see if there is an animation definition stored in it called ‘Origin’. If it is then it uses the animation to change the view’s origin and hence move the view. If there isn’t then it asks it if there is an animation in its pre-defined dictionary called ‘Origins’. Again, if there is then it uses the animation to change the view’s origin and hence move the view. If there isn’t then there isn’t a fall-back so the animator proxy simply changes the origin and moves the view without animating it.

More technically, the animator proxy initially invokes the NSAnimatablePropertyContainer’s animationForKey: method which will checks the NSAnimatablePropertyContainer’s animations dictionary to see if it can find an animation with the required name. If it can it returns the animation’s details to the animator which then performs the animation. If the requested animation is not listed in the dictionary then the NSAnimatablePropertyContainer’s defaultAnimationForKey: class method is invoked.

### Simple Concurrent Animations
It is possible to perform multiple animations concurrently. For example, you can change the origin of a frame and the size of a frame at the same time by simply calling the relevant methods in the same method:

<pre>[[textField animator] setFrameOrigin:[self newOrigin]];
[[textField animator] setFrameSize:[self newSize]];
</pre>

### Setting The Animation Duration
Cocoa Animation provides the ability to override the default duration setting of an animation using the NSAnimationContext object.

To use NSAnimationContext you begin a grouping, set the duration of the current context, invoke the animation and then end the grouping. The code would look something like this:

<pre>[NSAnimationContext beginGrouping];
[[NSAnimationContext currentContext] setDuration:3.0f];
[[textField animator] setFrameOrigin:NSMakePoint([textField frame].origin.x, 10.0)];
[NSAnimationContext endGrouping];</pre>

It is possible to nest NSAnimationContexts so that you can apply different durations to different animations that occur concurrently. This is a basic form of grouping which is explained later in this article.

<pre>[NSAnimationContext beginGrouping];
[[NSAnimationContext currentContext] setDuration:5.0f];
[[textField animator] setFrameOrigin:NSMakePoint([textField frame].origin.x, 10.0)];

[NSAnimationContext beginGrouping];
[[NSAnimationContext currentContext] setDuration:2.0f];
[[textField animator] setFrameSize:NSMakeSize([textField frame].size.width, 44.0)];
[NSAnimationContext endGrouping];

[NSAnimationContext endGrouping];</pre>

The above code creates an initial NSAnimationContext where the animation duration is 5 seconds and a secondary one where the animation duration is 2 seconds. The setFrameOrigin: method will be animated using the 5 second duration whilst the setFrameSize: method will be animated using the 2 second duration. In effect the animation will begin and the NSTextField’s frame origin and frame size will all being to change. After two seconds the frame size will have been set and 3 seconds later the changes to the frame’s origin will be complete.

<strong>Gotcha!</strong> You need to watch out because if you have defined a custom animation for say the frameOrigin property and you try to override the duration of that animation using NSAnimationContext the NSAnimationContext will have no effect. NSAnimationContext only seems to override the built-in default durations.

[Download an example project](http://images.swwritings.com/2010-05-23-core-animation-the-animator-proxy-01.zip)


### Adding Your Own Animations
As discussed above, the animator proxy searches a dictionary for any custom animations before it applies the default one. Adding your own animations is fairly simple as long as you are aware of a couple of gotchas.

Once you have defined an animation (see below for details about how to do this and work on the basis that <code>[self customAnimation]</code> is used to create an animation object) you need to add it to the animations dictionary of the window or view you want to apply it to. You may think that this will work:

<pre>[textField setAnimations:[NSDictionary dictionaryWithObject:[self customAnimation] forKey:@"frameOrigin"]];</pre>

Well it will as long as you only want one custom animation applied to the view, in this example an NSTextField. If you already have an animation defined or want to add more you will quickly discover that the above code replaces the entire animations dictionary with your one new animation. The following code is a better approach:

<pre>NSMutableDictionary *newAnimations = [NSMutableDictionary dictionary];
[newAnimations addEntriesFromDictionary:[textField animations]];
[newAnimations setObject:[self customAnimation] forKey:@"frameOrigin"];
[textField setAnimations:newAnimations];</pre>

You might think that you could simplify the code by writing something like this:

<pre>NSMutableDictionary *newAnimations = [[[textField animations] mutableCopy] autorelease];
[newAnimations setObject:[self customAnimation] forKey:@"frameOrigin"];
[textField setAnimations:newAnimations];</pre>

The problem is that if the animations dictionary is empty then the mutable array is not created so you are effectively setting the animations dictionary to nil in the third line.

[Download an example project containing this code](http://images.swwritings.com/2010-05-23-core-animation-the-animator-proxy-02.zip)

Sometimes you will not see an animation effect that you expect. If, for example, you wanted to change the the textField’s size as well as origin you might create your new animation and set its key to ‘frame’:

<pre>[textField setAnimations:[NSDictionary dictionaryWithObject:[self customAnimation] forKey:@"frame"]];
NSRect newFrame = [textField frame];
newFrame.origin.x = 400.0f;
newFrame.size.width = 50.0f;
[[textField animator] setFrame:newFrame];</pre>

If you run this then the frame would change its origin and size but it would do so using the default basic animation properties. To resolve this you create two custom animations: one for the frameOrigin and one for the frameSize:

<pre>NSMutableDictionary *newAnimations = [NSMutableDictionary dictionary];
[newAnimations addEntriesFromDictionary:[textField animations]];
[newAnimations setObject:[self customAnimation] forKey:@"frameOrigin"];
[newAnimations setObject:[self customAnimation] forKey:@"frameSize"];
[textField setAnimations:newAnimations];

NSRect newFrame = [textField frame];
newFrame.origin.x = 400.0f;
newFrame.size.width = 50.0f;
[[textField animator] setFrame:newFrame];</pre>

Essentially, if something doesn’t work as expected play around and change the key values. The most obvious key is not always the correct one.

### Creating Animations
When you allow the animator proxy or a CALayer to perform one of the standard animations it is applying what is termed a basic animation, an animation represented by the CABasicAnimation class. By default an animation effect lasts for a quarter of a second and it eases in and eases out. You can define your own basic animations to override the default ones and you can also go a step further and override them with keyframe animations.

### Media Timing - CAMediaTiming
Before getting into the animation objects themselves it is worth talking about media timing which essentially describes how an object's speed changes during the course of the animation. Above I mentioned that the default animation eases-in and eases-out which means that if you are moving a view from one part of a window to another the view will start moving slowly, accelerate, move across the window at a constant speed and will then decelerate to a stop. The CAMediaTiming object, which is what determines the media timing, has five pre-defined timing functions:
<ul>
<li>Linear - The animation occurs at a constant speed over the duration of the animation.</li>
<li>Ease-In - The animation begins slowly and accelerates to and finishes at a constant speed.</li>
<li>Ease-Out - The animation starts and runs at a constant speed until it slows down to a stop.</li>
<li>Ease-In, Ease-Out - The animation begins slowly and accelerates up to a constant speed before slowing down to a stop.</li>
<li>Default - The default timing function is an ease-in, ease-out function were the initial acceleration occurs quickly and the deceleration occurs at a more stately pace.</li>
</ul>

There is a really useful web page at [http://netcetera.org/camtf-playground.html](http://netcetera.org/camtf-playground.html) where you can see how CAMediaFunctions look as plotted on a graph which is extremely useful because being able to visualise them helps enormously.

### Basic Animation - The CABasicAnimation Object
A basic animation allows you to set the duration and/or timing function of the animation.

To create a new CABasicAnimation you would write something like this:

<pre>CABasicAnimation *animation = [CABasicAnimation animation];
  [animation setDuration:5.0f];
  [animation setTimingFunction:[CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut]];</pre>

This animation has a duration of five seconds and the media timing function is set to the pre-defined ease-in, ease-out function.

I tend to define animations in their own methods rather than creating them in-line in my code because it makes them more reusable, breaks the code into more clearly defined chunks and keeps the lengths of my methods under control.

<pre>- (CABasicAnimation *)customAnimation;
{
  CABasicAnimation *animation = [CABasicAnimation animation];
  [animation setDuration:5.0f];
  [animation setTimingFunction:[CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut]];
  return animation;
}</pre>

You can now simply add this new animation object to the animations dictionary of your window or view as described above to use it.

[Download an example project](http://images.swwritings.com/2010-05-23-core-animation-the-animator-proxy-03.zip)

### Keyframe Animation - The CAKeyframeAnimation Object
Keyframe animation allows you finer-grain control over your animations. For example, you can create a keyframe animation where you change the origin of a view over three seconds and have the view move half way to its destination in the first second, stay stationary for a second and then move to its final destination in the last second.

<pre>- (CAKeyframeAnimation *)keyframeAnimation;
{
  CGFloat midPoint = NSMidX([[window contentView] frame]) - ([view frame].size.width / 2.0f);

  CAKeyframeAnimation *animation = [CAKeyframeAnimation animation];
  [animation setDuration:3.0f];
  [animation setValues:[NSArray arrayWithObjects:
                        [NSValue valueWithPoint:NSMakePoint([view frame].origin.x, [view frame].origin.y)],
                        [NSValue valueWithPoint:NSMakePoint(midPoint, [view frame].origin.y)],
                        [NSValue valueWithPoint:NSMakePoint(midPoint, [view frame].origin.y)],
                        [NSValue valueWithPoint:[self newViewOrigin]],
                        nil]];
  return animation;
}</pre>

In the above example, midPoint is simply a calculated value of where the x origin of the view should be if the view is centred horizontally in the window. The interesting part is the array that is used for the animation's values property. This lists four points (wrapped in NSValues) which are the four keyframe points that the view should travel through. The first is the starting point of the view, the second and third are both the mid-point origins and the fourth is the final origin point for the view. We have given the animation three points to move through from an initial starting point and, to keep the maths simple, we've told it to carry out the entire process over three seconds. This means that it will move from point 1 to point 2 during the first second, from point 2 to point 3 over the second second and from point 3 to point 4 during the last second. Because points 2 and 3 are the same the view will remain stationary for that middle second.

We can enhance this further by giving each of the values a cut of the total time of the animation. Let's say that you actually wanted the view to move from its original position to point 1 in half a second, pause for one second and then move to its intended destination in one and a half seconds. You can do this by passing an array of floating point numbers to the CAKeyframeAnimation's keyTimes property. Because you are apportioning the total time of the animation the numbers are fractions of the total duration meaning that the start of the animation has a value of 0.0 and the end of the animation has a value of 1.0.

Enhancing our earlier example to do the above would result in:

<pre>- (CAKeyframeAnimation *)keyframeAnimation;
{
  CGFloat midPoint = NSMidX([[window contentView] frame]) - ([view frame].size.width / 2.0f);

  CAKeyframeAnimation *animation = [CAKeyframeAnimation animation];
  [animation setDuration:3.0f];
  [animation setValues:[NSArray arrayWithObjects:
                        [NSValue valueWithPoint:NSMakePoint([view frame].origin.x, [view frame].origin.y)],
                        [NSValue valueWithPoint:NSMakePoint(midPoint, [view frame].origin.y)],
                        [NSValue valueWithPoint:NSMakePoint(midPoint, [view frame].origin.y)],
                        [NSValue valueWithPoint:[self newViewOrigin]],
                        nil]];
  [animation setKeyTimes:[NSArray arrayWithObjects:
                          [NSNumber numberWithFloat:0.0],
                          [NSNumber numberWithFloat:0.17],
                          [NSNumber numberWithFloat:0.67],
                          [NSNumber numberWithFloat:1.00],
                          nil]];
  return animation;
}</pre>

There are four key times, one for each of the values. The first key time means that the first point should be reached at that time, i.e. this is the starting point and starting time. The second key time means that 17% of the way through the three second duration (17% of 3 seconds is 0.46 of a second, close enough to our half a second) of the animation the origin of the view should be at the second point. The third time means that at 67% of the duration of the animation the third point should be reached and the fourth means that at 100% of the duration of the animation the fourth point should be reached.

We can further enhance the animation by passing an array of media timing functions to the timingFunctions property of the CAKeyframeAnimation. Let's now perform the initial move with a linear timing function and then have an ease-in timing function for the last stage of the animation:

<pre>- (CAKeyframeAnimation *)keyframeAnimation;
{
  CGFloat midPoint = NSMidX([[window contentView] frame]) - ([view frame].size.width / 2.0f);
  
  CAKeyframeAnimation *animation = [CAKeyframeAnimation animation];
  [animation setDuration:3.0f];
  [animation setValues:[NSArray arrayWithObjects:
                        [NSValue valueWithPoint:NSMakePoint([view frame].origin.x, [view frame].origin.y)],
                        [NSValue valueWithPoint:NSMakePoint(midPoint, [view frame].origin.y)],
                        [NSValue valueWithPoint:NSMakePoint(midPoint, [view frame].origin.y)],
                        [NSValue valueWithPoint:[self newViewOrigin]],
                        nil]];
  [animation setKeyTimes:[NSArray arrayWithObjects:
                          [NSNumber numberWithFloat:0.0],
                          [NSNumber numberWithFloat:0.17],
                          [NSNumber numberWithFloat:0.67],
                          [NSNumber numberWithFloat:1.00],
                          nil]];
  [animation setTimingFunctions:[NSArray arrayWithObjects:
                                 [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionLinear],
                                 [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionLinear],
                                 [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseIn],
                                 nil]];
  return animation;
}</pre>

Note that there is one less media timing function than there are values or keyTimes. This is because the media timing function apply to the animation between the values and not to the values themselves.

[Download an example project](http://images.swwritings.com/2010-05-23-core-animation-the-animator-proxy-04.zip)

As an alternative to creating an array of values for a keyframe animation it is possible to use a path for situations where the values are dual-value properties such as origin which has both an x and a y value. This is particularly useful when you want to animate something in a non-linear fashion. For example, if we decided to move our view across the window but wanted it to go from top-left to bottom-right in a smooth curve we would be unable to do this properly (well realistically) using values but it can be done easily using a path:

<pre>- (CAKeyframeAnimation *)keyframeAnimation;
{
  CGPoint midPoint = CGPointMake(NSMidX([[window contentView] frame]) - ([view frame].size.width / 2.0f),
                                 NSMidY([[window contentView] frame]) - ([view frame].size.height / 2.0f));
  
  CGMutablePathRef path = CGPathCreateMutable();
  CGPathMoveToPoint(path, NULL, [view frame].origin.x, [view frame].origin.y);
  CGPathAddCurveToPoint(path, NULL, [view frame].origin.x, midPoint.y, [view frame].origin.x, midPoint.y, midPoint.x, midPoint.y);
  CGPathAddCurveToPoint(path, NULL, [self newViewOrigin].x, midPoint.y, [self newViewOrigin].x, midPoint.y, [self newViewOrigin].x, [self newViewOrigin].y);
  
  CAKeyframeAnimation *animation = [CAKeyframeAnimation animation];
  [animation setDuration:3.0f];
  [animation setPath:path];
  [animation setTimingFunctions:[NSArray arrayWithObjects:
                                 [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseIn],
                                 [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseOut],
                                 nil]];
  
  CFRelease(path);
  
  return animation;
}</pre>

In the above example we run the animation over three seconds and we apply an ease-in timing function to the animation from the origin point to the mid point and then an ease-out timing function to the animation from the mid point to the end point. This gives the impression that the view is moving along the path in one fluid motion, starting slowly, reaching a constant speed and then slowing down as it reaches the end.

As before we can set keyTimes for the animation too so if we wanted to perform an animation where the view moves to the centre of the window, pauses and then moves on to the destination point we would use this code:

<pre>- (CAKeyframeAnimation *)keyframeAnimation;
{
  CGPoint midPoint = CGPointMake(NSMidX([[window contentView] frame]) - ([view frame].size.width / 2.0f),
                                 NSMidY([[window contentView] frame]) - ([view frame].size.height / 2.0f));
  
  CGMutablePathRef path = CGPathCreateMutable();
  CGPathMoveToPoint(path, NULL, [view frame].origin.x, [view frame].origin.y);
  CGPathAddCurveToPoint(path, NULL, [view frame].origin.x, midPoint.y, [view frame].origin.x, midPoint.y, midPoint.x, midPoint.y);
  CGPathAddLineToPoint(path, NULL, midPoint.x, midPoint.y);
  CGPathAddCurveToPoint(path, NULL, [self newViewOrigin].x, midPoint.y, [self newViewOrigin].x, midPoint.y, [self newViewOrigin].x, [self newViewOrigin].y);
  
  CAKeyframeAnimation *animation = [CAKeyframeAnimation animation];
  [animation setDuration:3.0f];
  [animation setPath:path];
  [animation setKeyTimes:[NSArray arrayWithObjects:
                          [NSNumber numberWithFloat:0.0],
                          [NSNumber numberWithFloat:0.33],
                          [NSNumber numberWithFloat:0.66],
                          [NSNumber numberWithFloat:1.0],
                          nil]];
  [animation setTimingFunctions:[NSArray arrayWithObjects:
                                 [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut],
                                 [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut],
                                 nil]];
  
  CFRelease(path);
  
  return animation;
}</pre>

One thing to note is that like when we used values we need the number of path segments to match the number of key times so there is a new path drawing element that simply draws the path from the point it is at to the point it is at.

[Download an example project](http://images.swwritings.com/2010-05-23-core-animation-the-animator-proxy-05.zip)

### Grouping Animations
Sometimes you might want to group animations together and you can do this with the CAAnimationGroup object. Assume, for example, that you wanted to move a view across a window and, at the same time, change its size.

To do this you would define your two animations, one for the frameOrigin and one for the frameSize properties:

<pre>- (CABasicAnimation *)originAnimation;
{
  CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"frameOrigin"];
  [animation setDuration:3.0f];
  [animation setTimingFunction:[CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut]];
  [animation setFromValue:[NSValue valueWithPoint:[view frame].origin]];
  [animation setToValue:[NSValue valueWithPoint:[self newViewOrigin]]];
  return animation;
}

- (CABasicAnimation *)sizeAnimation;
{
  CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"frameSize"];
  [animation setDuration:3.0f];
  [animation setBeginTime:1.5f];
  [animation setTimingFunction:[CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionLinear]];
  [animation setFromValue:[NSValue valueWithSize:[view frame].size]];
  [animation setToValue:[NSValue valueWithSize:[self newViewSize]]];
  return animation;
}</pre>

You will notice a few new things here. The first is that the CABasicAnimation object is created via the convenience method animationWithKeyPath: which allows you to tell the animation which property it applies to. Next is that the sizeAnimation sets a beginTime property. This allows us to offset the start of the animation by a number of seconds. Third is that we are setting a fromValue and a ToValue for the animation. These are necessary for grouped animations and they are also the only way to define what the original and new values are because you are not going to be setting them when you use the animator proxy.

Once you have defined your animations it is time to add them to the group:

<pre>CAAnimationGroup *group = [CAAnimationGroup animation];
[group setAnimations:[NSArray arrayWithObjects:[self originAnimation], [self sizeAnimation], nil]];
[group setDuration:4.5];

NSDictionary *anims = [NSDictionary dictionaryWithObject:group forKey:@"groupedAnimation"];
[view setAnimations:anims];</pre>

You supply the group with a list of the animations you want to group together in an array. We are also setting the duration of the animation group, specifying that the group animation should run over four and a half seconds. This is because the sizeAnimation does not start until one and a half seconds have passed from the group animation beginning and it runs for three seconds.

We then add the animation group to the animations dictionary of the view, giving it a key name of 'groupedAnimation'.

Finally, to cause the animation to run we use:

<pre>[[view animator] setValue:nil forKey:@"groupedAnimation"];</pre>

The value we pass is irrelevant as nothing uses it but the key is the name we gave to our animation group and the simple act of setting its value to nil causes the animation group to run which in turn executes the two animations within it.

<strong>Important:</strong> Using a custom animation name like this does introduce one problem. You are effectively trying to set the value of a property that does not exist in the target window or view and this will raise an exception. The work-around is to override the setValue:forUndefinedKey: in a subclass of the window or view:

<pre>- (void)setValue:(id)value forUndefinedKey:(NSString *)key
{
  return;
}</pre>

[Download an example project](http://images.swwritings.com/2010-05-23-core-animation-the-animator-proxy-06.zip)

### Custom Names
As described above, you can give an animation a custom name and invoke it via the setValue:forKey: method of the window or view's animator proxy. This adds more flexibility to the system because it means that you can pre-define a whole raft of animations that, for example, apply to a view's origin, add them all to your view's animations dictionary using a custom key for each and then invoke the required animation using that custom key. Just remember that because the animation is not linked to an existing animatable property you must use the animationWithKeyPath: convenience method to create the CAAnimation object and you must override setValue:forUndefinedKey: in a subclass of the object invoking the method.
