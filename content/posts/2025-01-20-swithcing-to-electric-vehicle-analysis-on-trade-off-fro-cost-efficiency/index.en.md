---
title: 'Switching To Electric Vehicle : Analysis on Trade-off for Cost Efficiency'
author: Package Build
date: '2025-01-20'
slug: []
categories:
  - Analysis
  - R
tags:
  - ggplot2
lastmod: '2025-01-20T13:24:45+07:00'
draft: no
description: ''
show_in_homepage: yes
show_description: no
license: ''
featured_image: '/images/ev_vs_gasoline.png'
featured_image_preview: ''
comment: yes
toc: no
autoCollapseToc: yes
math: no
---

<!--more-->

**Today marks four months since I started using an electric vehicle (EV) for my daily commute**. So far, I’m quite satisfied with my decision to switch to an EV. While there are some aspects where regular motorcycles are still better, overall, it has been a good experience.

Before deciding to switch to an EV, I, of course, did some research and a simple analysis of the pros and cons. I considered various aspects such as price, cost, quality, reliability, and after-sales support.

### Price
When it comes to price, EVs are competitive with regular motorcycles. In fact, many EVs are even cheaper.

### Quality, Reliability, and After-Sales Support
For these aspects, regular motorcycles still have the edge over EVs, and there’s no doubt about it. Mainstream brands like Honda and Yamaha have been established for decades and boast wide dealer and service center coverage. EVs, on the other hand, still need several more years to reach that level. I set a minimum standard and threshold for these aspects before making my decision.

### Cost Efficiency: Where EVs Stand Out
One area where EVs have the potential to outperform regular (gasoline) motorcycles is in cost efficiency. To determine this, I conducted a simple analysis to find the trade-off point—essentially, under what conditions switching to an EV would save me money.

### Choosing My EV: Polytron Fox R
After some research and comparisons, I decided to purchase the Polytron Fox R. The brand’s reputation, user reviews, and dealer/service center coverage were major plus points for me. I also appreciated the battery subscription scheme, which I consider a significant advantage. Since the battery is the most expensive part of an EV (accounting for around 30% of the vehicle’s price) and we’re still uncertain about its lifespan in this early EV era, the subscription model mitigates the risk upfront.


### Cost Breakdown
So, when does switching to an EV start saving money? Let’s break down the costs and visualize the data:

Assumptions:
* Gasoline price: Rp 13,000 (Pertamax)
* Battery subscription: Rp 200,000 per month
* Charging cost: Rp 5,700 per full charge (0% to 100%)

_Cost per kilometer (km):_
* Gasoline motorcycle: 
Gasoline cist : Rp 13,000 / 55 km = Rp 236/km (Assumes Pertamax achieves 55 km per liter.)

* EV (Polytron Fox R):

Battery subscription: Rp 200,000 / 30 days = Rp 6,667/day
Charging cost: Rp 5,700 / 130 km = Rp 44/km


