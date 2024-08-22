---
title: "SMU Travel Map 2024: Carolina to California"
layout: single
excerpt_separator: "<!--more-->"
categories:
  - blog
tags:
  - maps
  - web scraping
  - python
  - folium
  - college football
  - smu
  - sports
  - football
date: 2024-08-21 10:00:00 EST
timezone: America/New_York
search: true
toc: false
---

<iframe src="/assets/maps/map_smuFootball2024.html" frameborder="0" allowfullscreen allow="fullscreen" style="width: 100%; aspect-ratio: 1 / 1; border: 0;"></iframe>

## Inspiration
With the recent conference shakeups in college football, some teams will now travel much further this year than ever before. One prominent example of this is **SMU**, who has **recently joined the ACC**. While based in the Highland Park neighborhood of Dallas, Texas, SMU will travel coast to coast to play their schedule this year.
<br><br>
While this this is no doubt difficult for die-hard Mustangs fans, it's great for random fans like me who will get to see a game much closer to home in North Carolina! Especially since I have a **personal connection** to the team... **my cousin!**
<br><br>
This project is an extension of a [previous post](/blog/2024-05-31-web-scraping-gb/), check that out if you want to see how I set up a web scraper to grab the info I needed. I'm only going to cover the major differences in this post.

## Web Scraping
I developed this project in a Python notebook, [see the raw code here](/smuFootballMap2024/). I used [FBSSchedules](https://fbschedules.com/2024-smu-football-schedule/) as my reference data. The most difficult part was handling bye weeks. Strangely, SMU has **3 bye weeks** this year!

## Mapping
I was able to draw lines connecting each week of the season to help visualize the travel path from week to week. I also styled the pins differently for home/away/bye/championship games.
