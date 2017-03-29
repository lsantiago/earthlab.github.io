---
layout: single
title: "An example of creating modular code in R - Efficient scientific programming"
excerpt: "This lesson provides an example of modularizing code in R. "
authors: ['Carson Farmer', 'Leah Wasser']
modified: '2017-03-28'
category: [course-materials]
class-lesson: ['intro-APIs-r']
permalink: /course-materials/earth-analytics/week-10/leaflet-r/
nav-title: 'Leaflet '
week: 10
sidebar:
  nav:
author_profile: false
comments: true
order: 4
---

{% include toc title="In This Lesson" icon="file-text" %}

<div class='notice--success' markdown="1">

## <i class="fa fa-graduation-cap" aria-hidden="true"></i> Learning Objectives

After completing this tutorial, you will be able to:

*

## <i class="fa fa-check-square-o fa-2" aria-hidden="true"></i> What you need

You will need a computer with internet access to complete this lesson and the
data that we already downloaded for week 6 of the course.

{% include/data_subsets/course_earth_analytics/_data-week6-7.md %}
</div>






```r
library("knitr")
library("dplyr")
library("ggplot2")
library("RCurl")
```



```r
base <- "https://data.colorado.gov/resource/j5pc-4t32.json?"
full <- paste0(base, "station_status=Active",
            "&county=BOULDER")
res <- getURL(URLencode(full))
sites <- fromJSON(res)

# turn amount into number
sites$amount <- as.numeric(sites$amount)
# lat and long should also be numeric
sites$location$longitude <- as.numeric(sites$location$longitude)
sites$location$latitude <- as.numeric(sites$location$latitude)
```


## Interactive maps with Leaflet

Static maps are useful for adding context, but often times we want to interact with our data and explore things geographically--perhaps via an interactive web-map...

...this is after all, web-data we're working with!

- [Leaflet](http://leafletjs.com) is an open-source `JavaScript` library for mobile-friendly interactive maps
    - It is designed with *simplicity*, *performance* and *usability* in mind
    - It also has a beautiful, easy to use, and [well-documented API](http://leafletjs.com/reference.html)
- Even better, the `leaflet` `R` package 'wraps' Leaflet functionality in an easy to use `R` package!
  - Now, a basic web-map is as easy as:


```r
library(leaflet)

map = leaflet() %>%
  addTiles() %>%  # Default OpenStreetMap tiles
  addMarkers(lng=174.768, lat=-36.852,
             popup="The birthplace of R")
print(map)
```




<iframe title="Basic Map" width="80%" height="600" src="{{ site.url }}/leaflet-maps/birthplace_r.html" frameborder="0" allowfullscreen></iframe>

Web-Mapping with R and Leaflet
========================================================
type: sub-section

Static maps are useful for adding context, but often times we want to interact with our data and explore things geographically--perhaps via an interactive web-map...

...this is after all, web-data we're working with!

Web-Mapping Made Easy
========================================================

- [Leaflet](http://leafletjs.com) is an open-source `JavaScript` library for mobile-friendly interactive maps
    - It is designed with *simplicity*, *performance* and *usability* in mind
    - It also has a beautiful, easy to use, and [well-documented API](http://leafletjs.com/reference.html)
- Even better, the `leaflet` `R` package 'wraps' Leaflet functionality in an easy to use `R` package!
  - Now, a basic web-map is as easy as:


Basic Web Map
========================================================
title: false




Mapping Real Data
========================================================

- Getting back to our previous example, let's plot the current surface water conditions again, this time using `leaflet`:


```r
leaflet(sites) %>%
  addTiles() %>%
  addCircleMarkers(lng=~long, lat=~lat)
```


## Leaflet could be a bonus / optonal how to

- In case you’re not familiar with the [`magrittr`](https://cran.r-project.org/web/packages/magrittr/index.html) pipe operator `(%>%)`, here is the equivalent *without* pipes:


```r
map = leaflet(sites)
map = addTiles(map)
map = addCircleMarkers(map, lng=~long, lat=~lat)
## Error in eval(expr, envir, enclos): object 'long' not found
```



Water Web Map
========================================================
title: false

<iframe title="Basic Map" width="80%" height="600" src="{{ site.url }}/leaflet-maps/water_map1.html" frameborder="0" allowfullscreen></iframe>

Using the Power of Leaflet
========================================================

- Leaflet has all sorts of useful functions for web-mapping...
- We can add 'popups' and use 'markers' instead of circles
    - Plus we can specify different basemaps, like this one from [CartoDB](https://cartodb.com) called Positron:


```r
leaflet(sites) %>%
  addProviderTiles("CartoDB.Positron") %>%
  addMarkers(lng=~long, lat=~lat, popup=~station_name)
```


```
## Error in eval(expr, envir, enclos): object 'long' not found
```




<iframe title="Basic Map" width="80%" height="600" src="{{ site.url }}/leaflet-maps/water_map2.html" frameborder="0" allowfullscreen></iframe>

More Water Web Maps!
========================================================

- We can even specify a *custom* icon, just for fun:


```r
# Nothing special, just found this one online...
url = "http://tinyurl.com/jeybtwj"
water = makeIcon(url, url, 24, 24)

leaflet(sites) %>%
  addProviderTiles("Stamen.Terrain") %>%
  addMarkers(lng=~long, lat=~lat, icon=water,
             popup=~paste0(station_name,
                           "<br/>Discharg: ",
                           amount))
```


```
## Error in eval(expr, envir, enclos): object 'long' not found
```

Final Water Web Map!
========================================================
title: false

<iframe title="Basic Map" width="80%" height="600" src="{{ site.url }}/leaflet-maps/water_map3.html" frameborder="0" allowfullscreen></iframe>