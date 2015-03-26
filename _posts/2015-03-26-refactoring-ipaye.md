---
date: 2015-03-26 19:52
title: Refactoring iPAYE
permalink: /post/2015-03-26-refactoring-ipaye
categories: The iPaye Diaries
layout: post
share: true
---

Early last year I took over an app called iPAYE from its original developer. It is a UK tax calculator which not only allows you to calculate tax, national insurance, etc. as a one-off calculation but it also allows you to build up an entire year of playslips and handles Director's National Insurance which is a far from trivil calculation. There are both [iOS](https://itunes.apple.com/gb/app/ipaye-tax-calculator/id341368716?mt=8) and [OS X](https://itunes.apple.com/gb/app/ipaye-uk-tax-calculator/id455202188?mt=12) versions of the application.

Last week I finished updating both versions for the 2015-16 tax year which was a fairly major task because of new tax codes and some variations in how National Insurance is calculated. In fact I had to rope the original developer in to help me out. Whilst I was doing the updates I realised that the annual income from the apps was going to fall far short of the time I was investing in them which made me question whether the apps are sustainable.

iPAYE is currently a one-off payment. Once you have bought a copy for £1.49 ($1.99 for those of you following along in the US) you get each annual update for free and I receive the grand total of £0.87 ($1.40). There may be thousands or even tens of thousands of active users but it is only revenue from new customers which covers the annual update costs.

The obvious solution to this seems to be to make the annual updates an in-app purchase and this is where I think the app is heading. It is well suited to this because the one-off calculation element of the app, the Quick Tax calculator, could become the free feature (and there are plenty of free alternatives although they are of varying quality and accuracy) and you only pay if you want to access the payslips element.

However there are other problems which limit the lifespan of the apps. One of the reasons that the annual updates tend to be time consuming is that the architecture of the apps is less than optimal. Whilst the original author has a huge amount of experience in payroll software and a lot of experience of software development, iPAYE for iOS was his first ever Cocoa application and iPAYE for OS X was his second. This inevitably and understandably means that the codebase has rough edges. 

Some of the main issues are listed below but please understand that none of them are attacks on the abilities of the original author (iPAYE does some very complex calculations very well) but rather the sort of mistakes that a lot of new Cocoa developers tend to make.

* There are huge amounts of duplicate (or nearly duplicate) code. This includes the duplication of code between iPhone and iPad-specific view controllers and the duplication of code across the iOS and OS X platforms rather than it being stored in a sharable library or even sharable classes.
* On both platforms the data is stored in the NSApplicationDelegate and this is referenced from all over the place. This class is 1,475 lines long in the OS X codebase and 1,193 in the iOS codebase.
* The OS X version has pretty much all of the code in a single window controller class. This runs to 12,030 lines of code.
* The data models the iOS and OS X versions use are not compatible. You can't export data from one and import it into the other.
* The calculations are all done using floats rather than NSDecimalNumber. Rounding tricks resolve this but it's not ideal. The calculations are all also done in single, huge methods.
* The data model is flat and therefore 'fixed'. For example, the data model allows for six user-defined payment and four user-defined deductions (plus some core ones) so if a user wanted to add more types would be stuck. Similarly it can only cope with a Director having a fixed number of National Insurance code changes in a year (which is theoretically fine but still an artificial limit).
* There are lots of small, restrictive things. For example, iPAYE on iOS can go back to the 2009-10 financial year which is probably a bit unneccesary but because years are identifier on an index basis (2009-10 is year 0) it makes dropping old years less than simple since all references to subsequent year indexes would need to be found and updated.

In addition to the above the UI could do with a refresh, particularly on iOS. However the way the UI links in to the data model makes this a fairly major task in itself. At the moment things really are cobbled together to get the UI to work in iOS 8 and on the iPhones 6 and 6 Plus.

Last year I made some attempts to re-write bits of the calculation code but the new code didn't make it into the revisions for the 2015-16 financial year because the overall infrastructure of the apps still needs a lot of work to accommodate it. I've toyed with some new UI ideas for iOS but the work needed seems overwhelming and progress was slow.

However, whilst I was at NSConference a few weeks ago [Pieter Omvlee](https://twitter.com/pieteromvlee) gave a fantastic talk about how he and his team have evolved the [Sketch](http://bohemiancoding.com/sketch/) codebase over several years. Several ideas seemed very sensible to me and it also invigorated me and made me want to have another go at tackling the task of modernising iPAYE.

So this is where I am up to and I've decided to write a series of blog posts about the process. This will serve two main purposes and have a third as a result of them. The first is that it will allow me to share my thoughts, processes and ideas with other developers and hopefully get their feedback too. The second is that it will help keep me focused. I'll feel more motivated to keep plugging away at it if I am publicly accountable. The third is that even if iPAYE doesn't generate enough income to cover the refactoring costs the educational process for both myself and others will make it worthwhile. I'm intending to learn from this process and I hope others will too.

In my next post I'll outline what stages and in what order I think I will tackle things in.