---
title: "George Birge Tour Map: A Web Scraping Exercise"
layout: single
excerpt_separator: "<!--more-->"
categories:
  - blog
tags:
  - maps
  - web scraping
  - python
  - folium
  - george birge
  - music
date: 2024-05-31 10:00:00 EST
timezone: America/New_York
search: true
toc: false
---

<iframe src="/assets/maps/map_GB.html" frameborder="0" allowfullscreen allow="fullscreen" style="width: 100%; aspect-ratio: 1 / 1; border: 0;"></iframe>

## Inspiration
I wanted to learn how to **create a dynamic map**, as well as **scrape and format data from the web**… so why not do both in one project!
[George Birge](https://www.georgebirge.com) is a rising country artist who plays shows across the country, and it just so happens that my brother helps to manage many aspects of his tours! This project gives me a way to see where all the performances will be geographically, and keep track of past performances.

## Web Scraping
I developed this project in a Python notebook, [see the raw code here](/pythonScrapeGeorgeBirge/). The web scraping is done with a Python package called [Selenium](https://pypi.org/project/selenium/), which quickly opens up a browser and grabs the data from the html tags that you supply. The key hurdle was **making sure that the scraper waited for the page to load dynamic data**, which www.georgebirge.com/tour has. Otherwise the scraper would return without finding the tour information.

## Processing the Data
I take all of the scraped data and put each performance into its own `Performance` object to keep track of the needed info.
### Reading/Exporting CSV
I also keep track of the performances in my own csv sheet, this way I still have a record of past performances. I read the performances from the csv and add any performances I’m missing. At the end of the program I **overwrite the existing csv file** with the new list of performances.
### Geolocating Cities
To find where to put the pin for each performance, I used the [Nominatum](https://nominatim.org/release-docs/latest/library/Getting-Started/) package to make geolocator requests for each city (including the state and country also). This free service uses **Open Street Maps** to fulfill the request. I also offset each pin by a very small random amount, just so that if there’s multiple performances in the same city they don’t overlap each other.

## Mapping
I used the [Folium](https://pypi.org/project/folium/) package to put all of the pins on a map. This easy-to-use package has a lot of features that I’d like to use in the future to make this map even better, maybe adding lines connecting the performances in chronological order? For now I just show future performances in blue and past performances in gray. The **map is saved as an html file** that I displayed on this webpage using an **iframe**.
