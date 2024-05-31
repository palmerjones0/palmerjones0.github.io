---
permalink: /pythonScrapeGeorgeBirge/
title: "Scraping and Mapping George Birge Tour Dates"
classes: wide
---

# Grab from George Birge website and display on a map

## Start by importing everything we need


```python
pip install selenium
```

    Requirement already satisfied: selenium in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (4.20.0)
    Requirement already satisfied: urllib3[socks]<3,>=1.26 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from selenium) (1.26.11)
    Requirement already satisfied: typing_extensions>=4.9.0 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from selenium) (4.11.0)
    Requirement already satisfied: trio-websocket~=0.9 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from selenium) (0.11.1)
    Requirement already satisfied: certifi>=2021.10.8 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from selenium) (2022.9.24)
    Requirement already satisfied: trio~=0.17 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from selenium) (0.25.0)
    Requirement already satisfied: attrs>=23.2.0 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from trio~=0.17->selenium) (23.2.0)
    Requirement already satisfied: idna in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from trio~=0.17->selenium) (3.3)
    Requirement already satisfied: sniffio>=1.3.0 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from trio~=0.17->selenium) (1.3.1)
    Requirement already satisfied: sortedcontainers in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from trio~=0.17->selenium) (2.4.0)
    Requirement already satisfied: exceptiongroup in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from trio~=0.17->selenium) (1.2.1)
    Requirement already satisfied: outcome in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from trio~=0.17->selenium) (1.3.0.post0)
    Requirement already satisfied: wsproto>=0.14 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from trio-websocket~=0.9->selenium) (1.2.0)
    Requirement already satisfied: PySocks!=1.5.7,<2.0,>=1.5.6 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from urllib3[socks]<3,>=1.26->selenium) (1.7.1)
    Requirement already satisfied: h11<1,>=0.9.0 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from wsproto>=0.14->trio-websocket~=0.9->selenium) (0.14.0)
    Note: you may need to restart the kernel to use updated packages.



```python
pip install folium
```

    Requirement already satisfied: folium in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (0.16.0)
    Requirement already satisfied: xyzservices in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from folium) (2024.4.0)
    Requirement already satisfied: branca>=0.6.0 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from folium) (0.7.2)
    Requirement already satisfied: numpy in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from folium) (1.26.4)
    Requirement already satisfied: requests in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from folium) (2.28.1)
    Requirement already satisfied: jinja2>=2.9 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from folium) (3.1.4)
    Requirement already satisfied: MarkupSafe>=2.0 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from jinja2>=2.9->folium) (2.0.1)
    Requirement already satisfied: certifi>=2017.4.17 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from requests->folium) (2022.9.24)
    Requirement already satisfied: charset-normalizer<3,>=2 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from requests->folium) (2.0.4)
    Requirement already satisfied: idna<4,>=2.5 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from requests->folium) (3.3)
    Requirement already satisfied: urllib3<1.27,>=1.21.1 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from requests->folium) (1.26.11)
    Note: you may need to restart the kernel to use updated packages.



```python
pip install geopy
```

    Requirement already satisfied: geopy in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (2.4.1)
    Requirement already satisfied: geographiclib<3,>=1.52 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from geopy) (2.0)
    Note: you may need to restart the kernel to use updated packages.



```python
from selenium import webdriver
from selenium.webdriver.common.by import By
```


