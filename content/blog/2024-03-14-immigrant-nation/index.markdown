---
title: "A Nation of Immigrants"
subtitle: 
excerpt: "Mapping America's diversity using ACS data"
date: 2024-03-14
author: 
draft: false
images:
series:
tags:
categories:
layout: single
---





For the past few weeks I've been going deep into the data from the US Census Bureau's American Community Survey (ACS). This whole project started with me trying to recreate a [dot plot map of New York's immigrant communities from NYU](https://furmancenter.org/thestoop/entry/mapping-the-melting-pot-where-foreign-born-new-yorkers-live). As someone new to working with Census data (and who works with incredibly sparse population data for developing countries in my day job), I was pretty blown away at the level of detail included in the data. It's an amazing resource that seems to be underutilized. 

That type of rich data is also a playground for dataviz. In this post I wanted to use the ACS data on the foreign-born population to recreate a technique I saw from Nathan Yau at Flowing Data recently. Yau has a knack for taking bizarre questions and then using data viz to u tell an interesting story. In this [post](https://flowingdata.com/2024/02/28/mcdonalds-locations-vs-golf-courses/), he asks if there are really more golf courses than McDonald's locations in America. It turns out this is true (and also slightly terrifying given how much water a golf course requires). To visualize the locations that have more golf courses than McDonalds he uses a technique called a Dorling Cartogram. Basically, this is a bubble map where all the bubbles are shifted slightly so that none overlap. You're basically sacrificing absolute location accuracy for a more clear visualization. Given that bubble maps are so often over-plotted and cluttered, I've always wanted to learn how to implement this map. Lucky for me the learning process was easy thanks to the cartogram library in R. See [here](https://github.com/sjewo/cartogram) for the details on using the function. It's very easy. You just need a table with a location column and a weight column, which determines the size of the bubble. 

In my case I used the total foreign-born population within each county as my weight. 

[Github code for this post](https://github.com/orionsbelt11/immigrant-nation/tree/main)

![Figure](img/foreign-pop-total.png)
I thought the result looked pretty cool and displayed some interesting facts. Miami is the only county that is majority immigrant. Also, the West Aleutians Census district in Alaska is 30 percent immigrants from the Philippines. Apparently sailors from the Philippines started showing up in Alaska in the 18th century (i.e. before it was part of the US). 

The hardest part of the project was just setting up the scripts for formatting the ACS data. That script is available here. I then spent a lot of time (probably too much) just digging into to see where different immigrant communities are clustered within the US. 

Here are few of those maps:

![Figure](img/foreign-pop-americas.png)

![Figure](img/foreign-pop-europe.png)
![Figure](img/foreign-pop-china.png)
![Figure](img/foreign-pop-africa.png)
![Figure](img/foreign-pop-mena.png)
