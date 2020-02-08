---
title: Visualize Indonesia Population Density using ggplot2
author: idrusfachr
date: '2017-02-12'
slug: visualize-indonesia-population-density-using-ggplot2
categories:
  - R
tags:
  - ggplot2
  - R
  - map
lastmod: '2020-02-07T23:06:55+07:00'
description: 'visualisasi peta indonesia dengan R dan ggplot2'
show_in_homepage: yes
show_description: no
license: ''
featured_image: ''
featured_image_preview: ''
comment: yes
toc: no
autoCollapseToc: yes
math: no
---

<!--more-->

## Intro
Many peoples say that ggplot is a powerful package for visualization in R. I know that. But i'd never triggered to explore this package deeper (except just to visualize mainstream plot like pie chart, bar chart, etc.) till this [post](http://eriqande.github.io/rep-res-web/lectures/making-maps-with-R.html) impressed me.

That post is about plotting map using ggplot. The pretty plots resulted on that post challenged me to explore ggplot more, especially in plotting map/spatial data.

## Preparation

We always need data for any analysis. The author of those post use USA map data available in `ggplot2` package. Though that post is reproducible research (data and code are available) menas i can re-run the examole on my own, but I want to go with local using Indonesia map data.

So, objective of this post is to visualize Indonesian population by province in Indonesia. So we need the two kinds of dataset. First is population data in province level and second is Indonesia map data.
The [first data](https://www.bps.go.id/linkTableDinamis/view/id/843) is available in BPS site. The data consists of population density (jiwa/km2) for each province in Indonesia from 2000 till 2015. I will go with the latest year available on the data which is 2015.

Oke, now we arrive to the hardest part. I stacked for a while when searching Indonesia map data that suit for ggplot2 geom_polygon format. But Alhamdillah, after read some articles about kind of spatial data, I got one clue, I need shape data with .shp format to plot map data. And just by typing "indonesian .shp data" on google, He will provide us tons of link results. Finally, I got Indonesian .shp data from this [site](http://biogeo.ucdavis.edu/data/gadm2.8/shp/IDN_adm_shp.zip). I downloaded level 1 data (province level) for this analysis.

### Data Acquisition

The fist step is load data into R. I use rgdal package to import .shp data. rgdal is more efficient in loading shape data compared to others package like maptools, etc.






















