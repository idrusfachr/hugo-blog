---
title: ' Jokowi''s Cabinet Reshuffle Buzz '
author: Idrusfachr
date: '2016-10-16'
slug: jokowi-s-cabinet-reshuffle-buzz
categories:
  - R
tags:
  - R
  - text mining
  - Twitter
lastmod: '2020-02-05T13:32:44+07:00'
draft: false
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

### Background

President Joko Widodo announced cabinet reshuffle on 27th July, 2016. A move aimed at enhancing the effectiveness of his cabinet. This thing generated mixed responses from netizen. Some are pros and others are cons. Knowing about what people think about reshuffle looks interesting. So, I analyze what people talk on twitter regarding reshuffle topic by tracking keyword “#reshuffle” then extract information from there.

### Data

I collected data from 27 July 2016 till 3 August 2016 and got total of 15,290 tweets contains hashtag #reshuffle (of course, actually more than that number of tweets about #reshuffle out there). I use [`twitteR`](http://geoffjentry.hexdump.org/twitteR.pdf) package by Jeff Gentry to crawl the tweets.

### Analysis
#### Type of Tweets
Let's start the analysis by knowing the tweet behaviour over #reshuffle keyword on twitter.

![Type of tweet](/images/type_of_tweet.png)

It seems netizens is more like to retweet people tweet (55.4%) instead of express their own thought (tweet, 42.5%). Few of them (2,07%) did further discussion by replying tweet each others.
![Tweet's rush hour](/images/rush_hour.png)

This graph shows the distribution of time used by netizens to active on twitter. It looks netizens more like to spend their morning time to active on twitter instead of in the night. Peak time is at 8.00 am.

#### Influencer
 
Now, we will find out who's the 'buzzer' on twitter that spread tweet about #reshuffle. Here you go.

![Top 5 influencer of #reshuffle](/images/influencer.png)

There are total of 6081 unique users posted about #reshuffle on that period with impression 837,660,066. Graph above shows the top 10 twitter accounts who posted the most.

#### Who is the star?
Below is the popularity distribution of ministers that get involve in this cabinet reshuffle. Let's see who is the most talked (popular) minister over reshuffle issue.
![Top mentioned ministers](/images/ministry.png)

Woa!, Anies baswedan is the most popular minister over #reshuffle topic. Followed by Wiranto, Sri Mulyani and Ignasius Jonan.

#### What's netizens thought?
Ok, in above we have known ministers that are populer among netizens. So, what their thought about them? Did they say good sentimet or vise versa?

Let wordcloud answers your question. Wordcloud below is clustered wordcloud for top 4 popular ministers that mentioned the most by netizens.
![Clustered wordcloud over top 4 popular ministers.](/images/clustered_wordcloud.png)

**Wiranto**
Wiranto is linked to law issue since terms most appear around him are _‘kejahatan’, ‘dakwaan’_, and _‘ham’_.

**Anies Baswedan**
Anies Baswedan seems got positive impression by netizens. They thanks to Anies for his performance, netizens also dissappoint about the replacement of Anies Baswedan

**Sri Mulyani**
People are welcoming back Sri Mulyani, they hope she can heal ou economy.

**Ignatius Jonan**
Ignatius Jonan, got mixed sentimen from netizen. Some are agree with his replacement, others are disappoint regarding his replacement because they think Jonan is good enough.


<!--more-->
