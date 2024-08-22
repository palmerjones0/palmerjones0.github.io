---
permalink: /smuFootballMap2024/
title: "Scraping and Mapping SMU Football Travel 2024"
classes: wide
---


## Install Dependencies


```python
pip install selenium
```

    Requirement already satisfied: selenium in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (4.20.0)
    Requirement already satisfied: urllib3[socks]<3,>=1.26 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from selenium) (1.26.11)
    Requirement already satisfied: trio~=0.17 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from selenium) (0.25.0)
    Requirement already satisfied: certifi>=2021.10.8 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from selenium) (2022.9.24)
    Requirement already satisfied: trio-websocket~=0.9 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from selenium) (0.11.1)
    Requirement already satisfied: typing_extensions>=4.9.0 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from selenium) (4.11.0)
    Requirement already satisfied: attrs>=23.2.0 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from trio~=0.17->selenium) (23.2.0)
    Requirement already satisfied: idna in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from trio~=0.17->selenium) (3.3)
    Requirement already satisfied: sniffio>=1.3.0 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from trio~=0.17->selenium) (1.3.1)
    Requirement already satisfied: sortedcontainers in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from trio~=0.17->selenium) (2.4.0)
    Requirement already satisfied: outcome in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from trio~=0.17->selenium) (1.3.0.post0)
    Requirement already satisfied: exceptiongroup in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from trio~=0.17->selenium) (1.2.1)
    Requirement already satisfied: wsproto>=0.14 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from trio-websocket~=0.9->selenium) (1.2.0)
    Requirement already satisfied: PySocks!=1.5.7,<2.0,>=1.5.6 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from urllib3[socks]<3,>=1.26->selenium) (1.7.1)
    Requirement already satisfied: h11<1,>=0.9.0 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from wsproto>=0.14->trio-websocket~=0.9->selenium) (0.14.0)
    Note: you may need to restart the kernel to use updated packages.



```python
pip install folium
```

    Requirement already satisfied: folium in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (0.16.0)
    Requirement already satisfied: numpy in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from folium) (1.26.4)
    Requirement already satisfied: branca>=0.6.0 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from folium) (0.7.2)
    Requirement already satisfied: jinja2>=2.9 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from folium) (3.1.4)
    Requirement already satisfied: requests in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from folium) (2.28.1)
    Requirement already satisfied: xyzservices in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from folium) (2024.4.0)
    Requirement already satisfied: MarkupSafe>=2.0 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from jinja2>=2.9->folium) (2.0.1)
    Requirement already satisfied: charset-normalizer<3,>=2 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from requests->folium) (2.0.4)
    Requirement already satisfied: urllib3<1.27,>=1.21.1 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from requests->folium) (1.26.11)
    Requirement already satisfied: certifi>=2017.4.17 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from requests->folium) (2022.9.24)
    Requirement already satisfied: idna<4,>=2.5 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from requests->folium) (3.3)
    Note: you may need to restart the kernel to use updated packages.



```python
pip install geopy
```

    Requirement already satisfied: geopy in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (2.4.1)
    Requirement already satisfied: geographiclib<3,>=1.52 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from geopy) (2.0)
    Note: you may need to restart the kernel to use updated packages.



```python
pip install pandas
```

    Requirement already satisfied: pandas in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (2.2.0)
    Requirement already satisfied: numpy<2,>=1.22.4 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from pandas) (1.26.4)
    Requirement already satisfied: tzdata>=2022.7 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from pandas) (2023.2)
    Requirement already satisfied: pytz>=2020.1 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from pandas) (2022.1)
    Requirement already satisfied: python-dateutil>=2.8.2 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from pandas) (2.8.2)
    Requirement already satisfied: six>=1.5 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from python-dateutil>=2.8.2->pandas) (1.16.0)
    Note: you may need to restart the kernel to use updated packages.



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
from selenium import webdriver
from selenium.webdriver.common.by import By
```


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

## Define Global Variables


```python
# URL of the website to scrape
url = 'https://fbschedules.com/2024-smu-football-schedule/'

# The csv file that we're saving all the games to, and reading from
file_name = "smuFootballSchedule2024.csv"
```

## Scrape the website


```python
# Initialize Safari WebDriver
driver = webdriver.Safari()
driver.implicitly_wait(1) #wait up to 5 secs for the page to load

from selenium.common.exceptions import NoSuchElementException, TimeoutException

# Open the URL in the browser
driver.get(url)

