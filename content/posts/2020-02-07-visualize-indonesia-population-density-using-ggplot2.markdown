---
title: 'Visualize Indonesia Population Density using ggplot2 '
author: Idrusfachr
date: '2020-02-07'
slug: visualize-indonesia-population-density-using-ggplot2
categories:
  - R
tags:
  - Ggplot2
  - Map
  - R
lastmod: '2020-02-07T10:57:48+07:00'
description: ''
show_in_homepage: yes
show_description: no
license: ''
featured_image: ''
featured_image_preview: ''
comment: yes
toc: yes
autoCollapseToc: yes
math: no
---

### Intro
Many peoples say that ggplot is a powerful package for visualization in R. I know that. But i'd never triggered to explore this package deeper (except just to visualize mainstream plot like pie chart, bar chart, etc.) till this [post](eriqande.github.io/rep-res-web/lectures/making-maps-with-R.html) impressed me.

That post is about plotting map using ggplot. The pretty plots resulted on that post challenged me to explore ggplot more, especially in plotting map/spatial data.

### Preparation

We always need data for any analysis. The author of those post use USA map data available in `ggplot2 package`. Though that post is reproducible research (data and code are available) that i can re-run on my own, but I want to go local by using Indonesia map data.

So, objective of this post is to visualize Indonesian population by province in Indonesia. So we need the two kinds of dataset. First is population data in province level and second is Indonesia map data.
The [first data](https://www.bps.go.id/linkTableDinamis/view/id/843) is available in BPS site. The data consists of population density (jiwa/km2) for each province in Indonesia from 2000 till 2015. I will go with the latest year available on the data which is 2015.

Oke, now we arrive to the hardest part. I stacked for a while when searching Indonesia map data that suit for `ggplot2` `geom_polygon` format. But Alhamdillah, after read some articles about kind of spatial data, I got one clue, I need shape data with `.shp` format to plot map data. And just by typing "indonesian .shp data" on google, He will provide us tons of link results. Finally, I got Indonesian `.shp` data from this [site](http://biogeo.ucdavis.edu/data/gadm2.8/shp/IDN_adm_shp.zip). I will use level 1 data (province level) for this analysis.

#### Data Preprocessing

The fist step is load data into R. I use `rgdal package` to import `.shp` data. rgdal is more efficient in loading shape data compared to others package like `maptools`, etc.

```r
#import data .shp file
library("rgdal")
#import .shp data
idn_shape <- readOGR(dsn = path.expand("/Users/muhammad.i.fachruddin/Downloads/IDN_adm_shp/"),
                     layer="IDN_adm1")
```
Here's one of the advantages using `rgdal package`, though it is in shape format we can use operation like `names()`, `table()`, and `$` subset operation like in data frame format.

After data loaded into R workspace, we need to reformat shape data into format that suit to `ggplot2` function.

```r
#format data
library("ggplot2")
idn_shape_df <- fortify(idn_shape)
head(idn_shape_df)
```

```
##       long      lat order  hole piece id group
## 1 96.23585 4.055532     1 FALSE     1  0   0.1
## 2 96.23552 4.055860     2 FALSE     1  0   0.1
## 3 96.23448 4.056840     3 FALSE     1  0   0.1
## 4 96.23372 4.057950     4 FALSE     1  0   0.1
## 5 96.23324 4.058574     5 FALSE     1  0   0.1
## 6 96.23248 4.059689     6 FALSE     1  0   0.1
```



<!--more-->
