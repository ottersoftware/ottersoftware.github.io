---
date: 2019-07-15 20:51
title: Functional Programming (Over?) Simplified
permalink: /post/2019-07-15-functional-programming-over-simplified
categories: elixir
layout: post
share: true
---

## Introduction

Almost a year ago I wrote a post called [Five Great Things About Elixir](https://www.swwritings.com/post/2018-08-17-five-great-things-about-elixir). When I wrote that post I didn't imagine that fewer than 12 months later I would be writing Elixir for a living and that quite a few people who are still macOS and iOS developers would be asking me about Elixir and about functional programming itself. I may well dig into Elixir in a bit of detail  in future blog posts but this one is about functional programming and it will hopefully be a bit clearer than the very brief overview I gave in last year's post. In fact I am hoping to make it as jargon-free as possible.

## Data

In object-orientated programming you construct code around the idea of objects which have properties and behaviours. You could create a car object whose fuel level is a number between 1 (full) and 0 (empty). You can read that value but not directly change it. The fuel level value is changed by using various commands such as drive and refuel.


 and one of its behaviours is to indicate. When you indicate you can tell the car to indicate left or right and when you do so a property which is read-only will tell you   When you accelerate the speed property might change from zero to 60. The car keeps track of its own properties; if you want to know something about the state of the car you ask the car. If you want to change a property you usually tell the car to do something (although you can also change properties directly if they are exposed). The car is the thing you interact with.

In functional programming you construct code around the idea of