# define Game class
class Game:
    def __init__(self, game_card):
        self.game_card = game_card
        #self.raw_text = raw_text
        
# define games list of objects
games = []

# Find all div elements by xpath
game_cards = driver.find_elements(By.XPATH, '//*[@id="main"]/div[1]/div/div/div/div[6]/div/div[2]/table/tbody/tr')

# Save the html
for game_card in game_cards:
    # create the object for this game card
    game = Game(game_card)
    
    # Get the date
    try:
        #print(game_card.find_element(By.CLASS_NAME, 'cfb2').find_element(By.TAG_NAME, 'a').text)
        date = game.game_card.find_element(By.CLASS_NAME, 'cfb1').text
        date = ' '.join(date.split())
    except (NoSuchElementException, TimeoutException):
        date = ""
        
    game.date = date
    
    # Get the opponent
    try:
        opponent = game.game_card.find_element(By.CLASS_NAME, 'cfb2').find_element(By.TAG_NAME, 'a').text
    except (NoSuchElementException, TimeoutException):
        try:
            opponent = game.game_card.find_element(By.CLASS_NAME, 'cfb2').find_element(By.TAG_NAME, 'strong').text
        except (NoSuchElementException, TimeoutException):
            opponent = "START"
    
    if opponent.startswith(" "):
        opponent = opponent[4:]
    
    game.opponent = opponent
    
    # Get the location
    try:
        location = game.game_card.find_element(By.CLASS_NAME, 'cfb2').find_element(By.CLASS_NAME, 'stadium-txt-span').text
    except (NoSuchElementException, TimeoutException):
        try:
            location = game.game_card.find_element(By.CLASS_NAME, 'cfb2').text.replace(game.game_card.find_element(By.CLASS_NAME, 'cfb2').find_element(By.TAG_NAME, 'strong').text, '').strip()
        except (Exception, NoSuchElementException, TimeoutException):
            location = ""
    
    game.location = location
    
    # Get the ticketLink
    try:
        ticketLink = game.game_card.find_element(By.CLASS_NAME, 'cfb4').find_element(By.TAG_NAME, 'a').get_attribute('href')
    except (NoSuchElementException, TimeoutException):
        ticketLink = ""
    
    game.ticketLink = ticketLink
    
    # Set up latitude and longitude
    game.latitude = None
    game.longitude = None
    
    # Test Print
    print(game.location)
    
    # append to the list of objects
    games.append(game)

# Close the browser
driver.quit()
```

    
    Mackay Stadium, Reno, NV
    Gerald J. Ford Stadium, Dallas, TX
    Gerald J. Ford Stadium, Dallas, TX
    
    Gerald J. Ford Stadium, Dallas, TX
    Gerald J. Ford Stadium, Dallas, TX
    L&N Stadium, Louisville, KY
    
    Stanford Stadium, Stanford, CA
    Wallace Wade Stadium, Durham, NC
    Gerald J. Ford Stadium, Dallas, TX
    
    Gerald J. Ford Stadium, Dallas, TX
    Scott Stadium, Charlottesville, VA
    Gerald J. Ford Stadium, Dallas, TX
    Bank of America Stadium, Charlotte, NC


## Get Most Common Location for OFF Weeks


```python
# Get default location (one that occurs most often)
from collections import Counter

# Extract the locations from the list of game objects, excluding empty strings
locations = [game.location for game in games if game.location]

# Count the frequency of each location
location_counts = Counter(locations)

# Find the location with the maximum frequency
most_common_location = location_counts.most_common(1)[0]

print(f"The most common location is '{most_common_location[0]}' with {most_common_location[1]} occurrences.")
```

    The most common location is 'Gerald J. Ford Stadium, Dallas, TX' with 7 occurrences.


## Locate the Opponents on Map


```python
geolocator = Nominatim(user_agent="palmercjones@comcast.net")

