---
title: How We Built an Automated Data Pipeline (Without Being a Data Engineer)
author: Package Build
date: '2025-11-10'
slug: []
categories:
  - Opinion
tags:
  - etl
  - dataengineering
lastmod: '2025-11-11T11:27:48+07:00'
description: ''
show_in_homepage: yes
show_description: no
license: ''
featured_image: '/images/etl_arch.png'
featured_image_preview: '/images/etl_arch.png'
comment: yes
toc: no
autoCollapseToc: yes
math: no
---

<!--more-->
This is the story of how ~~3 people~~ a small team from a digital marketing agency tackled a big data engineering project, despite having zero 'official' data engineers on staff. (Sure, I’d been around DE projects in a previous job, but I was never the one hands-on building the system). As a digital agency who run tons of marketing campaigns daily, we face a constant, real-world headache: pulling reporting data from dozens of client accounts across a ton of ad platforms (like Facebook Ads, Google Ads, TikTok Ads, etc) and other sources (like GA4, google sheets and so on)

Before this project, we were subscribing to 3rd-party tools for our data collection. Honestly, they weren't that expensive, but we paid for it in other ways. They were painfully slow and would error out constantly. This was a nightmare for us, especially when it affected the external reports and dashboards we shared with clients—it sacrificed our credibility. So, we decided to find a better way.

Our goal was to build an automated pipeline that would:

1. Run on a schedule.

2. Pull data from APIs (Facebook, TikTok, etc.).

3. Be secure and accessible to the team.

4. Be faster and more reliable than our old 3rd party tools. 

5. Bonus if it's cheaper.

> Spoiler: We did it. And the best part? The core tool we used is "low-code," which means we didn't have to write any complex data-processing scripts. If you're in a similar boat, here’s how we built it.

## The "A-ha!" Moment: Discovering Airbyte

Our first breakthrough was finding Airbyte Open Source.

This tool is a game-changer. It's an ELT platform that handles the "E" (Extract) and "L" (Load) parts. Instead of writing code to handle API authentication, pagination, and schemas, you just pick from a huge list of pre-built connectors.

Want to pull from Facebook Ads and send it to a Google Sheet or a Bigquery warehouse? You just select the "Source," then the "Destination," fill in your credentials, and click "Go." Airbyte handles the rest. This was the "low-code" superpower we were looking for.

## The Stack: Building a "Serverless" Server on the Cheap

We decided to host Airbyte ourselves using the open-source version. This gave us full control. Here's the high-level architecture we landed on—it sounds complex, but it's just a few smart pieces working together.

![](/images/etl_arch.png)

### The Foundation: GCP Virtual Machine
This is just a basic computer (a "VM") running in the Google Cloud (GCP). We chose a custom machine that fit our needs and ~~budget~~ workload.

### The "Brains": Kubernetes (k3s)
Instead of just running Airbyte directly on the VM, We used k3s (this is suggestion from *Gemini*, actually), which is a lightweight version of Kubernetes. This is a "container orchestrator", which is a fancy way of saying it’s a smart manager for all the little services that Airbyte needs to run. It makes sure everything starts up correctly and can recover from crashes.

### The Cost-Saver: Cloud Scheduler
*Anak jaksel* said, **In this economy**, why pay for a server to run 24/7 when we only need to sync data once a day? we set up GCP's Cloud Scheduler to automatically start and stop at certain time every day. The pipeline runs, gets the data, and the server shuts down.

### The Front Door: Nginx & DuckDNS
This is where it got tricky. Because our VM shuts down every day, GCP gives it a new IP address every time it starts. This is a problem: we need a stable, permanent URL (like airbyte.mycompany.com) for the team to log in.

**DuckDNS**: Again, in this economy, cost free is number one priority. DuckDNS is a free service that gives you a domain name (like cool-etl-tool.duckdns.org, *of-course-you-can-not-have-a-fancy-custom-name*). We ask AI to wrote a simple script that runs every time the VM starts, telling DuckDNS, "Hey, here's my new IP address!"

**Nginx**: This is a web server that acts as the "front door." When I access my DuckDNS domain, the request hits Nginx first. Nginx handles two crucial jobs:

1. Security: It provides a simple password pop-up (Basic Auth) so only authorize people can access the tool.

2. Routing: It forwards request to the Airbyte application running inside Kubernetes. It also handles the https:// (SSL) part, making the connection secure.

### The Struggle Was Real
I'll be honest—it wasn't a smooth journey. As a non-engineer, we definitely hit a many walls. We faced problems here and there, especially with getting all the networking and security layers to talk to each other correctly. After a lot of troubleshooting (and plenty of help from my good friend Gemini, chatGPT, etc), we finally got everything working. Our in-house data pipeline tool is officially running and live.

## Results

Building this system is a win for us. We now have a reliable, automated data pipeline, with the results:

* **Reporting is 2-3 times faster** because the data is available in our BI tools almost immediately. 

* **It's 40% cheaper** than the 3rd party tools we were paying for.

* We have **full control** over our own data, from the pipeline to the warehouse.

* **Most importantly, our reports are far more reliable**. A failure in the pipeline no longer breaks our dashboards, which means we can share reports with clients confidently.


The biggest takeaway for us is that the "low-code" movement is real. Tools like Airbyte (and AI) empower people like me and my team from an digital marketing agency to build systems that were once reserved for specialized engineering teams. 

By combining a low-code tool (Airbyte) with some basic, learnable "ops" tools (GCP, Nginx), We were able to build a professional-grade solution that saves our team money and hours of work every single week.