```python
import pandas as pd
import os
import folium
from geopy.geocoders import Nominatim
```

    /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages/pandas/core/computation/expressions.py:21: UserWarning: Pandas requires version '2.8.4' or newer of 'numexpr' (version '2.8.3' currently installed).
      from pandas.core.computation.check import NUMEXPR_INSTALLED
    /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages/pandas/core/arrays/masked.py:60: UserWarning: Pandas requires version '1.3.6' or newer of 'bottleneck' (version '1.3.5' currently installed).
      from pandas.core import (



```python
from bs4 import BeautifulSoup
import re
```


```python
from datetime import datetime
```


```python
import random
```


```python
import csv
```

## All of the variables we need to run the program


```python
# URL of the website to scrape
url = 'https://www.georgebirge.com/tour'

# The csv file that we're saving all the performances to, and reading from
file_name = "performances_GB.csv"
```

## Do the web scraping


```python
# Initialize Safari WebDriver
driver = webdriver.Safari()
driver.implicitly_wait(10) #wait up to 10 secs for the page to load

# Open the URL in the browser
driver.get(url)

# define performance class
class Performance:
    def __init__(self, raw_html, raw_text):
        self.raw_html = raw_html
        self.raw_text = raw_text
        
# define performances list of objects
performances = []

# Find all div elements with class="sqs-tourdates__venue-name"
performance_cards = driver.find_elements(By.CLASS_NAME, 'sqs-tourdates__item')

# Save the html
for performance_card in performance_cards:
    # create the object for this performance card
    performance = Performance(performance_card.get_attribute('outerHTML'), '\n'.join(line for line in performance_card.text.strip().splitlines() if line.strip()))
    # append to the list of objects
    performances.append(performance)
    # print the raw text just to make sure we're grabbing the right thing
    #print(performance.raw_text)

# Close the browser
driver.quit()
```

## Break down the html data into the performance object properties


```python
# This will make displaying whatever info we want really easy down the road
# I'm going to use BeautifulSoup for this because I think it's a bit more user friendly

for performance in performances:
    # get each raw_html into BeautifulSoup
    soup = BeautifulSoup(performance.raw_html, 'html.parser')
    # find the datetime of the performance [0]
    performance.datetime = soup.find('span', class_='sqs-tourdates__timeframe')['data-tour-datetime']
    # find the date of the performance [1]
    performance.date = datetime.strptime(performance.datetime, "%Y-%m-%dT%H:%M:%S").strftime("%Y-%m-%d")
    # find the venue of the performance [2]
    performance.venue = soup.find('div', class_='sqs-tourdates__venue-name').text.strip().split('@')[0].strip()
    # find the location of the venue
    performance.location = soup.find('a', class_='sqs-tourdates__venue-link').text.strip()
    split_string = performance.location.split(',')
    performance.city = split_string[0].strip()  # Remove leading and trailing whitespace [3]
    performance.state = split_string[1].strip()  # Remove leading and trailing whitespace [4]
    performance.country = split_string[2].strip()  # Remove leading and trailing whitespace [5]
    # find the details (generally the other artists that will be there)
    performance_details = soup.find_all('div', class_='sqs-tourdates__lineup-item')
    performance.details = '' # set up as empty string [6]
    for performance_detail in performance_details:
        performance.details = performance.details + performance_detail.text.strip() + '|'
    performance.details = performance.details[:-1]
    # find the link to tickets
    try:
        performance.ticket_link = soup.find('a', class_='sqs-editable-button sqs-button-element--primary sqs-tourdates__button', text=lambda s: "Tickets" in s)['href'].strip()
    except:
        performance.ticket_link = ''
    # find the link to RSVP
    try:
        performance.rsvp_link = soup.find('a', class_='sqs-editable-button sqs-button-element--primary sqs-tourdates__button', text=lambda s: "RSVP" in s)['href'].strip()
    except:
        performance.rsvp_link = ''
    # find the link to Presale, this often doesn't exist so we just put None
    try:
        performance.presale_link = soup.find('a', class_='sqs-editable-button sqs-button-element--primary sqs-tourdates__button', text=lambda s: "Presale" in s)['href'].strip()
    except:
        performance.presale_link = ''
    performance.latitude = None # [7]
    performance.longitude = None # [8]
```

## For website performances, find the coordinates


```python
geolocator = Nominatim(user_agent="palmercjones@comcast.net")

for performance in performances:
    if (performance.latitude == None) or (performance.longitude == None):
        coordinates_query = f"{performance.city}, {performance.state}, {performance.country}"
        coordinates = geolocator.geocode(coordinates_query)
        performance.latitude = coordinates.latitude + random.uniform(-0.01, 0.01) # so pins don't fall directly on top of one another
        performance.longitude = coordinates.longitude + random.uniform(-0.01, 0.01)
    if datetime.strptime(performance.date, "%Y-%m-%d") > datetime.today():
        performance.color = "blue"
    else:
        performance.color = "lightgray"
    #print(performance.color)
    print(f"Coordinates for {performance.city}: Latitude = {performance.latitude}, Longitude = {performance.longitude}")
```

    Coordinates for Alpharetta: Latitude = 34.06756708540253, Longitude = -84.27134967358556
    Coordinates for Jacksonville: Latitude = 30.336827401223776, Longitude = -81.65069630244315
    Coordinates for Nashville: Latitude = 36.171365557542956, Longitude = -86.77342730802815
    Coordinates for Myrtle Beach: Latitude = 33.688202266164595, Longitude = -78.88935246405087
    Coordinates for Nashville: Latitude = 36.166439181403994, Longitude = -86.77076419984124
    Coordinates for Bend: Latitude = 44.05517213756802, Longitude = -121.31255800866063
    Coordinates for Auburn: Latitude = 47.30599077094418, Longitude = -122.23204990799502
    Coordinates for Nampa: Latitude = 43.56767959453048, Longitude = -116.56843349196099
    Coordinates for Harrisburg: Latitude = 40.27481225049882, Longitude = -76.88348970555096
    Coordinates for Camden: Latitude = 39.95080291470532, Longitude = -75.11150383353399
    Coordinates for Bristow: Latitude = 38.715987892413914, Longitude = -77.5458948872787
    Coordinates for Virginia Beach: Latitude = 36.84369763754082, Longitude = -75.96918500473055
    Coordinates for Dewey Beach: Latitude = 38.686384804945526, Longitude = -75.07618841824008
    Coordinates for Uncasville: Latitude = 41.435135368632295, Longitude = -72.11542203863725
    Coordinates for Cuyahoga Falls: Latitude = 41.144585744447234, Longitude = -81.48261838525265
    Coordinates for Burgettstown: Latitude = 40.38413642485206, Longitude = -80.39585314127807
    Coordinates for Jacksonville: Latitude = 30.341086687394412, Longitude = -81.66501074172102
    Coordinates for Charleston: Latitude = 32.79493092809718, Longitude = -79.94108099785572
    Coordinates for Raleigh: Latitude = 35.779116716251714, Longitude = -78.64395996790658
    Coordinates for Albuquerque: Latitude = 35.08275197749372, Longitude = -106.64680695747239
    Coordinates for Greenwood Village: Latitude = 39.62678817079165, Longitude = -104.94861537209026
    Coordinates for West Valley City: Latitude = 40.69711844862041, Longitude = -111.98878018951689
    Coordinates for Bonner: Latitude = 46.87756085529895, Longitude = -113.85686723780591
    Coordinates for San Diego: Latitude = 32.717605213761786, Longitude = -117.16783382916417
    Coordinates for Los Angeles: Latitude = 34.049765203470116, Longitude = -118.24991614673777
    Coordinates for San Bernardino: Latitude = 34.818532867760425, Longitude = -116.09207286347338
    Coordinates for Montréal: Latitude = 45.4971035229452, Longitude = -73.5795452340849
    Coordinates for Oklahoma City: Latitude = 35.463254203851506, Longitude = -97.52001535736535
    Coordinates for Des Moines: Latitude = 41.59206953341399, Longitude = -93.61590442137607
    Coordinates for Allentown: Latitude = 40.60201023720133, Longitude = -75.47616043037634
    Coordinates for Somerset: Latitude = 45.13164433194328, Longitude = -92.66519662357882
    Coordinates for Peoria: Latitude = 40.695042742034595, Longitude = -89.5897747086285
    Coordinates for Noblesville: Latitude = 40.05019319494188, Longitude = -86.01711577914554
    Coordinates for West Palm Beach: Latitude = 26.707647399562045, Longitude = -80.06327284521662
    Coordinates for Tampa: Latitude = 27.9514396314976, Longitude = -82.45432720069637
    Coordinates for Sacramento: Latitude = 38.57320891596573, Longitude = -121.49400703864724


## Import the existing csv of performances, if it exists, make into objects and then add to performances list


```python
# Initialize an empty list to store the data
csv_data = []

# Check if the file exists
if os.path.exists(file_name):
    # Open the file in read mode
    with open(file_name, 'r') as csv_file:
        # Create a CSV reader object
        csv_reader = csv.reader(csv_file)

        # Read each row from the CSV file and append it to the data list
        for row in csv_reader:
            csv_data.append(row)

    print("Data imported successfully:")
    #for row in csv_data:
    #    print(row)
else:
    print(f"The file {file_name} does not exist.")
```

    Data imported successfully:


## Compare the the new locations we found


```python
if len(csv_data) == 0:
    print("no data from csv")
else:
    for row in csv_data:
        match_found = False # initialize
        for performance in performances:
            if row[1] == performance.date:
                if row[3] == performance.city:
                    if row[2] == performance.venue:
                        print('Match found: ' + row[1] + ', ' + row[3] + ', ' + row[2])
                        match_found = True
                        break # if we find a match, we skip this performance and move on to the next row in csv_data without adding
        if not match_found:
            print('No match found.')
            performance = Performance('','') #skipped if we found a match in the website already there
            performance.datetime = row[0]
            performance.date = row[1]
            performance.venue = row[2]
            # performance.location = row[]
            performance.city = row[3]
            performance.state = row[4]
            performance.country = row[5]
            performance.details = row[6]
            # performance.ticket_link
            # performance.rsvp_link
            # performance.presale_link
            performance.latitude = row[7]
            performance.longitude = row[8]
            if datetime.strptime(performance.date, "%Y-%m-%d") < datetime.today():
                performance.color = "blue"
            else:
                performance.color = "lightgray"
            performances.append(performance) # append to performances
            print(performance.city + ', ' + performance.state)
```

    Match found: 2024-05-31, Alpharetta, Ameris Bank Amphitheatre
    Match found: 2024-06-01, Jacksonville, Daily's Place
    Match found: 2024-06-06, Nashville, CMA Music Festival 2024
    Match found: 2024-06-07, Myrtle Beach, Carolina Country Music Festival 2024
    Match found: 2024-06-08, Nashville, Tin Roof Broadway
    Match found: 2024-06-28, Bend, Hayden Homes Amphitheater
    Match found: 2024-06-29, Auburn, White River Amphitheatre
    Match found: 2024-06-30, Nampa, Ford Idaho Center Amphitheater
    Match found: 2024-07-10, Harrisburg, XL Live
    Match found: 2024-07-11, Camden, Freedom Mortgage Pavilion
    Match found: 2024-07-12, Bristow, Jiffy Lube Live
    Match found: 2024-07-13, Virginia Beach, Veterans United Home Loans Amphitheater at Virginia Beach
    Match found: 2024-07-14, Dewey Beach, Bottle & Cork
    Match found: 2024-07-18, Uncasville, Mohegan Sun Arena
    Match found: 2024-07-19, Cuyahoga Falls, Blossom Music Center
    Match found: 2024-07-20, Burgettstown, The Pavilion at Star Lake
    Match found: 2024-07-25, Jacksonville, Daily's Place
    Match found: 2024-07-26, Charleston, Credit One Stadium
    Match found: 2024-07-27, Raleigh, Coastal Credit Union Music Park At Walnut Creek
    Match found: 2024-08-01, Albuquerque, Isleta Amphitheater
    Match found: 2024-08-02, Greenwood Village, Fiddler's Green Amphitheatre
    Match found: 2024-08-03, West Valley City, Utah First Credit Union Amphitheatre
    Match found: 2024-08-04, Bonner, KettleHouse Amphitheater
    Match found: 2024-08-08, San Diego, North Island Credit Union Amphitheatre
    Match found: 2024-08-09, Los Angeles, The Kia Forum
    Match found: 2024-08-10, San Bernardino, Glen Helen Amphitheater
    Match found: 2024-08-17, Montréal, Lasso  2024
    Match found: 2024-08-22, Oklahoma City, Paycom Center
    Match found: 2024-08-24, Des Moines, Wells Fargo Arena
    Match found: 2024-08-30, Allentown, Allentown Fairgrounds
    Match found: 2024-09-05, Somerset, Somerset Amphitheater
    Match found: 2024-09-06, Peoria, Crusens Farmington Road
    Match found: 2024-09-07, Noblesville, Ruoff Music Center
    Match found: 2024-09-12, West Palm Beach, iTHINK Financial Amphitheatre
    Match found: 2024-09-14, Tampa, MIDFLORIDA Credit Union Amphitheatre
    Match found: 2024-10-19, Sacramento, GoldenSky Festival 2024


## Put the performances in order

## Do the mapping and save to html file


```python
# Get the directory where the Python script or notebook is located
current_dir = os.path.dirname(os.path.abspath('pythonScrapeGeorgeBirge.ipynb'))

# Set the current working directory to the directory of the Python script or notebook
os.chdir(current_dir)

# Create a map centered at the geographical center of the US
m = folium.Map(location=[39.8283, -98.5795], zoom_start=4)

# Add markers for each location
for performance in performances:
    folium.Marker(
        location=[performance.latitude, performance.longitude],
        tooltip=(performance.venue + '<br>' +performance.city + ', ' + performance.state + '<br>' + performance.date),
        icon=folium.Icon(color=performance.color)
    ).add_to(m)

# Specify the path to save the HTML file
html_file_path = os.path.join(current_dir, 'map_GB.html')

# Save the map to an HTML file in the current directory
m.save(html_file_path)

print(f"Map saved to: {html_file_path}")

m
```

    Map saved to: /Users/palmerjones/Website/Projects/map_GB.html





<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><span style="color:#565656">Make this Notebook Trusted to load map: File -> Trust Notebook</span><iframe srcdoc="&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;

    &lt;meta http-equiv=&quot;content-type&quot; content=&quot;text/html; charset=UTF-8&quot; /&gt;

        &lt;script&gt;
            L_NO_TOUCH = false;
            L_DISABLE_3D = false;
        &lt;/script&gt;

    &lt;style&gt;html, body {width: 100%;height: 100%;margin: 0;padding: 0;}&lt;/style&gt;
    &lt;style&gt;#map {position:absolute;top:0;bottom:0;right:0;left:0;}&lt;/style&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/npm/leaflet@1.9.3/dist/leaflet.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://code.jquery.com/jquery-3.7.1.min.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/js/bootstrap.bundle.min.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/leaflet@1.9.3/dist/leaflet.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/css/bootstrap.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/@fortawesome/fontawesome-free@6.2.0/css/all.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/gh/python-visualization/folium/folium/templates/leaflet.awesome.rotate.min.css&quot;/&gt;

            &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width,
                initial-scale=1.0, maximum-scale=1.0, user-scalable=no&quot; /&gt;
            &lt;style&gt;
                #map_4a10750bec240798ebb340911bb182a9 {
                    position: relative;
                    width: 100.0%;
                    height: 100.0%;
                    left: 0.0%;
                    top: 0.0%;
                }
                .leaflet-container { font-size: 1rem; }
            &lt;/style&gt;