for game in games:
    if (game.latitude == None) or (game.longitude == None):
        if (game.location == ""):
            game.location = most_common_location[0]
        coordinates_query = f"{game.location}"
        coordinates = geolocator.geocode(coordinates_query)
        if coordinates == None:
            coordinates_query = f"{game.location.split(', ', 1)[1] if ', ' in game.location else game.location}"
            coordinates = geolocator.geocode(coordinates_query)
        print(coordinates_query)
        #print(coordinates)
        newLatitude = coordinates.latitude
        newLongitude = coordinates.longitude
        while (any(game.latitude == newLatitude and game.longitude == newLongitude for game in games)):
            newLatitude = newLatitude - 0.01
            newLongitude = newLongitude + 0.01
        game.latitude = newLatitude
        game.longitude = newLongitude
    if ('Champion' in game.opponent): #championship
        game.color = "darkpurple"
        game.icon = "trophy"
    elif ('START' in game.opponent): #start
        game.color = "green"
        game.icon = "play"
    elif (game.opponent == "OFF"): #OFF
        game.color = "lightgray"
        game.icon = "moon"
    elif (game.location == most_common_location[0]): #home
        game.color = "darkred"
        game.icon = "home"
    else: #away
        game.color = "darkblue"
        game.icon = "plane"
    #print(game.color)
    print(f"Coordinates for {game.opponent}, Latitude = {game.latitude}, Longitude = {game.longitude}")
```

    Coordinates for START, Latitude = 32.838058700000005, Longitude = -96.78345776612298
    Coordinates for Nevada Wolf Pack , Latitude = 39.5468915, Longitude = -119.81739798009238
    Coordinates for HCU Huskies , Latitude = 32.82805870000001, Longitude = -96.77345776612297
    Coordinates for BYU Cougars , Latitude = 32.81805870000001, Longitude = -96.76345776612297
    Coordinates for OFF, Latitude = 32.80805870000001, Longitude = -96.75345776612296
    Coordinates for TCU Horned Frogs , Latitude = 32.79805870000001, Longitude = -96.74345776612296
    Coordinates for Florida State Seminoles , Latitude = 32.788058700000015, Longitude = -96.73345776612295
    Coordinates for Louisville Cardinals , Latitude = 38.206016149999996, Longitude = -85.75877338041424
    Coordinates for OFF, Latitude = 32.77805870000002, Longitude = -96.72345776612295
    Coordinates for Stanford Cardinal , Latitude = 37.43453005, Longitude = -122.16116296732366
    Coordinates for Duke Blue Devils , Latitude = 35.995445849999996, Longitude = -78.94188924076411
    Coordinates for Pitt Panthers , Latitude = 32.76805870000002, Longitude = -96.71345776612294
    Coordinates for OFF, Latitude = 32.75805870000002, Longitude = -96.70345776612294
    Coordinates for Boston College Eagles , Latitude = 32.74805870000002, Longitude = -96.69345776612293
    Coordinates for Virginia Cavaliers , Latitude = 38.029306, Longitude = -78.4766781
    Coordinates for California Golden Bears , Latitude = 32.738058700000025, Longitude = -96.68345776612293
    Coordinates for ACC Championship, Latitude = 35.22579505, Longitude = -80.85385877910787


## Open Existing File


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


## Make the Map


```python
# Get the directory where the Python script or notebook is located
current_dir = os.path.dirname(os.path.abspath('smuFootballMap2024.ipynb'))

# Set the current working directory to the directory of the Python script or notebook
os.chdir(current_dir)

# Create a map centered at the geographical center of the US
m = folium.Map(location=[39.8283, -98.5795], zoom_start=4)

# Add markers for each location
for game in games:
    folium.Marker(
        location=[game.latitude, game.longitude],
        tooltip=(game.opponent + '<br>' +game.location + '<br>' + game.date),
        icon=folium.Icon(color=game.color, prefix='fa',icon=game.icon)
    ).add_to(m)
    
travel_coordinates = [(game.latitude, game.longitude) for game in games if game.location]
#print(coordinates)

folium.PolyLine(travel_coordinates, tooltip="Travel").add_to(m)

# Specify the path to save the HTML file
html_file_path = os.path.join(current_dir, 'map_smuFootball2024.html')

# Save the map to an HTML file in the current directory
m.save(html_file_path)

print(f"Map saved to: {html_file_path}")

