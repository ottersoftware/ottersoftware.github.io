---
date: 2015-03-27 00:04
title: iPAYE - The Plan of Attack
permalink: /post/2015-03-27-ipaye-plan-of-attack
categories: iPAYE
layout: post
share: true
---

**Important:** Please read the news about my decision to finally [retire iPAYE](https://www.swwritings.com/post/2015-10-15-retiring-ipaye/).

---

In [my last post](https://www.swwritings.com/post/2015-03-26-refactoring-ipaye/) I explained that I was going to undertake a major refactoring task to modernise and improve an application which is split over two platforms (iOS and OS X) and three device types (Mac, iPad and iPhone).

In this post I outline how I am going to approach this task and hopefully make it manageable. I'm expecting some revisions to this post and I'll try to devise a way to make them clear and reviewable so that it is easy to see what changed, when and why.

---

## The Overall Approach

At the moment the data model is intertwined with the user interface and also with some of the calculations. This means that it is not possible, as far as I can tell, to make initial sweeping changes in any of those three areas. Therefore my plan is to take two passes at the data model since that is the creeper intertwined amongst everything.

I am intending on building Swift application but I am also anticipating that I may have to include Objective-C classes too. There is a risk that subsequent releases of Swift will necessitate a lot of code re-writing or at least fixing I am hoping that WWDC will mark the beginning of more stability for the language.

## The Data Model - Phase 1

The first pass at the data model will involve creating a model which will be used in both the iOS and OS X apps.

The ApplicationDelegate will no longer be responsible for storing the data but rather this responsibility will move into a new DataManager class which will be consciously  passed between classes.

The data model will be extended so that elements such as payments and deductions are added as separate objects but don't have to be used immediately. This means that as refactoring takes place they can be phased in.

Migration code to move data from the current iOS and OS X versions will not be written until the second phase because the entire refactoring process can be done with new data.

## The Common Support Structure

iPAYE relies on some core supporting components which need to be built and then be maintainable in a sensible way. These include:

* The list of financial years.
* The list of valid tax codes in each financial year.
* The list of valid National Insurance codes in each financial year.
* The annual tax, National Insurance, Student Loans, etc. amounts and percentages used in the calculations.
* Functions to check the validity of payment dates and which period payment dates fall within.
* Report generation.
* Data import and export.
* In-App Purchases.

## The Calculations

This is the big one! The huge single method calculation routines need to be split into smaller routines which are easier to test and easier to maintain. Unit tests will be used extensively here and the plan is to use the current codebase to generate test data which will then be used to check the new code. This will allow thousands of tests to be created if necessary and ensure that ares where HMRC do not provide test data are properly covered.

The calculations will be split roughly into the following areas:

* Tax calculations for the standard pay periods using gross to net calculations covered by HMRC test data.
* Tax calculations for the standard pay periods using net to gross calculations covered by iPAYE-generated tests.
* Tax calculations (gross to net and net to gross) for the additional pay periods (such as Week 53) covered bt iPAYE-generated tests.
* National Insurance calculations using the exact calculation method covered by HMRC test data.
* National Insurance calculations using the table calculation method covered by iPAYE-generated tests.
* Director's National Insurance calculations using the exact calculation method covered by HMRC test data.
* Director's National Insurance calculations using the table calculation method covered by iPAYE-generated tests.
* Student Loan calculation covered by HMRC test data.
* Complete payslip calculations (for single payslips and for a series of payslips) covered by iPAYE-generated tests.

## Decision Time

WWDC 2015 will have taken place by now so, if necessary, iPAYE can use OS X 10.11 and iOS 9 as the base versions if there are compelling reasons to do so. None of the above work is likely to impacted by anything new in the operating systems (other than perhaps Swift changes) but the next stages might be.

## The Mac User Interface

The new Mac user interface will be tackled first because it's actually a simpler task than re-doing the iOS UI. This process will also be used to help refactor elements as and when necessary to optimise code resuse between iOS and OS X.

## The iOS User Interface

By this point the creation of the iOS user interface should be fairly straightforward with lots of the glue code, or at least the theory, formulated after working on the OS X version.

## The Data Model - Phase 2

By this stage the applications should be in a state where the data model is not intertwined with the view controllers, calculations, etc. so it will be possible to finalise the data model and write migration code to move data from the current iOS and OS X versions. It is also at this point that I can decide if iPAYE will use Code Data and iCloud syncing or another mechanism entirely (again this is a nice decision to make after WWDC 2015).

## The Finishing Touches

This stage includes things like how the tax rate information which ships within iPAYE can be maintained easily and what the help documentation consists of and how it can be generated.