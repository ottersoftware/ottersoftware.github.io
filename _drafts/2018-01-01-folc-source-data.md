---
date: 2018-01-01 12:14
title: FOLC - The Source Data
permalink: /post/2018-01-01-folc-source-data
categories: folc
layout: post
share: true
---

For an overview of the FOLC project please see the [Introduction](http://www.swwritings.com/post/2017-12-28-folc-introduction) post. To see all of the posts relating to the FOLC project please see the [article list](http://www.swwritings.com/categories/#folc).

---

## The Source Data
The FOLC application uses a variety of data sources and types. This post explains what the types of data are and some considerations about how they will be stored in the database and used.

## Birth, Marriage and Burial Records
These records are transcripts of original paper ledgers and they are stored in tables in Excel workbooks spread across Excel files (for births, marriages and burials) so they have a database-like structure already. However the data consistency and accuracy is an issue which is why a later process revolves around data validation and cleansing. However, in terms of the data model, the Excel data provides a good starting point.

However there are two questions which spring immediately to mind.

*Should the three sources of data be merged into a single entity or should they be kept separate?*

*How can related data be identified and linked?*







## Scanned Parish Records, Photographs and Maps
These are all images files which will need to have associated metadata such as the type of image, the title, date, contents, etc.

How the images are stored needs to be considered carefully. They could either be stored as image files on the computer and then the database stores paths to the images or the images could be converted into binary data and stored within the database itself.

Having images as separate files and storing the path to each in the database along with the other image metadata keeps the database smaller and means that the images can easily be accessed, backed up, etc. Storing the images as binary data means that no file management is necessary and the application cannot be broken by someone moving an image file but the database itself will be much larger and it isn't possible to simply browse through the image files.

Initially the image files will be kept as images stored in the file system rather than as binary data in the database. This will make it easier to ensure that everything is working properly and make backups and restores of the database much faster during development.

If I'd chosen to store the images in the database (or do so in the future) I would keep the image data and the image's metadata in separate tables so that it is easier to switch between the two storage types.

## Image Contents Metadata
The image type metadata will store the overall type of the image, whether it is a map, a scan of a parish record or a photograph for example. The image contents metadata will 'describe' the contents.

A scan of a page from a parish record will usually contain several entries and these need to be individually identified so that they can be related to people in the database. Therefore the contents metadata will contain a list of the people whose records appear in the scanned image. Similarly a scanned photograph may contain identifiable people and/or properties and maps too will contain identifiable items such as properties or graves of people.

It would probably be sufficient to simply create a relationship between images and people and places but it would be more useful if people and places can be related to parts of images. For example, if you are looking at a photograph of a group of people it would be ideal if the individuals are identifable. Similarly, a map of the village is more useful if individual properties are identifable.

To allow areas of images to be related to people and places a coordinates system is needed. Rather than using any absolute measurements such as centimeters a size and resolution-independent approach is needed.

If each image's width and height has an origin point of 0 and then the actual width and actual height are given values of 1 then any point inside the image can be defined as two values between 0 and 1. In the image below,  the position of point 'x' can be given as *0.5, 0.75*.

<figure class="half">
	<img src="{{ site.url }}/images/folc-source_data-01.png" alt="The Coordinates System">
	<figcaption>The Coordinates System</figcaption>
</figure>

This approach means that regardless of the zoom level used to view images, positions within those images remain consistent.

Two points can be used to define a rectangular area and by using rectangles and points it is possible to identify regions of images and relate them to people, places, etc.