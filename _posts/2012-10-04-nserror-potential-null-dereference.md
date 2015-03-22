---
date: 2012-10-04 17:00
title: NSError and the Potential Null Dereference Warning
permalink: /post/2012-10-04-nserror-potential-null-dereference
categories: cocoa
layout: post
share: true
---

Clang may generate warnings about potential null dereference when you are using return-by-reference NSError objects. It is essentially warning you that someone calling the method might pass NULL as the NSError parameter which means that you cannot set it to something else. If you do the application will crash.

Imagine the following method:

	- (NSString*)reverseName:(NSString*)sourceName error:(NSError**)error;
	{
		NSArray *nameElements = [sourceName componentsSeparatedByString:@" "];
		
		if ([nameElements count] == 2) {
			return [NSString stringWithFormat:@"%@ %@", [nameElements objectAtIndex:1], [nameElements objectAtIndex:0]];
		}
		
		NSError *localError = [NSError errorWithDomain:@"NameManagerError" code:9 userInfo:[NSDictionary dictionaryWithObject:@"The name consists of more than or less than two space-delimited elements." forKey:NSLocalizedDescriptionKey]];
		*error = localError;
		return nil;
	}

[_View this source code on GitHub_](https://github.com/ottersoftware/SWwritings/blob/master/2012-10-04%20NSError%20and%20the%20Potential%20Null%20Dereference%20Warning/2012-10-04-nserror-potential-null-dereference-01.m)

Assuming that it is called with code similar to that below then all will be fine:

	NSString *fullName = @"Simon";
	NSError * error = nil;
	NameManager *nameManager = [[NameManager alloc] init];
	NSString *modifiedName = [nameManager reverseName:fullName error:&error];

However if it is called with this code then you will get a crash:

	NSString *fullName = @"Simon";
	NameManager *nameManager = [[NameManager alloc] init];
	NSString *modifiedName = [nameManager reverseName:fullName error:NULL];

The problem is that if an NSError pointer variable isn't passed in then you can't use it so your code needs to be changed to:

	- (NSString*)reverseName:(NSString*)sourceName error:(NSError**)error;
	{
		NSArray *nameElements = [sourceName componentsSeparatedByString:@" "];
		
		if ([nameElements count] == 2) {
			return [NSString stringWithFormat:@"%@ %@", [nameElements objectAtIndex:1], [nameElements objectAtIndex:0]];
		}
		
		NSError *localError = [NSError errorWithDomain:@"NameManagerError" code:9 userInfo:[NSDictionary dictionaryWithObject:@"The name consists of more than or less than two space-delimited elements." forKey:NSLocalizedDescriptionKey]];
		if (error) {
			*error = localError;
		}
		return nil;
	}

[_View this source code on GitHub_](https://github.com/ottersoftware/SWwritings/blob/master/2012-10-04%20NSError%20and%20the%20Potential%20Null%20Dereference%20Warning/2012-10-04-nserror-potential-null-dereference-02.m)

Now you are checking that the error object exists and avoiding the potential crash.

---

Many thanks to [Alastair Houghton](http://alastairs-place.net) for pointing out that, "you shouldn’t be passing nil for an NSError ** parameter. nil is a NULL object pointer; you want a NULL pointer-to-an-object-pointer, so should pass NULL (not nil)."

The post and code has been updated to reflect this.