m
```

    Map saved to: /Users/palmerjones/Downloads/map_smuFootball2024.html





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
                #map_f25ee8ea0af22eb2ea72bd5e9e448f1a {
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


            &lt;div class=&quot;folium-map&quot; id=&quot;map_f25ee8ea0af22eb2ea72bd5e9e448f1a&quot; &gt;&lt;/div&gt;

&lt;/body&gt;
&lt;script&gt;


            var map_f25ee8ea0af22eb2ea72bd5e9e448f1a = L.map(
                &quot;map_f25ee8ea0af22eb2ea72bd5e9e448f1a&quot;,
                {
                    center: [39.8283, -98.5795],
                    crs: L.CRS.EPSG3857,
                    zoom: 4,
                    zoomControl: true,
                    preferCanvas: false,
                }
            );





            var tile_layer_53ac5931dfbd68168aa404568397b3bb = L.tileLayer(
                &quot;https://tile.openstreetmap.org/{z}/{x}/{y}.png&quot;,
                {&quot;attribution&quot;: &quot;\u0026copy; \u003ca href=\&quot;https://www.openstreetmap.org/copyright\&quot;\u003eOpenStreetMap\u003c/a\u003e contributors&quot;, &quot;detectRetina&quot;: false, &quot;maxNativeZoom&quot;: 19, &quot;maxZoom&quot;: 19, &quot;minZoom&quot;: 0, &quot;noWrap&quot;: false, &quot;opacity&quot;: 1, &quot;subdomains&quot;: &quot;abc&quot;, &quot;tms&quot;: false}
            );


            tile_layer_53ac5931dfbd68168aa404568397b3bb.addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var marker_5494f99f02f000e92981e16c794806d4 = L.marker(
                [32.838058700000005, -96.78345776612298],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_99e809d0006dbbed16a576e020719f25 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;play&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;green&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_5494f99f02f000e92981e16c794806d4.setIcon(icon_99e809d0006dbbed16a576e020719f25);


            marker_5494f99f02f000e92981e16c794806d4.bindTooltip(
                `&lt;div&gt;
                     START&lt;br&gt;Gerald J. Ford Stadium, Dallas, TX&lt;br&gt;
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_bf954952caa5cc4385f193be952e3f92 = L.marker(
                [39.5468915, -119.81739798009238],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_8ed81ffe20e16a4a3cc5b9606139b515 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;plane&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;darkblue&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_bf954952caa5cc4385f193be952e3f92.setIcon(icon_8ed81ffe20e16a4a3cc5b9606139b515);


            marker_bf954952caa5cc4385f193be952e3f92.bindTooltip(
                `&lt;div&gt;
                     Nevada Wolf Pack &lt;br&gt;Mackay Stadium, Reno, NV&lt;br&gt;Saturday Aug. 24
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_488567feac8efa686bfe6d9da3ed03d4 = L.marker(
                [32.82805870000001, -96.77345776612297],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_7474b354057519f4c3cc01764c731d5a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;home&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;darkred&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_488567feac8efa686bfe6d9da3ed03d4.setIcon(icon_7474b354057519f4c3cc01764c731d5a);


            marker_488567feac8efa686bfe6d9da3ed03d4.bindTooltip(
                `&lt;div&gt;
                     HCU Huskies &lt;br&gt;Gerald J. Ford Stadium, Dallas, TX&lt;br&gt;Saturday Aug. 31
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_92f4109979b565ea72c0598e5d0de06e = L.marker(
                [32.81805870000001, -96.76345776612297],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_a19569ab2bc63e2f317c400bc2f6f472 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;home&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;darkred&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_92f4109979b565ea72c0598e5d0de06e.setIcon(icon_a19569ab2bc63e2f317c400bc2f6f472);


            marker_92f4109979b565ea72c0598e5d0de06e.bindTooltip(
                `&lt;div&gt;
                     BYU Cougars &lt;br&gt;Gerald J. Ford Stadium, Dallas, TX&lt;br&gt;Friday Sep. 6
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_3fc8c14fdf0d27920c370f34ad37b8ad = L.marker(
                [32.80805870000001, -96.75345776612296],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_ac7b6b64c278db457a58cfe9ff8ba4b8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;moon&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;lightgray&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_3fc8c14fdf0d27920c370f34ad37b8ad.setIcon(icon_ac7b6b64c278db457a58cfe9ff8ba4b8);


            marker_3fc8c14fdf0d27920c370f34ad37b8ad.bindTooltip(
                `&lt;div&gt;
                     OFF&lt;br&gt;Gerald J. Ford Stadium, Dallas, TX&lt;br&gt;Saturday Sep. 14
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_76efa48ad24d54f6e9b87dd61c6f84c9 = L.marker(
                [32.79805870000001, -96.74345776612296],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_506814d7ecfc628eba255146037bedf3 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;home&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;darkred&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_76efa48ad24d54f6e9b87dd61c6f84c9.setIcon(icon_506814d7ecfc628eba255146037bedf3);


            marker_76efa48ad24d54f6e9b87dd61c6f84c9.bindTooltip(
                `&lt;div&gt;
                     TCU Horned Frogs &lt;br&gt;Gerald J. Ford Stadium, Dallas, TX&lt;br&gt;Saturday Sep. 21
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_a7e31aab41606cfbde165e888a7d495a = L.marker(
                [32.788058700000015, -96.73345776612295],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_3285ea6fdf53059c530acf4d6ac3404e = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;home&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;darkred&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_a7e31aab41606cfbde165e888a7d495a.setIcon(icon_3285ea6fdf53059c530acf4d6ac3404e);


            marker_a7e31aab41606cfbde165e888a7d495a.bindTooltip(
                `&lt;div&gt;
                     Florida State Seminoles &lt;br&gt;Gerald J. Ford Stadium, Dallas, TX&lt;br&gt;Saturday Sep. 28
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_512e3e643a602ff79447cd4c76e161d2 = L.marker(
                [38.206016149999996, -85.75877338041424],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_4e3f2857f95158523312f3106db137b6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;plane&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;darkblue&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_512e3e643a602ff79447cd4c76e161d2.setIcon(icon_4e3f2857f95158523312f3106db137b6);


            marker_512e3e643a602ff79447cd4c76e161d2.bindTooltip(
                `&lt;div&gt;
                     Louisville Cardinals &lt;br&gt;L&amp;N Stadium, Louisville, KY&lt;br&gt;Saturday Oct. 5
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_ab1eaa21637aa7a4e7368c60a23e5e62 = L.marker(
                [32.77805870000002, -96.72345776612295],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_6f541b8dc55b16821edf38b7519c1ee7 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;moon&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;lightgray&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_ab1eaa21637aa7a4e7368c60a23e5e62.setIcon(icon_6f541b8dc55b16821edf38b7519c1ee7);


            marker_ab1eaa21637aa7a4e7368c60a23e5e62.bindTooltip(
                `&lt;div&gt;
                     OFF&lt;br&gt;Gerald J. Ford Stadium, Dallas, TX&lt;br&gt;Saturday Oct. 12
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_195f0796b4e898834927fa3f1b734250 = L.marker(
                [37.43453005, -122.16116296732366],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_0bc8d6561cd13b5239f0710c1336812d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;plane&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;darkblue&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_195f0796b4e898834927fa3f1b734250.setIcon(icon_0bc8d6561cd13b5239f0710c1336812d);


            marker_195f0796b4e898834927fa3f1b734250.bindTooltip(
                `&lt;div&gt;
                     Stanford Cardinal &lt;br&gt;Stanford Stadium, Stanford, CA&lt;br&gt;Saturday Oct. 19
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_39791432f31a6d340fa53a747988b202 = L.marker(
                [35.995445849999996, -78.94188924076411],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_6bd3d44e44a7c726270a53cfcccc9605 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;plane&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;darkblue&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_39791432f31a6d340fa53a747988b202.setIcon(icon_6bd3d44e44a7c726270a53cfcccc9605);


            marker_39791432f31a6d340fa53a747988b202.bindTooltip(
                `&lt;div&gt;
                     Duke Blue Devils &lt;br&gt;Wallace Wade Stadium, Durham, NC&lt;br&gt;Saturday Oct. 26
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_15e2575cefd2f529c5ecca0b2c10e63e = L.marker(
                [32.76805870000002, -96.71345776612294],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_08d59fab7e9f12d46127b290e545a95d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;home&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;darkred&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_15e2575cefd2f529c5ecca0b2c10e63e.setIcon(icon_08d59fab7e9f12d46127b290e545a95d);


            marker_15e2575cefd2f529c5ecca0b2c10e63e.bindTooltip(
                `&lt;div&gt;
                     Pitt Panthers &lt;br&gt;Gerald J. Ford Stadium, Dallas, TX&lt;br&gt;Saturday Nov. 2
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_c38d5d5f8bbd3720e1d164859de58e82 = L.marker(
                [32.75805870000002, -96.70345776612294],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_cab0f206bedead6b3197a209459155ea = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;moon&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;lightgray&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_c38d5d5f8bbd3720e1d164859de58e82.setIcon(icon_cab0f206bedead6b3197a209459155ea);


            marker_c38d5d5f8bbd3720e1d164859de58e82.bindTooltip(
                `&lt;div&gt;
                     OFF&lt;br&gt;Gerald J. Ford Stadium, Dallas, TX&lt;br&gt;Saturday Nov. 9
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_01f500821b5c227b04ae0df32896feb9 = L.marker(
                [32.74805870000002, -96.69345776612293],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_daa1ba4874d5fe340b34bc5d7164fb36 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;home&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;darkred&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_01f500821b5c227b04ae0df32896feb9.setIcon(icon_daa1ba4874d5fe340b34bc5d7164fb36);


            marker_01f500821b5c227b04ae0df32896feb9.bindTooltip(
                `&lt;div&gt;
                     Boston College Eagles &lt;br&gt;Gerald J. Ford Stadium, Dallas, TX&lt;br&gt;Saturday Nov. 16
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_c898c372c2945d4d0d829d0dcde0ec5b = L.marker(
                [38.029306, -78.4766781],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_4be2db2c7af37846f51a659488361191 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;plane&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;darkblue&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_c898c372c2945d4d0d829d0dcde0ec5b.setIcon(icon_4be2db2c7af37846f51a659488361191);


            marker_c898c372c2945d4d0d829d0dcde0ec5b.bindTooltip(
                `&lt;div&gt;
                     Virginia Cavaliers &lt;br&gt;Scott Stadium, Charlottesville, VA&lt;br&gt;Saturday Nov. 23
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_76d5f86d32a3f7831dea017e8c32d3a0 = L.marker(
                [32.738058700000025, -96.68345776612293],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_43cc41f276f5c5cbb92ecf4621bd2a9d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;home&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;darkred&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_76d5f86d32a3f7831dea017e8c32d3a0.setIcon(icon_43cc41f276f5c5cbb92ecf4621bd2a9d);


            marker_76d5f86d32a3f7831dea017e8c32d3a0.bindTooltip(
                `&lt;div&gt;
                     California Golden Bears &lt;br&gt;Gerald J. Ford Stadium, Dallas, TX&lt;br&gt;Saturday Nov. 30
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var marker_ca7486404a08fdd71f551dc7c879e2cd = L.marker(
                [35.22579505, -80.85385877910787],
                {}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            var icon_96c103189a16e02585af139088a71b09 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;trophy&quot;, &quot;iconColor&quot;: &quot;white&quot;, &quot;markerColor&quot;: &quot;darkpurple&quot;, &quot;prefix&quot;: &quot;fa&quot;}
            );
            marker_ca7486404a08fdd71f551dc7c879e2cd.setIcon(icon_96c103189a16e02585af139088a71b09);


            marker_ca7486404a08fdd71f551dc7c879e2cd.bindTooltip(
                `&lt;div&gt;
                     ACC Championship&lt;br&gt;Bank of America Stadium, Charlotte, NC&lt;br&gt;Saturday Dec. 7
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            var poly_line_9bc599104be206a0535ef988594b9862 = L.polyline(
                [[32.838058700000005, -96.78345776612298], [39.5468915, -119.81739798009238], [32.82805870000001, -96.77345776612297], [32.81805870000001, -96.76345776612297], [32.80805870000001, -96.75345776612296], [32.79805870000001, -96.74345776612296], [32.788058700000015, -96.73345776612295], [38.206016149999996, -85.75877338041424], [32.77805870000002, -96.72345776612295], [37.43453005, -122.16116296732366], [35.995445849999996, -78.94188924076411], [32.76805870000002, -96.71345776612294], [32.75805870000002, -96.70345776612294], [32.74805870000002, -96.69345776612293], [38.029306, -78.4766781], [32.738058700000025, -96.68345776612293], [35.22579505, -80.85385877910787]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);


            poly_line_9bc599104be206a0535ef988594b9862.bindTooltip(
                `&lt;div&gt;
                     Travel
                 &lt;/div&gt;`,
                {&quot;sticky&quot;: true}
            );


            tile_layer_53ac5931dfbd68168aa404568397b3bb.addTo(map_f25ee8ea0af22eb2ea72bd5e9e448f1a);

&lt;/script&gt;
&lt;/html&gt;" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



## Save the CSV


```python
# Save locations to csv

# Open the file in write mode with newline='' to prevent extra blank lines
with open(file_name, 'w', newline='') as csv_file:
    # Create a CSV writer object
    csv_writer = csv.writer(csv_file)
    
    # Write the header row
    #csv_writer.writerow(["Name", "Age", "City"])
    
    # Write the data rows
    for game in games:
        csv_writer.writerow([game.opponent, game.date, game.location, game.ticketLink])

print(f"Data saved to {file_name}")
```

    Data saved to smuFootballSchedule2024.csv

