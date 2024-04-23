---
title: "Svelte Challenge: Part II"
subtitle:  "Can I learn interactive data viz in three months?"
excerpt: "An ugly (but responsive!) scatter plot"
date: 2024-04-23
author: 
draft: false
images:
series:
tags:
categories:
layout: single
---

### Tl;DR

Last month I started learning Svelte for data viz. In [Part I](https://orionwilcox.com/blog/2024-04-06-svelte-part1/) I laid out my goals and learning plan.

### So what have I done so far?

I started this challenge on March 18th. Over the following two weeks I spent 21 hours and 13 minutes on this challenge. Of this time I spent about 10 hours on pre-requisites, 6 hours on the first course module, and 5 hours on a personal project.

<iframe title="Learning Svelte" aria-label="Grouped Columns" id="datawrapper-chart-mhmfn" src="https://datawrapper.dwcdn.net/mhmfn/1/" scrolling="no" frameborder="0" style="width: 0; min-width: 100% !important; border: none;" height="370" data-external="1"></iframe><script type="text/javascript">!function(){"use strict";window.addEventListener("message",(function(a){if(void 0!==a.data["datawrapper-height"]){var e=document.querySelectorAll("iframe");for(var t in a.data["datawrapper-height"])for(var r=0;r<e.length;r++)if(e[r].contentWindow===a.source){var i=a.data["datawrapper-height"][t]+"px";e[r].style.height=i}}}))}();
</script>

### Pre-requisites

In order to refresh my memory of HTML and CSS, I completed Mozilla’s Web Basics tutorial. I chose this because I wanted a super basic tutorial and I also wanted one that would allow me to get comfortable coding on my own computer. In my experience getting things set up to run locally is one of the most difficult challenges of any coding project, so I didn’t want a course that just had me coding in a browser. The Mozilla course was great for remembering how HTML, CSS and Document-Object Model (DOM) work in practice. 

Per Connor’s recommendation in the course notes, I then completed this Javascript fundamentals [tutorial](https://javascript.info/). While not required, I decided to set up the scripts for the exercises locally (rather than in the browser) to test everything out. 

Lastly, I worked through about half of the official Svelte tutorial. The tutorial is set up well but does not provide a lot of context. I’m sure its great for experienced developers who are just kicking the tires of Svelte but I didn’t feel like I was absorbing the information so stopped about half way through. That said, once I jumped into the course, I started to recognize some of what I learned in the tutorial, so it wasn’t a total loss.

### Course

The first module in the [course](https://www.newline.co/courses/better-data-visualizations-with-svelte) (after a few videos describing what Svelte is and getting your developer environment set up) has you build a super simple interactive scatter plot. I really like the course's approach to teaching, which focuses on explaining concepts through practical examples that build toward a finished project (in this case the chart). So for example, he teaches the concept an #each block, which is a key feature of Svelte that allows you to iterate through an array of data points and apply some logic (i.e., for plotting), by demonstrating how to plot the points for the scatter plot. There were definitely some areas where the course assumes a bit more front-end knowledge than I had but I was able to pause and Google those ideas since its a self-paced course.

![Figure](img/course-chart-1.png)

### Personal Project: An ugly (but responsive!) scatter plot

After completing the first module, my head was spinning. I had learned about drawing SVG elements based on data with an #each block, binding data to scales for plotting and reactivity, drawing axes (surprisingly hard), and adding in basic hover-over tooltips. In order to apply the above techniques, I also learned about linking different components in the app and exporting variables between components (also pretty confusing).

In order to make sure I actually understood these concepts I decided to build a simple scatter plot using data on the number of current NBA players born in each US state. I figured I would need to adapt some of the concepts above to my project since the data are shaped differently. 

Here's the final chart:

<div style="position: relative; padding-bottom: 75%; height: 0; overflow:hidden;">
  <iframe src="https://mrqwxz-5173.csb.app/" style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; border=0;"  frameborder="0" allowfullscreen></iframe>
</div>

[Here's the code](https://github.com/orionsbelt11/nba-scatter-plot)

### What I learned

I figured there would be a steep learning curve but I ended up running into a wall faster than I thought: how to upload the data into Svelte?

In the course, Connor provides the data for the example in a formatted JSON object but my data was in a CSV. 

I ended up spending an hour trying to figure this out. I was able to use R to convert my csv into a JSON that matched the course data format but it still did not load. After a lot of Googling I realized I simply needed to add EXPORT DEFAULT at the start of my JSON. Still not sure what this was about but it worked. Onward!

After figuring out how to load my data things went well for a while until I needed to create my axes. Here are ran into another doozy. My data contained values for each state’s 2020 population and the number of NBA players born in that state. I wanted to format the population data as 1 million as opposed to 1000000 (which was the default). My initial Googling led to a Svelte tutorial for creating an entire extra component (below).

![Figure](img/number_format.png)

This seemed a tad bit complicated but I couldn’t find another way. Eventually I learned that there is simple D3 function called format() that does this for you. 

![Figure](img/d3-function.png)

This was an important lesson. Going into this project I had assumed that D3 was “harder” than Svelte so I was limiting my troubleshooting to seeking out Svelte-based solutions. I now realize that Svelte and D3 compliment one another  well. If you’re trying to do something complicated but routine in Svelte, its likely there is an existing D3 function available.

After this lesson I feel like I have a good handle on the basic chart building process, the concept of responsiveness, how to scale data and create axes, and how to apply logic and CSS rules to style a chart.

The chart itself is still buggy (the tooltip for California hangs off the side). But overall I'm good with it and ready to move on.




