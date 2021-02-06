---
layout: post
title: Tracking South Sudan's conflict with ACLED data
---

![_config.yml]({{ site.baseurl }}/images/ss_map.png)

As the world’s youngest nation, South Sudan has had a rough start. After a decades-long conflict with the Sudanese government, the south gained independence in 2011. Less than two years later, the country descended into civil war, as former rebel leaders from the country’s two largest ethnic groups, the Dinka and the Nuer, fought over resources and control of the federal government.

The war has devastated the population, killing more than 400,000 and displacing millions. While no region was left unscathed, the northeast, the ancestral home of both the Dinka and the Nuer, and the diverse south around the capital Juba, were particularly hard hit. 

Despite multiple ceasefire agreements and an on-again-off-again peace process, the fighting continues today. A late 2018 agreement, known as the Revitalized Agreement on the Resolution of the Conflict in South Sudan (R-ARCSS), between the government and most of the rebel groups did lead to a noticeable reduction in violence throughout 2019, although conflict has increased by nearly 40% in 2020.

![_config.yml]({{ site.baseurl }}/images/Slide2.PNG)

I used data from the Armed Conflict Location & Event Database (ACLED) to look into how the conflict has changed since R-ARCSS was signed in September 2018. ACLED researchers track local media outlets to tag and geolocate incidents of armed violence. Each event is logged in an online database and tagged by date, location, and other key metadata. Read more about the ACLED methodology here. I consulted several online maps for the data on ethnic groups. The map indicates the majority ethnic group in each region. For this analysis, I used the R packages Dplyr (for manipulating the data) and Ggplot2 (for making the charts), as well as QGIS for mapping.

The R-ARCSS agreement is viewed by most analysts as inherently flawed in that it fails to address underlying trust deficits and the needs of transitional justice. Nevertheless, violence did decrease throughout the country in the months following the agreements and both sides have kept talking. In February 2020, Riek Machar, head of the largest opposition group, returned as Vice President (the same position he had when the conflict started). 

![_config.yml]({{ site.baseurl }}/images/Slide3.PNG)

The increase in violence in 2020 largely stems from intercommunal violence in the northeast which killed hundreds over the summer. Many media outlets have characterized this violence as “cattle raiding”, but a U.N. commissioner in South Sudan said that these local conflicts are being increasingly “politicized” and militias have access to heavy weaponry.  Local conflicts have also flared into large-scale violence in the central Warrap and Lakes states, where recent flooding has also hit communities hard. In the southern region around Juba, known as Central Equatoria,  the National Salvation Front, or NAS (not an acronym, Arabic for "people"; also they have a website), has refused to join the peace talks and continue to fight government forces.

![_config.yml]({{ site.baseurl }}/images/Slide3.PNG)

As these maps and timeline show, violence continues throughout the country more than two years since the agreement and has actually worsened since the agreement in some places (see below). In 2020, violence was 37% higher than 2019 levels, still below the peak of violence during the war, but devastating all the same. 
