---
date: 2018-01-01 14:28
title: FOLC - Wireframes
permalink: /post/2018-01-01-folc-wireframes
categories: folc
layout: post
share: true
---

For an overview of the FOLC project please see the [Introduction](https://www.swwritings.com/post/2017-12-28-folc-introduction) post. To see all of the posts relating to the FOLC project please see the [article list](https://www.swwritings.com/categories/#folc).

---

## Wireframing
For the FOLC project I needed to provide some wireframes fairly early on in the process as part of one of the tenders for obtaining charitable funds.

Wireframing is the process of creating an outline version of how an application will look and, to a degree, behave. It helps detemine what information will be displayed on each screen and therefore how the application and its data might need to be structured. Before any code is written they are useful for getting feedback from end users, from the developers, from designers (for user experience as well as the user interface) and anyone else who has a vested interest in the software and needs a very early, very bare-bones mock-up of it.

There are various tools you can use for wireframing but at the moment my tool of choice is [Mockups 3 by Balsamiq](https://balsamiq.com/products/).

## An Example Workflow
To put the wireframes into context it is helpful to construct example workflows of how the application might be used. Below is one, based on someone called Edgar Padfield who appears both in the records and also in several photographs.

1. Someone searches for the name ‘padfield’. The system searches in the database and returns a list of people whose name contains that search term along with any dates or birth or death. Amongst the list is Edgar Padfield.
2. To see more information about Edgar, the person clicks on the name in the list and they are then shown a screen of information about him. This comprises information from the transcribed parish records and any other available sources such as censuses, text from his gravestone, information about his burial plot, etc.
3. The same screen contains a list of related photographs. The items in the list can be clicked to show the photo. The photos might include things like his gravestone, the house where he used to live, a picture of him, etc.
4. Additional links are also provided. The position of Edgar’s grave is known so a link to the churchyard map is available which, when clicked, shows the map along with details of his grave’s position. Similarly, the address of a house he lived in is known so there is a link to a map showing the location of the house.
5. Known relatives exist in the database too so the main page of information about Edgar links to them allowing quick and easy access to their information pages too.

## The Home Page
The application’s home page will provide some information about the application and what it does. It will also show some brief instructions to get someone new to it started. A random image from a pool of pre-selected images will be shown. This can be clicked to show the image's information page.

<figure class="half">
	<img src="{{ site.url }}/images/folc-wireframes-01.png" alt="The Home Page Wireframe">
	<figcaption>The Home Page Wireframe</figcaption>
</figure>

## Search Results
The search results show people and images which match the search term. The result are clickable links.

<figure class="half">
	<img src="{{ site.url }}/images/folc-wireframes-02.png" alt="The Search Results Wireframe">
	<figcaption>The Search Results Wireframe</figcaption>
</figure>

## People
The person details screen shows all of the available information for a person. In some cases this might include some narrative and in others it may simply be a birth, marriage or death date.

Where possible links to things like a scanned parish record image or details about their grave will be provided.

<figure class="half">
	<img src="{{ site.url }}/images/folc-wireframes-03.png" alt="The Person Details Wireframe">
	<figcaption>The Person Details Wireframe</figcaption>
</figure>

## Images
The image information screen will display an image, its title and if any other notes related to it. In addition, if it is of a known location then a map may be displayed as will any known other people associated with the photograph or location. If the photograph is of a person, relatives and other photographs they are linked to may be listed.

<figure class="half">
	<img src="{{ site.url }}/images/folc-wireframes-04.png" alt="The Image Information Wireframe">
	<figcaption>The Image Information Wireframe</figcaption>
</figure>

## Graves
Where details about a grave exist a photograph may be shown as well as the location of the grave in the churchyard.

<figure class="half">
	<img src="{{ site.url }}/images/folc-wireframes-05.png" alt="The Grave Information Wireframe">
	<figcaption>The Grave Information Wireframe</figcaption>
</figure>
