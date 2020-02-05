---
title: 'Battle of Aksi 212 on Twitter: Social Network Analysis Perspective'
author: idrusfachr
date: '2016-12-02'
slug: battle-of-aksi-212-on-twitter-social-network-analysis-perspective
categories:
  - R
tags:
  - social network analysis
  - R
  - text mining
lastmod: '2020-02-05T00:13:46+07:00'
description: ''
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

Aksi 212 is only one day away. Netizens both pro and contra side are hyped about the event. They actively tweets (or retweet or copas) about the event. I am curious about seeing the hyped in another side. I want to see the battle of pro and conta side in twitter.

To fullfil my curiosity, I crawled data from twitter a day before aksi 212 (December 1, 2016). In order to simplify the analysis, I only crawled 1500 tweets contains keyword "aksi bela islam" on that period.

By applying [social network analysis](https://en.wikipedia.org/wiki/Social_network_analysis) method, the collected tweets then visualize to find its network structure. Tools I used to done this task is R for collecting and preprocessing data, and Gephy for visualizing network.

### The Battle is Real
After doing some preprocessing and preparation of data, here is the network visualization of data I'd collected.

![Social Network of #aksibelaislam](/post/2016-10-16-battle-of-aksi-212-on-twitter-social-network-analysis-perspective_files/aksibelaislam3.png)

### What the graph tell us?

* The battle is real! there are polarization between netizen who're pro and contra with aksi 212. it created 2 main clusters wich is disconnected
* @maspiyungan dkk in pro side vs @zevanya in contra side. The pro side is more dominant than contra side. They clustered in one big cluster and connected each other formed (very) big buble, while in contra side only @zevanya gathered big buble (red circle), others Ahok's buzzer like @digembok @gunromli etc just created small buble (smaller red circle)
* It's about 6 influencers who're pro with "aksi 212" gathered and formed big bubble. They are @maspiyungan, @husainiadianm @GrabJakmania, @posmetro, @hidcom and @wartapolitik. Their network are close and connect each other. It means netizens who're pro with aksi 212 interacted with all influencers.
* While in contra side (red circle), there is only one influencer which is got high interaction, it's @zevanya. There's also other influencer with small interaction but it's far and not connected with main influencer. That's mean the netizens who're interact with @zevanya didn't interact with other influencer, they have different network.

### Network Characteristic 
If we refer to [Michael Lieberman paper](https://ieeexplore.ieee.org/author/37301531100) network characteristic in SNA, then the network characteristic of my data be like this.
* Aksi 212 in general
** Polarized
Two main clusters (pro side and contra side) with no interconnection
** Bazaar
Many small/medium groups, some isolates
* Pro side
** In-group
Many connection, few disconnected isolate
* Contra side
** Broadcast
A hub which is retweeted by many disconnected users


<!--more-->
