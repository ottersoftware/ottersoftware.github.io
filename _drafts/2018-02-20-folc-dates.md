---
date: 2018-02-20 08:04
title: FOLC - Dates
permalink: /post/2018-02-20-folc-dates
categories: folc
layout: post
share: true
---

For an overview of the FOLC project please see the [Introduction](https://www.swwritings.com/post/2017-12-28-folc-introduction) post. To see all of the posts relating to the FOLC project please see the [article list](https://www.swwritings.com/categories/#folc).

---

## The Date Problem
Along with surnames, people are most likely to search for information by date and whilst they might want to search for records on a specific date allowing them to search for records which fall between two dates needs to be supported to add flexibility.

In itself this is fine but the quality of the information in parish records, particularly older ones, can be hugely varied. Issues such as legibility and damage to the actual records can limit how well the data can be transcribed and there is also the problem of missing data or partially complete data.

The partially complete dates are the problem that will be tackled here.

## Date Annotations
The dates in the transcribed records appear in a fairly standardised format which is designed to handle the issues around legibility.

Transcribers use asterisks and underscores to denote missing data.

An asterisk represents one or more missing date components (the day number and/or the month).

An underscore denotes a missing digit from the day number or the year.

Additionally the year component can span two years by use of a forward slash.

Some dates have an apostrophe at the start of them, presumably to force Excel into treating them as a string rather than a date.

Some dates are text strings such as 'no entry', 'blank', or 'crossed out'. These denote that the date is missing or that the entire line in the parish register has been voided. 

## Discovering The Annotations - A Technical Aside
When data is imported from the source spreadsheets it is important to know that all of the date values entered can be parsed and handled. This is achieved by setting up a set of [regular expressions](https://en.wikipedia.org/wiki/Regular_expression) and trying to match each date in the source data. If a date doesn't match then it needs to be categorised either as one which should be parsed (in which case a new regular expression is created for it) or it is an invalid piece of data (in which case the transcribers are asked to correct their spreadsheets).

This process produces a list of regular expressions and examples of the date strings which match them and this list can be used to ensure that all of the date strings can be parsed correctly when the spreadsheet data is imported.

## Sample Data
A set of date strings will be used to help explain how various date issues are handled. The sample dates are:

`* Dec 1649` is missing the day number.

`25 * 1649` is missing the month.
 
`16 May *` is missing the year.

`_2 Jan 1657/8` is missing the first digit of the day number.

`1_ Mar 1662/3` is missing the second digit of the day number.

`12 * 166_` is missing the month and the last digit of the year.

`* 1649/50` is missing the day number and the month and could be for one of two years.

This isn't an exhaustive list of examples of problematic dates but it gives a good representation of the issues and how they can be solved.

## Missing Values And Missing Ranges 
Dates can either be missing a set of specific values, missing a range of values, missing a set of ranges of values, or missing a range of sets of values.

This is more clearly shown by looking at the sample data items.

`* Dec 1649` represents a range of dates between 1 December 1649 and 31 December 1649.

`25 * 1649` could represent 25 January 1649, 25 February 1649, etc. This is an example of a missing number possibly representing a set of specific values.
 
`16 May *` is missing the year. In theory this is going to be a large set of specific values because the year could be anything from the start of the records to the end.

`_2 Jan 1657/8` could be 2nd of January 1657, 12th January 1657, 22nd January 1657, 2nd of January 1658, 12th January 1658, or 22nd January 1658. Again, this is a set of specific values.

`1_ Mar 1662/3` could be anything between 10 March 1662 and 19 March 1662 or 10 March 1663 and 19 March 1663. This is a set of ranges of values.

`12 * 166_` represents a range of a set of values because the date could be within the range of years 1660 and 1669 and in the set of the 12th of any month in those years.

`* 1649/50` could be any date between 1 January 1649 and 31 December 1650.

## Handling The Data
The search system needs to be designed to be able to handle the wide variety of dates which a partial date can match. The best way of doing this is to allow each parish record to be associated with multple dates.

