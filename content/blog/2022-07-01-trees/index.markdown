---
title: "A City of Trees"
subtitle: "If you can afford it"
excerpt: "Washington DC is a leafy capital but the city's trees are not evenly distributed."
date: 2022-07-01
author: 
draft: false
images:
series:
tags:
categories:
layout: single
---
The French-American urban planner Pierre L'Enfant, the man responsible for designing Washington, DC's layout, once said he envisioned a "city of trees." The leafy National Mall and Rock Creek Park are remnants of this vision. Unfortunately, not all DC neighborhoods have lived up to L'Enfant's plan.

While the city's northwest can seem like an urban forest, the central and southwest are veritable concrete jungles. And access to trees matters for more than neighborhood aesthetics. Urban trees cool the air by casting shade and releasing water vapor into the air. Other factors, such as asphalt, also create "heat islands." But trees are a big factor.

On a given summer day, the difference between temperatures in the leafiest neighborhoods and those with the least trees can be as high as 17 degrees.

I used data on trees and surface temperature from Open Data DC to create an interactive visual displaying the impact of trees on summer temperatures. Hover over each district to see the percentage of the tract that is covered by trees (i.e. urban tree canopy), the average temperature in July 2018, and demographic details.

<iframe seamless frameborder='0' src ="https://public.tableau.com/views/DCTreesMaps/maps?:embed=yes&:display_count=yes&:showVizHome=no" width = '650' height = '500' scrolling='yes' allowfullscreen='true'></iframe>

The tree canopy data come from a tree census (yeah, that's a thing) conducted by the city using aerial photography and temperature data prepared by GIS specialists at the Georgetown Strategy Group. The dataset includes a list of all the census tracts in DC and the percentage of the tract's land covered by trees, otherwise known as the Urban Tree Canopy.

I merged the tree data with demographic data from the census (median income, ethnicity etc.) using the statistical package R. I then created a linear regression model to measure the relationship between a 1% increase in the area of a tract covered by trees and temperatures. The model controls for other factors including the tract's median income, demographics, and the presence of asphalt. The model shows that a 1% increase in tree canopy is associated with a drop in temperatures of 5 degrees Fahrenheit (see chart on left below).

So having trees in your neighborhood is a good thing. But like most good things, trees are not spread equally throughout the city. Census tracts with higher median incomes, and a higher percentage of white residents, tend to have more trees -- and thus cooler summers.

<iframe seamless frameborder='0' src ="https://public.tableau.com/views/DCTreesScatter/scatter?:embed=yes&:display_count=yes&:showVizHome=no" width = '650' height = '400' scrolling='yes' allowfullscreen='true'></iframe>

For more background on the importance of urban trees, as well as the fight for more green spaces in DC, check out the film City of Trees.