&lt;/head&gt;
&lt;body&gt;


            &lt;div class=&quot;folium-map&quot; id=&quot;map_4a10750bec240798ebb340911bb182a9&quot; &gt;&lt;/div&gt;

&lt;/body&gt;
&lt;script&gt;


            var map_4a10750bec240798ebb340911bb182a9 = L.map(
                &quot;map_4a10750bec240798ebb340911bb182a9&quot;,
                {
                    center: [39.8283, -98.5795],
                    crs: L.CRS.EPSG3857,
                    zoom: 4,
                    zoomControl: true,
                    preferCanvas: false,
                }
            );





            var tile_layer_f69ec40a754eb434b9ad41dcdd62eeda = L.tileLayer(
                &quot;https://tile.openstreetmap.org/{z}/{x}/{y}.png&quot;,
                {&quot;attribution&quot;: &quot;\u0026copy; \u003ca href=\&quot;https://www.openstreetmap.org/copyright\&quot;\u003eOpenStreetMap\u003c/a\u003e contributors&quot;, &quot;detectRetina&quot;: false, &quot;maxNativeZoom&quot;: 19, &quot;maxZoom&quot;: 19, &quot;minZoom&quot;: 0, &quot;noWrap&quot;: false, &quot;opacity&quot;: 1, &quot;subdomains&quot;: &quot;abc&quot;, &quot;tms&quot;: false}
            );


            tile_layer_f69ec40a754eb434b9ad41dcdd62eeda.addTo(map_4a10750bec240798ebb340911bb182a9);


            var marker_7cbad24000f6adb5306120e80190ff92 = L.marker(
                [34.06756708540253, -84.27134967358556],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_4245f4cfe2096ffcc1891b21c5e68801 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;lightgray&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_7cbad24000f6adb5306120e80190ff92.setIcon(icon_4245f4cfe2096ffcc1891b21c5e68801);


            marker_7cbad24000f6adb5306120e80190ff92.bindTooltip(
                `&lt;div&gt;
                     Ameris Bank Amphitheatre&lt;br&gt;Alpharetta, GA&lt;br&gt;2024-05-31
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_c687ebb6e456ca6ca76ed5f658b70b56 = L.marker(
                [30.336827401223776, -81.65069630244315],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_1dc6c28474da89b60a1f9b664f20a411 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_c687ebb6e456ca6ca76ed5f658b70b56.setIcon(icon_1dc6c28474da89b60a1f9b664f20a411);


            marker_c687ebb6e456ca6ca76ed5f658b70b56.bindTooltip(
                `&lt;div&gt;
                     Daily&#x27;s Place&lt;br&gt;Jacksonville, FL&lt;br&gt;2024-06-01
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_cc97a51f5103737447f13d966c66f15d = L.marker(
                [36.171365557542956, -86.77342730802815],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_e9d6c9b79255b6698fb115933895791e = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_cc97a51f5103737447f13d966c66f15d.setIcon(icon_e9d6c9b79255b6698fb115933895791e);


            marker_cc97a51f5103737447f13d966c66f15d.bindTooltip(
                `&lt;div&gt;
                     CMA Music Festival 2024&lt;br&gt;Nashville, TN&lt;br&gt;2024-06-06
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_76faacb39caa41e637056cfdefaa555b = L.marker(
                [33.688202266164595, -78.88935246405087],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_043e537514541c75fd771b3f44a35691 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_76faacb39caa41e637056cfdefaa555b.setIcon(icon_043e537514541c75fd771b3f44a35691);


            marker_76faacb39caa41e637056cfdefaa555b.bindTooltip(
                `&lt;div&gt;
                     Carolina Country Music Festival 2024&lt;br&gt;Myrtle Beach, SC&lt;br&gt;2024-06-07
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_24adc33a927662e049898274e31ef902 = L.marker(
                [36.166439181403994, -86.77076419984124],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_ea01ec51203b051de4b7e4dae2e9c7a9 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_24adc33a927662e049898274e31ef902.setIcon(icon_ea01ec51203b051de4b7e4dae2e9c7a9);


            marker_24adc33a927662e049898274e31ef902.bindTooltip(
                `&lt;div&gt;
                     Tin Roof Broadway&lt;br&gt;Nashville, TN&lt;br&gt;2024-06-08
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_98523df87833c074ff6ef0daa62f9626 = L.marker(
                [44.05517213756802, -121.31255800866063],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_1603aba17ef3d93f56149852b4bbcbb1 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_98523df87833c074ff6ef0daa62f9626.setIcon(icon_1603aba17ef3d93f56149852b4bbcbb1);


            marker_98523df87833c074ff6ef0daa62f9626.bindTooltip(
                `&lt;div&gt;
                     Hayden Homes Amphitheater&lt;br&gt;Bend, OR&lt;br&gt;2024-06-28
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_c3c1ad6a04b82df187e325e8135f95d6 = L.marker(
                [47.30599077094418, -122.23204990799502],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_52aaf8f865ac2a8478f3df02edeaa154 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_c3c1ad6a04b82df187e325e8135f95d6.setIcon(icon_52aaf8f865ac2a8478f3df02edeaa154);


            marker_c3c1ad6a04b82df187e325e8135f95d6.bindTooltip(
                `&lt;div&gt;
                     White River Amphitheatre&lt;br&gt;Auburn, WA&lt;br&gt;2024-06-29
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_6bade40dc2e7a6652d240946e0e41bc0 = L.marker(
                [43.56767959453048, -116.56843349196099],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_a9185539668c31cd2bdda354a030714f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_6bade40dc2e7a6652d240946e0e41bc0.setIcon(icon_a9185539668c31cd2bdda354a030714f);


            marker_6bade40dc2e7a6652d240946e0e41bc0.bindTooltip(
                `&lt;div&gt;
                     Ford Idaho Center Amphitheater&lt;br&gt;Nampa, ID&lt;br&gt;2024-06-30
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_34aec472682fa63a73f7b546611b5500 = L.marker(
                [40.27481225049882, -76.88348970555096],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_bb66a33c72e4b137c89eb9e2b3d3e97d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_34aec472682fa63a73f7b546611b5500.setIcon(icon_bb66a33c72e4b137c89eb9e2b3d3e97d);


            marker_34aec472682fa63a73f7b546611b5500.bindTooltip(
                `&lt;div&gt;
                     XL Live&lt;br&gt;Harrisburg, PA&lt;br&gt;2024-07-10
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_5fe16947025492a279250275977bbeff = L.marker(
                [39.95080291470532, -75.11150383353399],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_62459887a5444a4798b8f73363f303cb = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_5fe16947025492a279250275977bbeff.setIcon(icon_62459887a5444a4798b8f73363f303cb);


            marker_5fe16947025492a279250275977bbeff.bindTooltip(
                `&lt;div&gt;
                     Freedom Mortgage Pavilion&lt;br&gt;Camden, NJ&lt;br&gt;2024-07-11
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_116018aae60c22e83d587215fd81f247 = L.marker(
                [38.715987892413914, -77.5458948872787],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_5e2aa6735dac0992edeea875f4c0ac59 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_116018aae60c22e83d587215fd81f247.setIcon(icon_5e2aa6735dac0992edeea875f4c0ac59);


            marker_116018aae60c22e83d587215fd81f247.bindTooltip(
                `&lt;div&gt;
                     Jiffy Lube Live&lt;br&gt;Bristow, VA&lt;br&gt;2024-07-12
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_549f520a057b71a509285ca6d7df9f3b = L.marker(
                [36.84369763754082, -75.96918500473055],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_784276c79365efa154c085db366dd985 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_549f520a057b71a509285ca6d7df9f3b.setIcon(icon_784276c79365efa154c085db366dd985);


            marker_549f520a057b71a509285ca6d7df9f3b.bindTooltip(
                `&lt;div&gt;
                     Veterans United Home Loans Amphitheater at Virginia Beach&lt;br&gt;Virginia Beach, VA&lt;br&gt;2024-07-13
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_ef11fb804cfafc3354a5efef526619ad = L.marker(
                [38.686384804945526, -75.07618841824008],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_0a356f65911dfbbccec695d641e64d2f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ef11fb804cfafc3354a5efef526619ad.setIcon(icon_0a356f65911dfbbccec695d641e64d2f);


            marker_ef11fb804cfafc3354a5efef526619ad.bindTooltip(
                `&lt;div&gt;
                     Bottle &amp; Cork&lt;br&gt;Dewey Beach, DE&lt;br&gt;2024-07-14
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_5418b52c531299048c2823deeaa81343 = L.marker(
                [41.435135368632295, -72.11542203863725],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_484ff87cb383abf166d7098daf3f8dcd = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_5418b52c531299048c2823deeaa81343.setIcon(icon_484ff87cb383abf166d7098daf3f8dcd);


            marker_5418b52c531299048c2823deeaa81343.bindTooltip(
                `&lt;div&gt;
                     Mohegan Sun Arena&lt;br&gt;Uncasville, CT&lt;br&gt;2024-07-18
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_57edff6baa4b426b6302b311ec3cdb1d = L.marker(
                [41.144585744447234, -81.48261838525265],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_dc23131bb2752055eb189e4ecdff62da = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_57edff6baa4b426b6302b311ec3cdb1d.setIcon(icon_dc23131bb2752055eb189e4ecdff62da);


            marker_57edff6baa4b426b6302b311ec3cdb1d.bindTooltip(
                `&lt;div&gt;
                     Blossom Music Center&lt;br&gt;Cuyahoga Falls, OH&lt;br&gt;2024-07-19
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_b34aa1e17cb24aeaccb42b9bb956de79 = L.marker(
                [40.38413642485206, -80.39585314127807],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_1c7043da5e93dabac310a4f11c92dcff = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b34aa1e17cb24aeaccb42b9bb956de79.setIcon(icon_1c7043da5e93dabac310a4f11c92dcff);


            marker_b34aa1e17cb24aeaccb42b9bb956de79.bindTooltip(
                `&lt;div&gt;
                     The Pavilion at Star Lake&lt;br&gt;Burgettstown, PA&lt;br&gt;2024-07-20
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_bf5b0edd0371fb15128583dadf16bc74 = L.marker(
                [30.341086687394412, -81.66501074172102],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_075a333161b394b443e3a378063b5ff8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_bf5b0edd0371fb15128583dadf16bc74.setIcon(icon_075a333161b394b443e3a378063b5ff8);


            marker_bf5b0edd0371fb15128583dadf16bc74.bindTooltip(
                `&lt;div&gt;
                     Daily&#x27;s Place&lt;br&gt;Jacksonville, FL&lt;br&gt;2024-07-25
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_56d6c6696af825411b03d856d0d39b9c = L.marker(
                [32.79493092809718, -79.94108099785572],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_820fce5d40d3d3c9a858ba91dcf209d6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_56d6c6696af825411b03d856d0d39b9c.setIcon(icon_820fce5d40d3d3c9a858ba91dcf209d6);


            marker_56d6c6696af825411b03d856d0d39b9c.bindTooltip(
                `&lt;div&gt;
                     Credit One Stadium&lt;br&gt;Charleston, SC&lt;br&gt;2024-07-26
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_360116812a57f55976b861948d8cfa7d = L.marker(
                [35.779116716251714, -78.64395996790658],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_aa5df5c105bf620547251b4c4f295005 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_360116812a57f55976b861948d8cfa7d.setIcon(icon_aa5df5c105bf620547251b4c4f295005);


            marker_360116812a57f55976b861948d8cfa7d.bindTooltip(
                `&lt;div&gt;
                     Coastal Credit Union Music Park At Walnut Creek&lt;br&gt;Raleigh, NC&lt;br&gt;2024-07-27
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_786f8a4d743a5b84e78ba00dfc17c8ee = L.marker(
                [35.08275197749372, -106.64680695747239],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_6c3fce11a07bbf36eae249c9243ad52c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_786f8a4d743a5b84e78ba00dfc17c8ee.setIcon(icon_6c3fce11a07bbf36eae249c9243ad52c);


            marker_786f8a4d743a5b84e78ba00dfc17c8ee.bindTooltip(
                `&lt;div&gt;
                     Isleta Amphitheater&lt;br&gt;Albuquerque, NM&lt;br&gt;2024-08-01
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_62b5891463d59eb1151d8ed2364f1d57 = L.marker(
                [39.62678817079165, -104.94861537209026],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_47e82db525303f0d465b4b7ad80a0ee6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_62b5891463d59eb1151d8ed2364f1d57.setIcon(icon_47e82db525303f0d465b4b7ad80a0ee6);


            marker_62b5891463d59eb1151d8ed2364f1d57.bindTooltip(
                `&lt;div&gt;
                     Fiddler&#x27;s Green Amphitheatre&lt;br&gt;Greenwood Village, CO&lt;br&gt;2024-08-02
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_26bdbe25e2a77042a66434bd8b20e8ad = L.marker(
                [40.69711844862041, -111.98878018951689],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_689e335d9feb8ed451253e8b01514b8f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_26bdbe25e2a77042a66434bd8b20e8ad.setIcon(icon_689e335d9feb8ed451253e8b01514b8f);


            marker_26bdbe25e2a77042a66434bd8b20e8ad.bindTooltip(
                `&lt;div&gt;
                     Utah First Credit Union Amphitheatre&lt;br&gt;West Valley City, UT&lt;br&gt;2024-08-03
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_6f03eb08bd30b9aff3450eb56f725b1d = L.marker(
                [46.87756085529895, -113.85686723780591],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_60350f16a83527a6f5069c2b075e3756 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_6f03eb08bd30b9aff3450eb56f725b1d.setIcon(icon_60350f16a83527a6f5069c2b075e3756);


            marker_6f03eb08bd30b9aff3450eb56f725b1d.bindTooltip(
                `&lt;div&gt;
                     KettleHouse Amphitheater&lt;br&gt;Bonner, MT&lt;br&gt;2024-08-04
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_cd048a1045249ab4539083c077361f56 = L.marker(
                [32.717605213761786, -117.16783382916417],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_6306b02158e7da096de2cd3e50e5ebd3 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_cd048a1045249ab4539083c077361f56.setIcon(icon_6306b02158e7da096de2cd3e50e5ebd3);


            marker_cd048a1045249ab4539083c077361f56.bindTooltip(
                `&lt;div&gt;
                     North Island Credit Union Amphitheatre&lt;br&gt;San Diego, CA&lt;br&gt;2024-08-08
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_4f7ec5372cb41d4138d6f93848cd3647 = L.marker(
                [34.049765203470116, -118.24991614673777],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_544b83a2a14d067cad1389db7a94741a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_4f7ec5372cb41d4138d6f93848cd3647.setIcon(icon_544b83a2a14d067cad1389db7a94741a);


            marker_4f7ec5372cb41d4138d6f93848cd3647.bindTooltip(
                `&lt;div&gt;
                     The Kia Forum&lt;br&gt;Los Angeles, CA&lt;br&gt;2024-08-09
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_0a0de611a6bf02c8a5f55c091207afdf = L.marker(
                [34.818532867760425, -116.09207286347338],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_b96bc6b15f7c0b8cb6dc84b6287366dd = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0a0de611a6bf02c8a5f55c091207afdf.setIcon(icon_b96bc6b15f7c0b8cb6dc84b6287366dd);


            marker_0a0de611a6bf02c8a5f55c091207afdf.bindTooltip(
                `&lt;div&gt;
                     Glen Helen Amphitheater&lt;br&gt;San Bernardino, CA&lt;br&gt;2024-08-10
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_5772eb749216d3ee88be5f08e68cc9a7 = L.marker(
                [45.4971035229452, -73.5795452340849],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_19c613e28e82ed8502e08432ac328027 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_5772eb749216d3ee88be5f08e68cc9a7.setIcon(icon_19c613e28e82ed8502e08432ac328027);


            marker_5772eb749216d3ee88be5f08e68cc9a7.bindTooltip(
                `&lt;div&gt;
                     Lasso  2024&lt;br&gt;Montréal, QC&lt;br&gt;2024-08-17
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_30366df9acfba4af494485bb15f98bc6 = L.marker(
                [35.463254203851506, -97.52001535736535],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_a0186c24915d68f34823b3d410ab53c6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_30366df9acfba4af494485bb15f98bc6.setIcon(icon_a0186c24915d68f34823b3d410ab53c6);


            marker_30366df9acfba4af494485bb15f98bc6.bindTooltip(
                `&lt;div&gt;
                     Paycom Center&lt;br&gt;Oklahoma City, OK&lt;br&gt;2024-08-22
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_c2451f4b74471caee5f742983e0caa9c = L.marker(
                [41.59206953341399, -93.61590442137607],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_ce51dd8421a2221279b24ec786d50cb2 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_c2451f4b74471caee5f742983e0caa9c.setIcon(icon_ce51dd8421a2221279b24ec786d50cb2);


            marker_c2451f4b74471caee5f742983e0caa9c.bindTooltip(
                `&lt;div&gt;
                     Wells Fargo Arena&lt;br&gt;Des Moines, IA&lt;br&gt;2024-08-24
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_2d5a9e441a1ce4a59c09434342b2dd90 = L.marker(
                [40.60201023720133, -75.47616043037634],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_d86ef1ca24295e7fd239dbe8c942c0e8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2d5a9e441a1ce4a59c09434342b2dd90.setIcon(icon_d86ef1ca24295e7fd239dbe8c942c0e8);


            marker_2d5a9e441a1ce4a59c09434342b2dd90.bindTooltip(
                `&lt;div&gt;
                     Allentown Fairgrounds&lt;br&gt;Allentown, PA&lt;br&gt;2024-08-30
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_ca9eea950379f4b44a10ec7f3b4ad735 = L.marker(
                [45.13164433194328, -92.66519662357882],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_f7ecfaf8474567332afb7baffc13a0c1 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ca9eea950379f4b44a10ec7f3b4ad735.setIcon(icon_f7ecfaf8474567332afb7baffc13a0c1);


            marker_ca9eea950379f4b44a10ec7f3b4ad735.bindTooltip(
                `&lt;div&gt;
                     Somerset Amphitheater&lt;br&gt;Somerset, WI&lt;br&gt;2024-09-05
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_3969dedd53333c77a7136e09de209df5 = L.marker(
                [40.695042742034595, -89.5897747086285],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_975bd9e549680d93df5a051fb197ca14 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_3969dedd53333c77a7136e09de209df5.setIcon(icon_975bd9e549680d93df5a051fb197ca14);


            marker_3969dedd53333c77a7136e09de209df5.bindTooltip(
                `&lt;div&gt;
                     Crusens Farmington Road&lt;br&gt;Peoria, IL&lt;br&gt;2024-09-06
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_9cf43856ac2cf2e6f17e161ab003b2ee = L.marker(
                [40.05019319494188, -86.01711577914554],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_eca3335c6033c103fecc24ac244b9255 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9cf43856ac2cf2e6f17e161ab003b2ee.setIcon(icon_eca3335c6033c103fecc24ac244b9255);


            marker_9cf43856ac2cf2e6f17e161ab003b2ee.bindTooltip(
                `&lt;div&gt;
                     Ruoff Music Center&lt;br&gt;Noblesville, IN&lt;br&gt;2024-09-07
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_7a10ef13cc6129e08696d44aeb6efd9a = L.marker(
                [26.707647399562045, -80.06327284521662],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_9071aaceac155bac26ea2540b91352cd = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_7a10ef13cc6129e08696d44aeb6efd9a.setIcon(icon_9071aaceac155bac26ea2540b91352cd);


            marker_7a10ef13cc6129e08696d44aeb6efd9a.bindTooltip(
                `&lt;div&gt;
                     iTHINK Financial Amphitheatre&lt;br&gt;West Palm Beach, FL&lt;br&gt;2024-09-12
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_118155f30fdb62b37827d0cdf2b2c0a9 = L.marker(
                [27.9514396314976, -82.45432720069637],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_ef727ac01af827c861b6f4d6c182a9dd = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_118155f30fdb62b37827d0cdf2b2c0a9.setIcon(icon_ef727ac01af827c861b6f4d6c182a9dd);


            marker_118155f30fdb62b37827d0cdf2b2c0a9.bindTooltip(
                `&lt;div&gt;
                     MIDFLORIDA Credit Union Amphitheatre&lt;br&gt;Tampa, FL&lt;br&gt;2024-09-14
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_7c7e044b9e47899014e900a56353c7da = L.marker(
                [38.57320891596573, -121.49400703864724],
                {}
            ).addTo(map_4a10750bec240798ebb340911bb182a9);


            var icon_44accfc4404f5a004568dc43d6636a3c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;blue&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_7c7e044b9e47899014e900a56353c7da.setIcon(icon_44accfc4404f5a004568dc43d6636a3c);


            marker_7c7e044b9e47899014e900a56353c7da.bindTooltip(
                `&lt;div&gt;
                     GoldenSky Festival 2024&lt;br&gt;Sacramento, CA&lt;br&gt;2024-10-19
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            tile_layer_f69ec40a754eb434b9ad41dcdd62eeda.addTo(map_4a10750bec240798ebb340911bb182a9);

&lt;/script&gt;
&lt;/html&gt;" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



## Save to CSV


```python
# Save locations to csv

# Open the file in write mode with newline='' to prevent extra blank lines
with open(file_name, 'w', newline='') as csv_file:
    # Create a CSV writer object
    csv_writer = csv.writer(csv_file)
    
    # Write the header row
    #csv_writer.writerow(["Name", "Age", "City"])
    
    # Write the data rows
    for performance in performances:
        csv_writer.writerow([performance.datetime, performance.date, performance.venue, performance.city, performance.state, performance.country, performance.details, performance.latitude, performance.longitude])

print(f"Data saved to {file_name}")
```

    Data saved to performances_GB.csv
