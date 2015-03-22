---
date: 2013-04-30 12:00
title: Searching For Speedy Searching
categories: cocoa
layout: post
share: true
---

At [NSConference 5](http://nsconference.com) I presented a talk entitled 'Searching for Speedy Searching'. The talk was about how you can use SQLite's [Full Text Search](http://www.sqlite.org/fts3.html) (FTS) to add full text searching to your Cocoa applications quickly and easily.

A week ago I published a [blog post](http://swwritings.com/post/2013-04-15-searching-for-speedy-searching-prelude) where I made the slides I used in my talk available and posted a couple of corrections to things I'd said. The main purpose of this post is to go into detail about implementing FTS in an iOS application which uses Core Data. All of the basics will be covered but you still might want to take a look at the slides for a more detailed overview.

##<a id="contents"></a>Contents

1. [The Problem](#theproblem)
2. [The Solution](#thesolution)
3. [Third-Party Elements](#thirdpartyelements)
4. [The Demo App](#demoapp)
5. [Maintaining The Search Database](#maintenance)
6. [Alternatives](#alternatives)
7. [Conclusion](#conclusion)
8. [Thanks](#thanks)

##<a id="theproblem"></a>The Problem

Core Data is great for many things. One of the things it's not so great for is searching for text inside text. Searches based on predicates such as the one below tend to be slow:

	title contains[dc] $searchValue

The above would search a title property for a search value, ignoring diacritic marks and case sensitivity. The reason that it is slow is that each managed object is parsed in turn and a regex search performed against the title property.

You can speed up the search *marginally* by pre-converting the text that you want to search to lowercase and stripping out diacritic marks but it's really not much of an improvement and each managed object is checked in turn.

What is faster is checking the start of the text rather than within it. This is a trick that Apple uses in applications such as  Contacts in iOS. Launch it and search for yourself but miss off the first character of a name. The search won't find what you're looking for.

A predicate to search the start of a property might look like this:

	title beginsWith $searchValue

This is an efficient search and it is fast. You can make it even faster by using comparison operators which are faster than a regex search. [WWDC 2010](https://developer.apple.com/videos/wwdc/2010/) session 118, Mastering Core Data, goes into this in more detail so please refer to that if you want to know more.

However there is a problem with this too. It is fast but it's not flexible. What happens if you want to search for a string *in* a property rather than just searching for a match at the beginning of the property?

The aforementioned WWDC session suggests splitting the contents of the property into distinct words, storing these in a related entity, searching that and then using the relationship to get back to the original managed objects. The theory's good but it's horribly slow and you have to maintain the search entity.

Additionally, if you want to search for multiple terms rather than an exact phrase then you have to start using compound predicates. It's do-able but it's yet more complexity.

So Core Data isn't ideal for this particular job. However there is something that is and it doesn't involve any more work than maintaining a keywords entity in Core Data. It is fast, it is flexible and it is surprisingly easy to implement.

[Back to the contents list](#contents)

##<a id="thesolution"></a>The Solution

SQLite is the solution to the searching problem, more specifically SQLite's [Full Text Search](http://www.sqlite.org/fts3.html) or FTS for short.

Typically Core Data can use SQLite as a persistence store type but you can't currently use FTS from within Core Data. Fingers crossed that might change but until then this blog post should give you some ideas about what you can do to work around this limitation.

It is important to remember that the application will still use Core Data and that the SQLite FTS element is a separate database file used solely for searching. We are not modifying the Core Data database file itself.

[Back to the contents list](#contents)

##<a id="thirdpartyelements"></a>Third-Party Elements

To use FTS you need to have a build of SQLite with FTS enabled. Fortunately recent builds of iOS and OS X include bundled SQLite libraries with everything you need enabled. I have an iPhone 3GS running iOS 5.1 and I can confirm that this version of iOS contains SQLite with FTS enabled. 

> I've now created [test apps](https://bitbucket.org/ottersoftware/fts-test/overview) for iOS and OS X which you can use to see if FTS if enabled in a particular OS version. You can read a bit more about that in [this blog post](http://swwritings.com/post/2013-05-04-testing-for-fts-in-sqlite).

If however you want to do this on older operating systems which don't you should refer to [my presentation slides](http://swwritings.com/post/2013-04-15-searching-for-speedy-searching-prelude) for information about how to include SQLite in your application and how to enable FTS, multi-threading and the advanced querying engine.

What you will definitely need if you want to make your life easier is Gus Muller's [FMDB](https://github.com/ccgus/fmdb). 

FMDB is an Objective-C wrapper which makes working with SQLite database easier. It abstracts away some of the more gnarly bits and, importantly for the purposes of this article, it makes working with SQLite on multiple threads almost trivial.

To use FMDB you simply need to grab the source files from Github and include them in your project.

[Back to the contents list](#contents)

##<a id="demoapp"></a>The Demo App

You can find the demo application in my [FullTextSearch repository](https://bitbucket.org/ottersoftware/fulltextsearch) on [Bitbucket](https://bitbucket.org). Note that the repository is somewhat bloated by the inclusion of a 32 MB database file which contains TV series and episode data used to test the searching against.

The project was created in Xcode 4.6.2 and the code creates a universal application which will run on devices running iOS 5.1 or higher.

<a id="demoappcontents"></a>Because there is a lot going on in the application I'm going to break it all down into areas which will hopefully make it all easier to follow.

* [First Run & Testing The Searching](#firstrun)
* [The Project](#theproject)
* [OTSAppDelegate](#otsappdelegate)
* [OTSMainViewController](#otsmainviewcontroller)
* [OTSDetailViewController](#otsdetailviewcontroller)
* [NSString+OTSCategory](#nsstringotscategory)

#####<a id="firstrun"></a>1. First Run & Testing The Searching

The first time you run the code you'll have to be patient because the search database is built. This is a first-run process and it takes a while because it's processing a lot of data. Keep an eye on the debug area in Xcode to see how the process is going.

Once the search database has been populated you are presented with a table of TV show titles. There are 6,330 of them. Tap on a show to see its episodes. Across the shows there are 336,322 episodes.

Now try a search. If you just enter one or two letters it might take a little while for the app to find the matches. The more characters you enter the faster it is.

Searching is live so matches are found as you type. Importantly the user interface remains responsive during searching so you can amend the search criteria as searches are occurring.

Once some search results have come back you might notice that the search term is not part of a show title. Tap on a show to see the episodes and, if there are a lot of them, tap on the top-right magnifying glass button to filter the episodes just to show the matches.

Play around with it so you have a feel for how the application behaves.

[Back to the demo app contents list](#demoappcontents)

#####<a id="theproject"></a>2. The Project

There are a few things to note about the project itself.

* The Link Binary With Libraries build phase for the FTS target includes the libsqlite3.dylib static library. If you want to use your own build of SQLite then this can be removed.
* Related to the previous item, the project contains a group called SQLite. This actually includes the two SQLite source files you need if you want to use your own build of SQLite. However they are not included in the FTS target so if you want to use them be sure to change this in the Target Membership section in the File Inspector for the sqlite3.c file. These files can be removed if you are using the static library supplied by Apple.
* The project includes a target for [mogenerator](http://rentzsch.github.io/mogenerator/). This is not important for the demo app but gives me an excuse to say that if you're not using it then you should think about it. Mogenerator abstracts away some of the complexities when using Core Data but for me the most useful thing is that it generates NSManagedObject subclasses in such a way that you can edit them, change the managed object, re-generate the sub-classes and not lose any of the code you wrote.
* The Model group contains a file called FTSSourceData.coredata. This is the pre-populated database that contains the shows and episodes.

[Back to the demo app contents list](#demoappcontents)

#####<a id="otsappdelegate"></a>3. OTSAppDelegate

* The code in application:didFinishLaunchingWithOptions: is fairly standard. The only notable element is that at the top of the method we are copying the Core Data database which is included in the application's bundle into the application's Library folder.

		NSURL *dataFileURL = [[[NSFileManager defaultManager] URLsForDirectory:NSLibraryDirectory inDomains:NSUserDomainMask] lastObject];
		dataFileURL = [dataFileURL URLByAppendingPathComponent:@"FTSSourceData.coredata"];
		if (![[NSFileManager defaultManager] fileExistsAtPath:[dataFileURL path]]) {
			NSURL *bundleFileURL = [[NSBundle mainBundle] URLForResource:@"FTSSourceData" withExtension:@"coredata"];
			[[NSFileManager defaultManager] copyItemAtURL:bundleFileURL toURL:dataFileURL error:nil];
		}

* The initializeCoreDataStack method is also pretty standard. Once the Core Data stack has been constructed the buildFTSDatabase method is called. It is called on the main queue because it contains some code which can interact with the user interface.
* buildFTSDatabase is where the FTS database is created and populated with the data used for searching. If the FTS database exists  then the method simply sets the managedObjectContext property of OTSMainViewController (more on that below) and returns.

		NSURL *dataFileURL = [[[NSFileManager defaultManager] URLsForDirectory:NSLibraryDirectory inDomains:NSUserDomainMask] lastObject];
		dataFileURL = [dataFileURL URLByAppendingPathComponent:@"FTSSearchData.sqlite"];
		if ([[NSFileManager defaultManager] fileExistsAtPath:[dataFileURL path]]) {
			[[self mainViewController] setManagedObjectContext:[self managedObjectContext]];
			return;
		}

* If the FTS database does not yet exist then the application creates and populates it. So that the UI has time to display a progress indicator the actual code to do this is performed in a block.

		dispatch_queue_t private_queue = dispatch_queue_create("com.ottersoftware.ios.fts", 0);
		dispatch_async(private_queue, ^(void) {
			...
		}

* The progress overlay is placed into OTSMainViewController's hierarchy on an iPhone or iPod touch and into the OTSDetailViewController's view on an iPad. This is because OTSMainViewController's view can be hidden on an iPad in the portrait orientation.

		if ([[UIDevice currentDevice] userInterfaceIdiom] == UIUserInterfaceIdiomPhone) {
			[[[self mainViewController] view] addSubview:progressView];
		} else {
			[[[[self mainViewController] detailViewController] view] addSubview:progressView];
		}

* The actual population of the SQLite database is pretty straightforward. FMDB is used to create and open the database file and then the FTS virtual table is created.

		FMDatabase* db = [FMDatabase databaseWithPath:[dataFileURL path]];
		dbSuccess = [db open];
		ZAssert(dbSuccess, @"Could not open db.");
		
		dbSuccess = [db executeUpdate:@"create virtual table searchData using FTS4 (seriesID, episodeID, searchText)", nil];
		ZAssert(dbSuccess, @"Didn't create table: %@", 	[db lastErrorMessage]);

* A fetch request retrieves the series from the application's database. Each of which and the episodes belonging to it are processed and their identifier and name are stored in the FTS database.

		NSFetchRequest *request = [NSFetchRequest fetchRequestWithEntityName:@"Series"];
		NSArray *results = [[self managedObjectContext] executeFetchRequest:request error:nil];
		if (results) {
			for (NSManagedObject *seriesMO in results) {
				DLog(@"Processing item %d of %d", [results indexOfObject:seriesMO] , [results count]);
				@autoreleasepool {
					[db beginTransaction];
					dbSuccess = [db executeUpdate:@"insert into searchData (seriesID, searchText) values (?, ?)", [seriesMO valueForKey:@"seriesIdentifier"], [[seriesMO valueForKey:@"seriesName"] otsNormalizeString], nil];
					ZAssert(dbSuccess, @"Didn't create series record: %@", 	[db lastErrorMessage]);
					
					for (NSManagedObject *episodeMO in [seriesMO valueForKey:@"episodes"]) {
						dbSuccess = [db executeUpdate:@"insert into searchData (seriesID, episodeID, searchText) values (?, ?, ?)", [seriesMO valueForKey:@"seriesIdentifier"], [episodeMO valueForKey:@"episodeIdentifier"], [[episodeMO valueForKey:@"episodeName"] otsNormalizeString], nil];
						ZAssert(dbSuccess, @"Didn't create episode record: %@", 	[db lastErrorMessage]);
					}
					[db commit];
				};
			}
		}

> One thing to note is that when I originally investigated this whole process I was storing the objectID of each managed object in the FTS database. However I then learnt that objectIDs can change during a migration which means that you either need to re-created the FTS database after a migration or generate and store your own object identifiers.

* Each managed object is given an identifier which remains static. These are the IDs which are stored in the FTS database and provide the link back to the managed objects themselves.

	The IDs are generated using iOS 6's
	
		[[NSUUID UUID] UUIDString]
 
 If you are using iOS 5 then you will need to use [an alternative method](http://stackoverflow.com/questions/427180/how-to-create-a-guid-uuid-using-the-iphone-sdk) to generate unique IDs.
 
* Additionally, the search text which is being stored is sanitised. This is done using an NSString category called [NSString+OTSCategory](#nsstringotscategory).
*  Once the FTS database has been populated it is closed, the progress overlay is removed from the screen and the managedObjectContext property of OTSMainViewController is set.

[Back to the demo app contents list](#demoappcontents)

#####<a id="otsmainviewcontroller"></a>4. OTSMainViewController

* OTSMainViewController contains a public property which allows an NSManagedObjectContext to be passed to it. This is called either when the FTS database has been populated or when the application detects that it already exists. The setter for the property is over-ridden and this is used as a jumping-off point for setting up the data management in this view.
* Private properties are used to store an array of unfiltered media items (this could easily be an NSFetchedResultsController), an array of the filtered media items, the current search string and the path to the FTS SQLite database file.
* The class is initialised using a custom initialiser, initWithAppropriateNib, which does some basic setup. I use variants of this a lot, particularly where I need to load iPhone and iPad-specific XIBs.
* An NSFetchRequest is then used to populate the unfilteredMediaItems array and the table view is refreshed to show all of the series records in the Core Data database.
* The UITableViewDataSource methods are pretty standard. However they do check to see if a managed object context exists so that we don't have any problems before the application's delegate class passes one in. They also check to see if the search bar contains a string. If it doesn't then the unfilteredMediaItems array is used to populate the table. If it does then the filteredMediaItems array is used.
* The UITableViewDelegate method tableView:didSelectRowAtIndexPath: is used to retrieve the managed object underlying the selected table row and pass it into OTSDetailViewController, creating OTSDetailViewController first if necessary. The current search string is also passed in so that the episodes shown in the detail view can themselves be filtered. In a shipping application you'd probably not need to do this.
* The really interesting method in this class is performSearch. It can be triggered by two UISearchBarDelegate methods: searchBar:textDidChange: and searchBarSearchButtonClicked:.
* The first thing this method does is check to see if a search should be performed. If one shouldn't then various properties in OTSMainViewController and OTSDetailViewController are cleared and then the table view is reloaded.

		if ([[[self searchBar] text] length] == 0) {
			[self setCurrentSearchString:nil];
			if ([self detailViewController]) {
				[[self detailViewController] setSearchString:nil];
				[[self detailViewController] setSeriesManagedObject:nil];
			}
			[[self seriesTableView] reloadData];
			[[self searchActivityIndicatorView] stopAnimating];
			return;
		}

* If there is a search string then an asterisk is appended to it so that we are performing a wildcard search:

		NSString *searchString = [NSString stringWithFormat:@"%@*", [[self searchBar] text]];

	This means that if the search text was 'banan' then the search is for 'banan\*' which would match 'banana'. Similarly a search for 'green do' would be converted to 'green do\*' so phrases containing the word green and words beginning with do would be found.
* Next the search string (including the asterisk) is stored in the currentSearchString property and it is also set in detailViewController if an instance of OTSDetailViewController has been created.
* A potentially delayed dispatch queue is then created:

		dispatch_queue_t backgroundQueue = dispatch_queue_create("com.ottersoftware.ios.fts.search", NULL);
		int64_t delay = 0.5;
		if ([[[self searchBar] text] length] > 3) {
			delay = 0.0;
		}
		dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW, delay * NSEC_PER_SEC);
		dispatch_after(time, backgroundQueue, ^(void){
			...
		});

	The idea behind this is that if only the first three characters of the search string have been typed then we introduce a slight delay to allow more to be entered because, if possible, we want to avoid searching against really short strings because they return a lot of results. Three is a completely arbitrary number I picked so you could always have the delay, never have one, or change the length of it.
* Regardless of whether there is a delay or not the first thing the code does in the block is check to see if the search string has changed. If it has then the method returns and the search is aborted:

		if (![[self currentSearchString] isEqualToString:searchString]) {
			return;
		}

	This check is performed several times in the block to allow us to abandon a search if the search string has changed. Why waste time looking for matches when we're not going to be using them for anything?
* If the search is to be performed then we use an FMDatabaseQueue block to perform the search:

		FMDatabaseQueue *queue = [FMDatabaseQueue databaseQueueWithPath:[self databasePath]];
		[queue inDatabase:^(FMDatabase *db) {
			...
		}];

* Inside that block we create an NSMutableSet to store found object identifiers. We use a set so that duplicates are removed.

		NSMutableSet *foundObjectIdentifiers = [[NSMutableSet alloc] init];

* The query where we look for matches in the FTS SQLite database is standard stuff. Just make sure that you use the MATCH keyword to ensure you are performing an FTS search:

		FMResultSet *rs = [db executeQuery:@"SELECT DISTINCT seriesID FROM searchData WHERE searchText MATCH ?", searchString, nil];
		while ([rs next]) {
			if (![[self currentSearchString] isEqualToString:searchString]) {
				[rs close];
				return;
			}
				
			[foundObjectIdentifiers addObject:[rs stringForColumn:@"seriesID"]];
		}
		[rs close];

	Again we check that the search string hasn't changed.

		if (![[self currentSearchString] isEqualToString:searchString]) {
			return;
		}
		
	> Huge thanks to [Dan Messing](https://twitter.com/danmessing) of [Stunt Software](http://stuntsoftware.com) for a suggestion on how to speed up the code that originally followed. I further refined his idea and removed a couple of steps which were probably unnecessary. Slightly unscientifically I can say that a search for 'elephant' on an iPhone 3GS was taking around 4-5 seconds. It's now takes around half a second.
	
* Now that we have a set of objectIDs we can retrieve the actual managed objects on the main thread and display the results:

		dispatch_async(dispatch_get_main_queue(), ^{
			NSError *error;
			NSFetchRequest *fetchRequest = [NSFetchRequest fetchRequestWithEntityName:@"Series"];
			[fetchRequest setPredicate:[NSPredicate predicateWithFormat:@"seriesIdentifier IN %@", foundObjectIdentifiers]];
			NSSortDescriptor *sd = [NSSortDescriptor sortDescriptorWithKey:@"seriesName" ascending:YES];
			[fetchRequest setSortDescriptors:[NSArray arrayWithObject:sd]];
			[self setFilteredMediaItems:[[self managedObjectContext] executeFetchRequest:fetchRequest error:&error]];
			if (![self filteredMediaItems]) {
				DLog(@"%@", error);
				return;
			}

			if (![[self currentSearchString] isEqualToString:searchString]) {
				return;
			}
			
			[[self seriesTableView] reloadData];
			[[self searchActivityIndicatorView] stopAnimating];
		});

	Here we are using an NSFetchRequest to retrieve managed objects which have the found object identifiers in the seriesIdentifier property. We're sorting the results  and storing them in the filteredMediaItems property. We perform a final check to make sure that the search criteria has not changed and if it hasn't the table is then refreshed to show the found items data.

[Back to the demo app contents list](#demoappcontents)

#####<a id="otsdetailviewcontroller"></a>5. OTSDetailViewController

* OTSDetailViewController has two public properties. One is for the series managed object whose episodes need displaying. The other is for the search string (if one is being used).
* There are two private properties. filterEpisodeSearchResults is a flag to determine whether the episodes are filtered to show items which match the search criteria. episodeManagedObjects is an array used to hold the episodeManagedObjects which will be displayed.
* The two public properties are both overridden so that when they are set the configureView method is invoked. The configureView method is also called when the view loads and when the toggle is applied to show either all episodes or just those matching the search criteria.
* The configureView method does a few things. It sets the title on the navigation bar and, if there is a seriesManagedObject it sets the contents of the episodeManagedObjects array to the series' episodes after sorting them by episode number. Finally, if there is a seriesManagedObject, and if there is a searchString, and if the episode table should be filtered, it calls the performSearch method. Otherwise the table is simply refreshed.
* The performSearch method is very similar to the approach used in OTSMainViewController. It opens the FTS database and, on a background dispatch queue, it then queries the FTS database for episodes in the selected series that match the search criteria. Back on the main thread the corresponding managed objects are retrieved and the table refreshed.

[Back to the demo app contents list](#demoappcontents)

#####<a id="nsstringotscategory"></a>6. NSString+OTSCategory

* Confession time. I'd assumed that the code in my category would strip diacritic marks and normalise the text. The code comes from the WWDC 2010 sample code and specifically the DerivedProperty Mac project. However, a web search whilst writing this post lead me to a [StackOverflow article](http://stackoverflow.com/questions/9376621/folding-normalizing-ligatures-e-g-to-ae-using-corefoundation) where it seems that this is all a bit of a minefield. This category may not be as great as I'd thought. If you do have problems with it, let me know. Even better, let me know with a better solution!

> I've published a [blog post](http://swwritings.com/post/2013-05-04-diacritics-and-fts) which presents an alternative approach although that too has some flaws.

[Back to the demo app contents list](#demoappcontents)

[Back to the contents list](#contents)

##<a id="maintenance"></a>Maintaining The Search Database

When you have a separate FTS database you need to keep it in step with your application's primary database. If you are using Core Data then you can tap into something like NSManagedObjectContext's NSManagedObjectContextObjectsDidChangeNotification or NSManagedObjectContextDidSaveNotification. Both of these provide sets of NSManagedObjects which have been inserted, changed and deleted. You can use these to add new items to the FTS search database, edit existing items or delete obsolete ones.

[Back to the contents list](#contents)

##<a id="alternatives"></a>Alternatives

[Alastair Houghton](https://twitter.com/alastairh) has [suggested](https://alpha.app.net/alastair/post/5202527) [Whoosh](http://pythonhosted.org/Whoosh/) as an alternative approach, maybe via the Python-ObjC bridge. It would work for non-SQLite Core Data too.

##<a id="conclusion"></a>Conclusion

FTS is great but it is not a magic bullet which will miraculously add fast searching functionality to your applications. There are always overheads and complexities involved in using a solution such as the one presented here but if you need to search a lot of data quickly and easily then I highly recommend it to you an an option worth investigating.

If after playing around with FTS you discover something new or realise that I've got something wrong in the article please do get in touch so I can update this post.

[Back to the contents list](#contents)

##<a id="thanks"></a>Thanks

A huge thank you to [Dan Messing](https://twitter.com/danmessing) of [Stunt Software](http://stuntsoftware.com) for a suggestion on how to speed the code where object IDs from the SQLite database are matched to Core Data managed objects. It made a vast difference to search performance.

I would also like to thank the following people for very kindly proof-reading this article:

* [Alan Morris](https://twitter.com/dippigu) of [Little Red Door](http://www.littlereddoor.co.uk) who also has [his own blog](http://www.littlereddoor.co.uk/ios/blog/).
* [Christian Klotz](https://twitter.com/christianklotz) (who I also need to thank for helping me out with a localisation issue recently).
* [Romain Briche](https://twitter.com/romainbriche) who should be followed on Twitter because he posts links to amazingly useful development articles.
* [Simon Harris](https://twitter.com/haruki_zaemon) whose blog can be found at [http://www.harukizaemon.com](http://www.harukizaemon.com).
* [Steve 'Scotty' Scott](https://twitter.com/macdevnet) who runs [iDeveloper.tv](http://ideveloper.tv), [NSConference](http://nsconference.com) and the brand new [Software Indie](http://softwareindie.com).

[Back to the contents list](#contents)
