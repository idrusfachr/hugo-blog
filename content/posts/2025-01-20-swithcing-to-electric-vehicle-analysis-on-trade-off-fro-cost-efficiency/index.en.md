---
title: 'Swithcing To Electric Vehicle : Analysis on Trade-off fro Cost Efficiency'
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

Today marked as 4 months I have been using EV for my daily commuting vehicle. So far, i am quite satisfied with my decision to switch to EV vehicles. There are some aspects that regular vehicle still better than EV, but overall, it's an oke for me.

Before decided to EV, of course i did some research and simple analysis on what is the pros and cons switching. Price, cost, quality, reliability, after sales are some aspects that i put into account.

For price, EV are able compete with regular motorcycle. Even many of EV are cheaper. For quality, reliability and after sales, regular vehicles are better than EV. No doubt. Mainstream brand like Honda and Yamaha have establish for decades with very wide coverage in dealer and service center. So for those aspects, EV need more years to achieve that levels. Thus, i put minimum standard and treshold for those aspects.

One aspect that EV has potentially better than regular/gasoline vehicle is on the cost. So, i did simple analysis to find the trade-off on what condition switching to EV will save my cost. 

After some research and comparison, i decided to take Polytron Fox R. Brand reputation, review and testimony from users and dealer/service center coverage are plus points for the brand. The battery subscription scheme also a point plus in my opinion, since battery is the most expensive part for EV (around 30% of the price) and in this early EV era, we still don't know how long the battery life. With subscription mwthod, i pay the risk on the front.




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


