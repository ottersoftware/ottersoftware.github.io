---
date: 2013-05-04 17:41
title: Diacritics and FTS
permalink: 2013-05-04-diacritics-and-fts
categories: cocoa
layout: post
---

One aspect of FTS is that you need to 'sanitise' the text you are searching for. For example, if your source text was 'École' then you want it to be matched if the search term is something like 'ecole' or 'Ecole' or 'école'.

I've [previously touched on this](http://swwritings.com/post/2013-04-30-searching-for-speedy-searching#nsstringotscategory) where I suggested using a possibly flawed NSString category to strip diacritic marks from the text you were storing in an FTS table.

I've now been told about an alternative.

{{more}}

[Brendan Duddridge](https://twitter.com/tapforms) told me about how he is doing this in his [Tap Forms](http://www.tapforms.com). He uses SQLite's [unicode61 tokenizer](http://www.sqlite.org/fts3.html#unicode61) which 'does full unicode case folding according to rules in Unicode Version 6.1 and it recognizes unicode space and punctuation characters and uses those to separate tokens.' Essentially, it is possibly better than my otsNormalizeString method.

However Brendan did bring up one particular issue which is that it doesn't handle Chinese, Japanese, or Korean type languages because of the word boundaries. In those instances he falls back on slow LIKE queries unfortunately.

Anyway, if you do want to implement this then you do have to include your own build of SQLite in your project because the versions which are bundled with iOS and OS X don't have this feature enabled.

To enable it you need to get and include the SQLite amalgamation source in your project (covered in the slides available in [this blog post](http://swwritings.com/post/2013-04-15-searching-for-speedy-searching-prelude)) and add a new define:

	#define SQLITE_ENABLE_FTS4_UNICODE61

Then you need to enable it in your FTS table:

	create virtual table searchData using FTS4 (searchText, tokenize=unicode61)

You can find all of this in the [sample project](https://bitbucket.org/ottersoftware/fts-diacritic-marks/overview).

The code is fairly straightforward so I'm going to just jump straight to the results. If you don't use the unicode61 tokenizer then you'll get output such as this:

	Searching for école...
	... found it in 'French for school is école'
	Searching for ecole...
	... found it in 'French for school without diacritical marks is ecole'
	Searching for rustu...
	Searching for RuStŭ...

If you do use it then you get this:

	Searching for école...
	... found it in 'French for school is école'
	... found it in 'French for school without diacritical marks is ecole'
	Searching for ecole...
	... found it in 'French for school is école'
	... found it in 'French for school without diacritical marks is ecole'
	Searching for rustu...
	... found it in 'Eskişehir, Şımarık, Hasan Şaş, Rüştü Reçber'
	Searching for RuStŭ...
	... found it in 'Eskişehir, Şımarık, Hasan Şaş, Rüştü Reçber'

This seems like a good solution albeit at the expense of including your own copy of SQLite in your project.

---

I’m [@sgaw on Twitter](http://twitter.com/sgaw), [@sw on App.net](https://alpha.app.net/sw) and you can also email me at simon at this domain.