``` r
library(dplyr)
library(tidyr)
library(ggplot2)


pertamax_price_per_km <- 13000/55 #assuming 55km per liter
battery_sub_perday <- 200000/30
charging_per_km <- 44 #in rupiah

dat <- data.frame(
  km_perday = seq(1,150),
  mosin = seq(1,150) * pertamax_price_per_km,
  molis = (seq(1,150) * charging_per_km) + battery_sub_perday
  ) %>% 
  mutate(area_group = ifelse(molis > mosin, "molis_higher", "mosin_higher"))


ggplot(dat, aes(x = km_perday)) +
  # Fill areas under the lines
  geom_ribbon(aes(ymin = pmin(mosin, molis), 
                  ymax = pmax(mosin, molis), 
                  fill = area_group), alpha = 0.2) +
  # Add the lines on top
  geom_line(aes(y = mosin, color = "mosin"), size = 1) +
  geom_line(aes(y = molis, color = "molis"), size = 1) +
  # Add custom colors
  scale_fill_manual(values = c("mosin_higher" = "skyblue", "molis_higher" = "red")) +
  scale_color_manual(values = c("mosin" = "red", "molis" = "skyblue")) +
  # Labels and theme
  labs(
    x = "Distance (KM/day)",
    y = "Daily Cost (Rp)",
    fill = "Area", 
    color = "Line"
  ) +
  geom_segment(aes(x = 35, xend = 35, y = 0, yend = 30000), 
               linetype = "dashed", color = "black", size = 0.5) +
  geom_segment(aes(x = 78, xend = 78, y = 18436, yend = 10087), 
               linetype = "solid", color = "black", size = 0.3) +
  geom_segment(aes(x = 76, xend = 80, y = 18436, yend = 18436), 
               color = "black", size = 0.3) +
  geom_segment(aes(x = 76.5, xend = 79.5, y = 10087, yend = 10087), 
               color = "black", size = 0.3) +
  annotate(
    geom = "text",
    x = 150,
    y = max(dat$mosin),
    label = "Gasoline\nVehicle",
    hjust = -0.2, 
    size = 4,
    colour = "red"
  ) +
  annotate(
    geom = "text",
    x = 150,
    y = max(dat$molis),
    label = "Electric\nVehicle",
    hjust = -0.2, 
    size = 4,
    colour = "skyblue"
  ) +
  annotate(
    geom = "text", x = 5, y = 39000, 
    label = "Switching to Electric Vehicle:\nA Cost Comparison with Gasoline Motorcycles", 
    hjust = 0, vjust = 1, size = 4, , fontface = 'bold'
  ) +
  annotate(
    geom = "text", x = 5, y = 35000, 
    label = "Evaluate the cost trade-offs between electric and gasoline motorcycles", 
    hjust = 0, vjust = 1, size = 3.5, colour = "darkgrey", fontface = 'bold'
  ) +
  annotate(geom = "point", x = 35, y = 8273, colour = "orange", size = 3) + 
  annotate(geom = "label", x = 29, y = 6900, label = "35 km/day", hjust = "left", 
           size = 2.5, fill = "lightgrey") +
  annotate(
    'text',
    x = 5,
    y = 31000,
    label = 'Minimum daily distance where EVs become \ncheaper than gasoline vehicles is 35 km',
    fontface = 'italic',
    hjust = "left",
    size = 2.5
  ) +
  annotate(
    'curve',
    x = 17, 
    y = 12500,
    yend = 6000,
    xend = 15,
    linewidth = 0.5,
    curvature = 0.5,
    arrow = arrow(length = unit(0.2, 'cm'))
  ) +
  annotate(
    'text',
    x = 5,
    y = 14000,
    label = 'Gasoline vehicle is \ncheaper than EV',
    fontface = 'italic',
    hjust = "left",
    size = 2.5
  ) +
  annotate(
    'text',
    x = 110,
    y = 19000,
    label = 'Electric vehicle is \ncheaper than EV',
    fontface = 'italic',
    hjust = "left",
    size = 2.5
  ) +
  annotate(
    'text',
    x = 155,
    y = 0,
    label = '@idrusfachr | Build with ggplot2',
    fontface = 'italic',
    hjust = "right",
    colour = "#1A1A1A",
    size = 2
  ) +
  annotate(
    'label',
    x = 71,
    y = 14400,
    label = '45,29% save',
    fill = "lightgrey",
    fontface = 'italic',
    hjust = "left",
    size = 2.5
  ) +
  annotate(
    'curve',
    x = 79, 
    y = 13500,
    yend = 5700,
    xend = 100,
    linewidth = 0.5,
    curvature = -0.5,
    arrow = arrow(length = unit(0.2, 'cm'))
  ) +
  annotate(
    'text',
    x = 80,
    y = 4700,
    label = 'For me personally, with daily distance 78 km, \ni could save 45,29% using EV than regular vehicle',
    fontface = 'italic',
    hjust = "left",
    size = 2.5
  ) +
  theme_classic() +
  theme(legend.position = "none",
        panel.grid.major = element_blank(), 
        panel.grid.minor = element_blank(),
        lot.margin = margin(10, 40, 10, 10), #extra space on the right
        legend.text=element_text(size=9),
        # plot.title = element_text(color="black", size=14, face="bold.italic", hjust=0.5),
        axis.title.x = element_text(color="darkgrey", size=9),
        axis.title.y = element_text(color="darkgrey", size=9)
        ) +
  scale_x_continuous(expand = expansion(mult = c(0.01, 0.1)))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-1.png" width="672" />

### Findings
Based on the simulation visualized in the chart above, I found that I need to travel a minimum of **35 km per day** to achieve cost efficiency with an EV. This analysis does not yet include other costs like taxes or regular maintenance.

When factoring in taxes and maintenance, the break-even distance would be even lower. EVs have a yearly tax of only Rp 35,000 and require no regular servicing or oil changes. In contrast, gasoline motorcycles incur a yearly tax of around Rp 300,000 and require 3-4 services annually, costing about Rp 400,000 in total.

### My Case
In my case, I travel 78 km daily (yes, that much!), which allows me to save approximately 45.29% compared to using a regular motorcycle. This saving could be even higher if I included other costs like taxes and maintenance.

### Conclusion
Thats the numbers behind my decision to swith to EV, and so far, it’s been the right decision.

