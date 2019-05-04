---
date: 2012-10-16 12:40
title: Functions vs Classes & Methods
permalink: /post/2012-10-16-functions-versus-classes-and-methods
categories: cocoa
layout: post
share: true
---

A couple of weeks ago I wrote about [double indirection](https://swwritings.com/post/2012-10-04-double-indirection) and one of the benefits of doing so, apart from getting it all straight in my head, was that I've started to use it myself, particularly in a client's project where I'm doing a lot of date manipulation (go and watch the WWDC 2011 video for session 117, Performing Calendar Calculations if you're doing something similar). This project and the date manipulation functionality also allowed me to advance [my goal to become more comfortable with C](https://swwritings.com/post/2012-09-11-learning-c-programming-and-code-runner) and rather than creating a date management class and methods or creating an NSDate category I've been creating functions instead.

Before I get onto an explanation of why I'm using functions for this rather than classes and methods and for the sake of technical completeness I'll mention that an Objective-C method is actually a C function. Bill Bumgarner (@bbum on [Twitter](https://twitter.com/bbum) and [Stack Overflow](http://stackoverflow.com/users/25646/bbum)) explains it in this in, probably amongst other places, [a Stack Overflow post](http://stackoverflow.com/questions/4846825/c-function-vs-objective-c-method) which you should read if you want a proper explanation.

However, on a more basic level, if you have methods in a class then you generally need to instantiate an instance of that class before you can call its methods (see the addendum at the end of this post for some information about class methods):

	OTSDateManager *dateManager = [[OTSDateManager alloc] init];
	NSString *dayName = [dateManager nameOfFirstDayOfMonthNumber:1 inYearNumber:2012];

This in itself isn't particularly arduous but being able to just call a function as and when you need it rather than having to create an instance of your class first does make life slightly easier and it feels a bit more flexible.

	NSString *dayName = OTSNameOfFirstDayOfMonthNumberInYearNumber(1, 2012);

You will notice that I've prefixed my function's name with OTS and I've done this to minimise the chances of clashing with a duplicate function name in an external framework or other third-party code.

The function is defined as:

	NSString *OTSNameOfFirstDayOfMonthNumberInYearNumber(NSInteger monthNumber, NSInteger yearNumber);

In a simpler case, say retrieving the day name of an actual date, I would probably have created a category for NSDate and created a method such as:

	- (NSString*)otsDayName;

_Again, note the method name's prefix to avoid collision if Apple were to add a dayName method to the NSDate class._

However, to use this when I just have the month number and a year I'd have to create an NSDate object first rather than having the date creation (which is a necessary step) in the function.

Categories are generally good but when a specific area of functionality is split over several classes such as NSDate, NSDateComponents and NSDateFormatter you then have to add several categories and remember which methods are where and apply to which object. They're not always the simplest solutions.

So what are the down-sides to functions? Because C functions are not part of a class they cannot access a class's instance variables or properties. A function really is a self-contained chunk of code.

In addition you lose the Objective-C-style verbose naming capabilities. In our imaginary OTSDateManager class we have a method called:

	- (NSString*)nameOfFirstDayOfMonthNumber:(NSInteger)monthNumber inYearNumber:(NSInteger)yearNumber;

The equivalent imaginary function is:

	NSString *OTSNameOfFirstDayOfMonthNumberInYearNumber(NSInteger monthNumber, NSInteger yearNumber);

I'd argue that the method name is more readable and clearer than the function name. I'm a fan of the more verbose Objective-C method names but, for the sake of convenience I'm happy to forgo them occasionally.

So go and try using functions if you've not done so before. Not everything has to be encapsulated in a class. Once you get past the non-Objective-C function naming convention it's all the same, right down to being able to use Objective-C objects and methods in them as long as you import the Foundation header file.

---

__Addendum__

Since this post was first published several people correctly said that you don't need to instantiate a class before calling a class method. My original response was that a class method is intended to create an autoreleasing instance of the class, preconfigured in some way. For example, _pathWithComponents:_ is an NSString class method which returns an autoreleased NSString object created from the components array it is passed. This is actually incorrect and what I defined was a factory method. A class method can and should do anything unrelated to a specific instance of the class. Thank you to [Paul Lynch](https://alpha.app.net/pauldlynch).

On that basis a class method may be a good alternative to a C function and has two benefits:

* You get a "poor-man's namespace" via the class's name.
* You still get dynamic dispatch and swizzling opportunities (something I actually try to avoid but that's a post for another day!).

The down-side remains that as they are still dynamic they have a higher overhead.
 
Thank you to [Phil Nash](https://alpha.app.net/phil_nash).
