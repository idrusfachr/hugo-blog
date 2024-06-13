---
title: Covid 19 Update Dashboard Using Flexdashboard
author: idrusfachr
date: '2024-06-13'
slug: [covid19-dashboard-using-flexdashboard]
categories:
  - R
tags:
  - R
  - visualization
  - flexdashboard
  - dashboard
lastmod: '2024-06-13T17:06:49+07:00'
description: ''
show_in_homepage: yes
show_description: no
license: ''
featured_image: '/images/new_case_trend_daily.png'
featured_image_preview: ''
comment: yes
toc: no
autoCollapseToc: yes
math: no
---

R provide various libraries and frameworks for data visualization. `ggplot` and `shiny` of course are sample of two most popular and most powerful data viz libraries in R. In this post, i want to share my portfolio creating viz dashboard in R using not so popular library but i think quite interesting and worth a try.  [`Flexdashboard`](https://pkgs.rstudio.com/flexdashboard/)

Flexdashboard, is a package in R, empowers users to create stunning and interactive dashboards effortlessly. Whether you are a data scientist, analyst, or enthusiast, Flexdashboard provides a seamless way to integrate R scripts into interactive web applications without requiring extensive knowledge of web development. In this blog post, i will use Covid-19 data in Indonesia. to visualize daily update of national Covid-19 cases.

Find the full code [here](https://github.com/idrusfachr/covid19-flexdashboard)

### Package used
Below are list of libraries or packages needed to build the dashboard

* Data acquisition
 * `httr` to work with API
 * `jsonlite` to parse & generate JSON
 
* Data wrangling
 * `dplyr` for data manipulation, i am a fan of its pipe `%>%`
 * `tidyr` to create tidy data
 * `xts` for handling time series data preparation
 * `RcppRoll` for efficient computation of windowed aggregation function
 
* Data visualization
 * [`dygraphs`](https://rstudio.github.io/dygraphs/) is R interface for popular JavaScript charting library for time-series data, [dygraphs](https://dygraphs.com/)
 * [`highcharter`](https://jkunst.com/highcharter/) is a R wrapper for [Highcharts](https://www.highcharts.com/) javascript library for creating chart & dashboard for web and mobile.
 
### Visualization
> visit this [link](https://idrusfachr.github.io/covid19-flexdashboard/) to view the dashboard.

Flexdashboard provide flexibility for us to create and design our dashboard. Its components cover almost all we need in our dashboard including flexible text annotation inside the chart.

Since flexdashboard support JavaScript HTML widgets data visualizations, i use 2 libraries that very powerful for interactive dashboard. dygraphs and highchart, 2 libraries that are very popular among JavaScript developer to build chart on web or mobile.

Some components i use in this dashboard are below

####  Value Box

Powerful to display single number along with title and optional icon. I use it to display total cases per day.

![](/images/valuebox.png)

#### HTML Widget

##### dygraphs

Visualize trend by time series using `dypraphs`. We can also add event inside chart to notate important event happened on certain time.

![](/images/new_case_trend_daily.png)

![](/images/case_trend_cummulative.png)

breakdown daily trend by province

![case by province](/images/case_trend_by_province.png)

Last chart is using highchart to map the covid-19 fatality per province based on death rate and recovery rate. highchart offer very flexible and complete tooltip for us to customise our chart.
status.

![](/images/fatality_by_province.png)
this chart divide province into 4 groups, line and color as helper to distinguish group of province by fatality rate. It can help the authority to make right and accurate decision for the group.]


### Build the dashboard
I use github action to host the dashboard. It's simple and easy.

### See the interactive Dashboard [HERE](https://pkgs.rstudio.com/flexdashboard/)

#### Source code https://github.com/idrusfachr/covid19-flexdashboard
<!--more-->
