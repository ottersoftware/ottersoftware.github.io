---
date: 2012-10-04 11:42
title: Double Indirection
permalink: 2012-10-04-double-indirection
categories: cocoa
layout: post
share: true
---

A [recent thread](https://devforums.apple.com/thread/168948?start=0&tstart=0) in Apple's Developer Forums (login required) consisted of a lengthy discussion based on a question about why, in iOS 6, '==' was not the same as 'isEqual:' with respect to comparing strings. The originator of the question said that this worked before iOS 6 and therefore Apple had broken something.

The simple answer is that '==' is a comparison operator for primitive types whilst 'isEqual:' is applicable to objects, of which NSString is one. Applying '==' to objects simply compares the memory addresses where the objects are stored to see if they are the same. It does not compare what is actually stored at the addresses.

The reason that the '==' comparison sometimes appears to work is that compiler optimisations could cause string constants to be stored once so pointers would indeed point to the same address.

However, that's not specifically what this post is about. Rather the confusion over pointers prompted me to investigate and resolve an issue I had with understanding double indirection. This blog post is my attempt to grok double indirection and I thought that my notes might help someone else too.

If you're not comfortable with the concept of pointers then I'd highly recommend reading [Book 6: What is Memory?](http://masters-of-the-void.com/book5.htm) in Uli Kusterer's [Masters of the Void](http://masters-of-the-void.com) tutorial. In fact some of the content below duplicates what is in [Book 6](http://masters-of-the-void.com/book5.htm) and [Book 7: Multiple return Values](http://masters-of-the-void.com/book6.htm). Uli's posts are great and may be enough to explain everything to you but if not read on...

In C a pointer variable is denoted by a preceding asterisk:

	int *result;
	
This declaration states that result is a pointer variable capable of pointing to variables of type int.

C provides two operators designed to help us work with pointers. The address operator, denoted by &, allows us to find the address of a variable. To gain access to whatever a pointer points to we can use the indirection operator, denoted by *.

Knowing about the indirection operator allows us to expand on our explanation of _int *result;_ slightly. We can also say that if you look at what is at the address stored in _result_ then it will be an int. 

In the code below we declare an int variable called counter. We declare a pointer variable called postman (it deals with addresses... give me a break... code-related puns are tricky!) and then we assign the address of counter to the value of postman:

	int main(int argc, char *argv[]) {
		int counter = 5;
		int *postman = &counter;
		
		printf("The address of counter is %p\n", &counter);
		printf("The address of postman is %p\n", &postman);
		
		printf("The value of counter is %d\n", counter);
		printf("The value of postman is %p\n", postman);
		
		printf("The value of the object postman points to is %d\n", *postman);
		
		return 0;
	}

[_View this source code on GitHub_](https://github.com/ottersoftware/SWwritings/blob/master/2012-10-04%20Double%20Indirection/2012-10-04-double-indirection-01.m)

The result of running this code is:

	The address of counter is 0x7fff53097bec
	The address of postman is 0x7fff53097be0
	The value of counter is 5
	The value of postman is 0x7fff53097bec
	The value of the object postman points to is 5

Because memory addresses can and do change between executions of code, if you run this code yourself the addresses will be different.

We can use the indirection operator to modify the value of the variable whose memory address the pointer variable is storing:

	int main(int argc, char *argv[]) {
		int counter = 5;
		int *postman = &counter;
		
		printf("The value of counter is %d\n", counter);
		printf("The value of the object postman points to is %d\n", *postman);
		
		*postman = 10;
	
		printf("The value of counter is %d\n", counter);
		printf("The value of the object postman points to is %d\n", *postman);
		
		return 0;
	}

[_View this source code on GitHub_](https://github.com/ottersoftware/SWwritings/blob/master/2012-10-04%20Double%20Indirection/2012-10-04-double-indirection-02.m)

results in:

	The value of counter is 5
	The value of the object postman points to is 5
	The value of counter is 10
	The value of the object postman points to is 10

Usually a variable supplied as an argument to a method is protected against change because the variable is passed by value which means that the method makes a copy of the variable being passed in rather than having accessing the variable in the calling method. However if we want the method to be able to change the value of a variable then we can use what we learnt above and this is called passing by reference. Rather than passing, say, an int we can pass an int pointer variable and this allows us to change the value of the int outside of the scope of the method;

	void decomposeSecondsIntoHoursMinutesAndSeconds(int timeAsSeconds, int *hours, int *minutes, int *seconds)
	{
		*hours = (timeAsSeconds / (60 * 60));
		timeAsSeconds = timeAsSeconds - (*hours * 60 * 60);
		
		*minutes = (timeAsSeconds / 60);
		timeAsSeconds = timeAsSeconds - (*minutes * 60);
		
		*seconds = timeAsSeconds;
	}
	
	int main(int argc, char *argv[]) {
		int timeAsSeconds = 5415;
		int hours, minutes, seconds;
		decomposeSecondsIntoHoursMinutesAndSeconds(timeAsSeconds, &hours, &minutes, &seconds);
		
		printf("%d seconds is the equivalent of %02d:%02d:%02d", timeAsSeconds, hours, minutes, seconds);
		
		return 0;
	}

[_View this source code on GitHub_](https://github.com/ottersoftware/SWwritings/blob/master/2012-10-04%20Double%20Indirection/2012-10-04-double-indirection-03.m)

Note that the main method's timeAsSeconds variable is passed to the decomposeSecondsIntoHoursMinutesAndSeconds method. Although the value is changed in decomposeSecondsIntoHoursMinutesAndSeconds, because it is a local copy (it was passed by value) the value of timeAsSeconds in main is not altered. Conversely, hours, minutes and seconds are passed by reference and so changes to them are reflected back in main.

From the above a method such as fileExistsAtPath:isDirectory: should cause you no problems. The method declaration is:

	- (BOOL)fileExistsAtPath:(NSString *)path isDirectory:(BOOL *)isDirectory

BOOL * should now make sense. Don't let the brackets confuse you. 'BOOL *' simply means that the method uses a pointer variable capable of pointing to a BOOL variable. If a similar variable was defined within the method it would look like this:

	BOOL *isDirectory;

This might make something click in the context of Objective-C. An Objective-C object such as NSString might actually be something to do with pointer variables. When I started writing Objective-C code my C knowledge was woefully lacking (it still is in many areas and you can read about that [here](http://swwritings.com/post/2012-09-11-learning-c-programming-and-code-runner)) and someone said to me that the asterisk before a variable's name could generally be understood to identify that variable as an object rather than a primitive. Therefore I'd blindly see _NSString *fullName_ as simply denoting that _fullName_ was an NSString object. However the proper explanation is that objects in Objective-C are pointers to C structures that contain the object's data; they are pointer variables and they simply store the address of the C structure that they represent.

For the sake of completeness, a C structure is simply a collection of variables. NSPoint (or CGPoint) is a collection of two CGFloat variables:

	typedef struct _NSPoint {
	      CGFloat x;
	      CGFloat y;
	} NSPoint;

So if we now consider a method signature such as the one below we might understand it better:

	- (BOOL)removeItemAtURL:(NSURL *)URL error:(NSError **)error

**error is simply a pointer variable capable of pointing to an NSError object and an NSError object is a pointer variable capable of pointing to a specific type of C structure.

We pass the method the address of an NSError object and then the method can alter it via indirection. Because the NSError object itself alters the underlying C structure via indirection we have double indirection.

Simple really.

---

I'd like to thank Uli Kusterer (whose web site is [http://zathras.de](http://zathras.de)) and Graham Lee ([@secboffin on Twitter](http://twitter.com/secboffin)) for technically editing this post and for both clarifying various points and helping me to focus on the core content and remove a lot of 'fluff'.
