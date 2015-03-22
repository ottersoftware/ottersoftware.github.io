---
date: 2012-10-04 16:00
title: Handling Indirectly Returned NSError Objects Properly
categories: cocoa
layout: post
share: true
---

In the Cocoa frameworks you will find methods such as:

	- (BOOL)removeItemAtURL:(NSURL *)URL error:(NSError **)error

When you use these methods you should not check to see if the NSError object is nil to determine if the method succeeded or failed, instead check the method's return value and if that indicates failure then check the NSError object.

Apple's [Error Handling Programming Guide](http://developer.apple.com/mac/library/documentation/cocoa/Conceptual/ErrorHandlingCocoa/CreateCustomizeNSError/CreateCustomizeNSError.html#//apple_ref/doc/uid/TP40001806-CH204-SW2) highlights this fact by saying:

<cite>Important: Success or failure is indicated by the return value of the method. Although Cocoa methods that indirectly return error objects in the Cocoa error domain are guaranteed to return such objects if the method indicates failure by directly returning nil or NO, you should always check that the return value is nil or NO before attempting to do anything with the NSError object.</cite>

[Apparently](https://twitter.com/bbum/status/6131130082), according to Apple engineer Bill Bumgarner,  Apple can 'scribble' on the error object and may not reset it to nil even if the method is going to return successfully. Therefore, since the returned NSError object is 'unsafe', setting it to nil before calling the method is effectively unnecessary. It may even be a good practice not to do so to help remind yourself not to check the state of the NSError object before checking the method's return value.

I'd suggest following this pattern in your own code too. If you create a method which includes a return-by-reference NSError then ensure that the method also has a return value.

---

__Updated on 5 October 2012__

[Sam Deane](https://twitter.com/samdeane) published a [blog post](http://www.bornsleepy.com/bornsleepy/nserror-and-paranoia) in response to this article, and specifically my advice that setting NSError to nil was unnecessary.  This is solely to ensure that if the code you are calling is badly written then your app won't crash.

[Graham Lee](https://twitter.com/secboffin) has also pointed out that in an ARC environment stack variables are implicitly initialised with nil.

My conclusion is that in an ARC environment, because NSError is being initialised with nil, not doing so explicitly in your code if you still do so out of habit from the pre-ARC days, can be a good reminder to not automatically check the NSError object's value. Mind you, after reading all of this you've never going to do that again anyway, right?
