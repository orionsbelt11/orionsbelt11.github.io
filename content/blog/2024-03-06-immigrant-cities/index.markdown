---
title: "Immigrant Cities"
subtitle: 
excerpt: "Mapping immigrant communities with ACS data, R, and QGIS."
date: 2024-03-06
author: 
draft: false
images:
series:
tags:
categories:
layout: single
---




Last week I posted some [maps](https://orionwilcox.com/blog/2024-02-27-dmv-diversity/) displaying the diverse immigrant communities in the DC region using American Community Survey data.  I decided to create some maps of other cities as a comparison. 

Here’s New York City.

![Figure](img/nyc.png)

When I posted the map of DC’s immigrant communities to Reddit most of the responses were positive. People pointed out how they recognized neighborhoods they lived in or how the map could also serve as a guide to finding the best food around on the beltway. Of course, being Reddit, there were also a few trolls. One argued that the map “explained why the area was so violent” (fact check: immigrants commit fewer crimes than citizens). Other's argued that the maps were proof of "an invasion." The reality of course is that while the immigrant population in the US is higher today than it has been in recent decades, its actually still lower than it was in the 1800s. And who were those immigrants who arrived then, again?

Given how polarizing the issue of immigration is today, I realized that this kind of map is going to be viewed differently by different people depending on their political view. But what I found fascinating about the DC map is how familiar it felt. I immediately recognized the Salvadoran communities in Mt. Pleasant and the Ethiopian enclave in Alexandria. The reality is that these maps don’t show how immigration is changing America but rather how it has made America what it is today. I think that's pretty cool. 

Now for some maps!

![Figure](img/la.png)
LA is massive so we're really only getting the downtown here (sorry San Fernando Valley!). As expected, the largest population is from Mexico. I thought it was interesting how the Salvadoran and Guatemalan communities are very mixed. Makes sense.

![Figure](img/chicago.png)
I love how Chinatown and the neighborhood known as the Polish Triangle (top left) stand out on this map.

![Figure](img/houston.png)

Like LA, Houston is sprawling.. and largely Mexican.

#The Code 

The main challenge I faced was figuring out what exactly constitutes a “city”. I played around using the Census Bureau’s Metropolitan Statistical Areas (MSAs) but I found these to be too large to get the kind of large-scale maps I wanted. The size of the also differs a lot, which means that the scale would be all over the place between the cities. 

In the end I decided to grab all of the Census tracts within a 20 mile radius of the city center. The downside here is that the maps may not capture the full sprawling extent of some cities, such as Los Angeles. The upside is that it makes it really easy to reproduce a map for any city. I also like how the circular map gives the sense of a “zoom in” on the city. I’ve posted the full code to Github [here](https://github.com/orionsbelt11/immigrant-cities/tree/main). It should only require changing the city name at the top of the script and editing paths to point to OSM files (for the basemap). OSM files are available for download from [Geofabrik](https://www.geofabrik.de/).

Another challenge is the inherently messy nature of this data. As I wrote last week, I like how the dasymetric dot plots show that the communities are both clustered in specific areas and overlapping in others. However, the initial designs all looked like an MS Paint project. I decided to go with a design that highlights the data and provides a minimalist basemap to provide some context. For the data points I used the Okabe-Ito color-blind friendly color palette. I wanted five distinct colors and I also like that this improves accessibility. For the basemap I used a grayscale QGIS [stylesheet](https://github.com/HeikkiVesanto/QGIS_OSM_Styles/tree/main/osm_light) for OSM data created by the Irish developer Heikki Vesanto.


[Get the code!](https://github.com/orionsbelt11/immigrant-cities/tree/main)



