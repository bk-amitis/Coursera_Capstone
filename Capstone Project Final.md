# Capstone Project : Where is the best cafe in the Haeundae(South Korea)?

## Introduction

### I wanted to dig into the topics I was interested in rather than finding out what caused the phenomenon to happen, and I wondered which of the cafes in my area was the best rated among the topics I was interested in.

### I will use the Foursquare API to investigate this topic and will not deviate from what I have learned so far.

### so, let's begin

## 1. Install Packages for use


```python
import requests # library to handle requests
import pandas as pd # library for data analsysis
import numpy as np # library to handle data in a vectorized manner
import random # library for random number generation


!pip install geopy
from geopy.geocoders import Nominatim # module to convert an address into latitude and longitude values

# libraries for displaying images
from IPython.display import Image 
from IPython.core.display import HTML 
    
# tranforming json file into a pandas dataframe library
from pandas.io.json import json_normalize


! pip install folium==0.5.0
import folium # plotting library

print('Folium installed')
print('Libraries imported.')
```

    Requirement already satisfied: geopy in c:\programdata\anaconda3\lib\site-packages (2.1.0)
    Requirement already satisfied: geographiclib<2,>=1.49 in c:\programdata\anaconda3\lib\site-packages (from geopy) (1.50)
    Requirement already satisfied: folium==0.5.0 in c:\programdata\anaconda3\lib\site-packages (0.5.0)
    Requirement already satisfied: requests in c:\programdata\anaconda3\lib\site-packages (from folium==0.5.0) (2.22.0)
    Requirement already satisfied: branca in c:\programdata\anaconda3\lib\site-packages (from folium==0.5.0) (0.4.2)
    Requirement already satisfied: six in c:\programdata\anaconda3\lib\site-packages (from folium==0.5.0) (1.14.0)
    Requirement already satisfied: jinja2 in c:\programdata\anaconda3\lib\site-packages (from folium==0.5.0) (2.11.1)
    Requirement already satisfied: urllib3!=1.25.0,!=1.25.1,<1.26,>=1.21.1 in c:\programdata\anaconda3\lib\site-packages (from requests->folium==0.5.0) (1.25.8)
    Requirement already satisfied: chardet<3.1.0,>=3.0.2 in c:\programdata\anaconda3\lib\site-packages (from requests->folium==0.5.0) (3.0.4)
    Requirement already satisfied: certifi>=2017.4.17 in c:\programdata\anaconda3\lib\site-packages (from requests->folium==0.5.0) (2019.11.28)
    Requirement already satisfied: idna<2.9,>=2.5 in c:\programdata\anaconda3\lib\site-packages (from requests->folium==0.5.0) (2.8)
    Requirement already satisfied: MarkupSafe>=0.23 in c:\programdata\anaconda3\lib\site-packages (from jinja2->folium==0.5.0) (1.1.1)
    Folium installed
    Libraries imported.
    

## 2. My 4square information


```python
CLIENT_ID = 'X3HMAFSD210LBZULFUWZ2YG1TWQ0M2LVYCMP5CXURNSC3SOX' # your Foursquare ID
CLIENT_SECRET = 'SXOYXNP4TY5KF0VHYC0KJ1KLE1QQXDKLTCROFCVQ0OXWHINW' # your Foursquare Secret
ACCESS_TOKEN = 'W14AGTRXCHB0IX4EHJLGAYLVZOICRQQKNAFFT1ZRPOEWH3A5' # your FourSquare Access Token
VERSION = '20180604'
LIMIT = 30
print('Your credentails:')
print('CLIENT_ID: ' + CLIENT_ID)
print('CLIENT_SECRET:' + CLIENT_SECRET)
```

    Your credentails:
    CLIENT_ID: X3HMAFSD210LBZULFUWZ2YG1TWQ0M2LVYCMP5CXURNSC3SOX
    CLIENT_SECRET:SXOYXNP4TY5KF0VHYC0KJ1KLE1QQXDKLTCROFCVQ0OXWHINW
    

### 2-1. Set a starting point


```python
address = 'Haeundae'

geolocator = Nominatim(user_agent="foursquare_agent")
location = geolocator.geocode(address)
latitude = location.latitude
longitude = location.longitude
print(latitude, longitude)
```

    35.1637531 129.1587469
    


```python
search_query = 'Cafe'
radius = 1000
print(search_query + ' .... OK!')
```

    Cafe .... OK!
    


```python
url = 'https://api.foursquare.com/v2/venues/search?client_id={}&client_secret={}&ll={},{}&oauth_token={}&v={}&query={}&radius={}&limit={}'.format(CLIENT_ID, CLIENT_SECRET, latitude, longitude,ACCESS_TOKEN, VERSION, search_query, radius, LIMIT)
url
```




    'https://api.foursquare.com/v2/venues/search?client_id=X3HMAFSD210LBZULFUWZ2YG1TWQ0M2LVYCMP5CXURNSC3SOX&client_secret=SXOYXNP4TY5KF0VHYC0KJ1KLE1QQXDKLTCROFCVQ0OXWHINW&ll=35.1637531,129.1587469&oauth_token=W14AGTRXCHB0IX4EHJLGAYLVZOICRQQKNAFFT1ZRPOEWH3A5&v=20180604&query=Cafe&radius=1000&limit=30'




```python
results = requests.get(url).json()
```


```python
venues = results['response']['venues']

# tranform venues into a dataframe
dataframe = json_normalize(venues)
dataframe.head()
```

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:4: FutureWarning: pandas.io.json.json_normalize is deprecated, use pandas.json_normalize instead
      after removing the cwd from sys.path.
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>name</th>
      <th>categories</th>
      <th>referralId</th>
      <th>hasPerk</th>
      <th>location.address</th>
      <th>location.lat</th>
      <th>location.lng</th>
      <th>location.labeledLatLngs</th>
      <th>location.distance</th>
      <th>location.postalCode</th>
      <th>location.cc</th>
      <th>location.state</th>
      <th>location.country</th>
      <th>location.formattedAddress</th>
      <th>location.city</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>590713f31543c754e1b6fa90</td>
      <td>Shilla Stay Haeundae Cafe</td>
      <td>[{'id': '52e81612bcbc57f1066b79f4', 'name': 'B...</td>
      <td>v-1612985726</td>
      <td>False</td>
      <td>해운대로570번길 46</td>
      <td>35.159972</td>
      <td>129.158761</td>
      <td>[{'label': 'display', 'lat': 35.15997208166144...</td>
      <td>420</td>
      <td>48093</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[해운대로570번길 46, 해운대구, 부산광역시, 48093]</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>51bc08ea498e6b52a69e0176</td>
      <td>Double bean cafe</td>
      <td>[{'id': '4bf58dd8d48988d16d941735', 'name': 'C...</td>
      <td>v-1612985726</td>
      <td>False</td>
      <td>NaN</td>
      <td>35.164016</td>
      <td>129.159867</td>
      <td>[{'label': 'display', 'lat': 35.16401589737672...</td>
      <td>106</td>
      <td>NaN</td>
      <td>KR</td>
      <td>NaN</td>
      <td>대한민국</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5d9acca4d53c6400088d562e</td>
      <td>cafe hito</td>
      <td>[{'id': '4bf58dd8d48988d16d941735', 'name': 'C...</td>
      <td>v-1612985726</td>
      <td>False</td>
      <td>해운대구 우동1로 21</td>
      <td>35.163754</td>
      <td>129.156430</td>
      <td>[{'label': 'display', 'lat': 35.163754, 'lng':...</td>
      <td>210</td>
      <td>48088</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[해운대구 우동1로 21, Haeundae - gu, 부산광역시, 48088]</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4d96f535b188721e563b0337</td>
      <td>Cafe Candy</td>
      <td>[]</td>
      <td>v-1612985726</td>
      <td>False</td>
      <td>NaN</td>
      <td>35.163915</td>
      <td>129.161482</td>
      <td>[{'label': 'display', 'lat': 35.163915, 'lng':...</td>
      <td>249</td>
      <td>NaN</td>
      <td>KR</td>
      <td>NaN</td>
      <td>대한민국</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5b8cb271cabcff002c532297</td>
      <td>Cafe Hop Chou (카페홉슈크림)</td>
      <td>[{'id': '4bf58dd8d48988d16d941735', 'name': 'C...</td>
      <td>v-1612985726</td>
      <td>False</td>
      <td>해운대구 해운대로570번길 46</td>
      <td>35.159893</td>
      <td>129.158360</td>
      <td>[{'label': 'display', 'lat': 35.159893, 'lng':...</td>
      <td>431</td>
      <td>48093</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[해운대구 해운대로570번길 46, 부산광역시, 부산광역시, 48093]</td>
      <td>부산광역시</td>
    </tr>
  </tbody>
</table>
</div>




```python
filtered_columns = ['name', 'categories'] + [col for col in dataframe.columns if col.startswith('location.')] + ['id']
dataframe_filtered = dataframe.loc[:, filtered_columns]

# function that extracts the category of the venue
# 장소의 범주를 추출하는 기능
def get_category_type(row):
    try:
        categories_list = row['categories']
    except:
        categories_list = row['venue.categories']
        
    if len(categories_list) == 0:
        return None
    else:
        return categories_list[0]['name']

# filter the category for each row
#각 행의 범주를 필터링하다
dataframe_filtered['categories'] = dataframe_filtered.apply(get_category_type, axis=1)

# clean column names by keeping only last term
# 마지막 기간만 유지하여 열 이름 정리
dataframe_filtered.columns = [column.split('.')[-1] for column in dataframe_filtered.columns]

dataframe_filtered
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>categories</th>
      <th>address</th>
      <th>lat</th>
      <th>lng</th>
      <th>labeledLatLngs</th>
      <th>distance</th>
      <th>postalCode</th>
      <th>cc</th>
      <th>state</th>
      <th>country</th>
      <th>formattedAddress</th>
      <th>city</th>
      <th>id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Shilla Stay Haeundae Cafe</td>
      <td>Buffet</td>
      <td>해운대로570번길 46</td>
      <td>35.159972</td>
      <td>129.158761</td>
      <td>[{'label': 'display', 'lat': 35.15997208166144...</td>
      <td>420</td>
      <td>48093</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[해운대로570번길 46, 해운대구, 부산광역시, 48093]</td>
      <td>NaN</td>
      <td>590713f31543c754e1b6fa90</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Double bean cafe</td>
      <td>Café</td>
      <td>NaN</td>
      <td>35.164016</td>
      <td>129.159867</td>
      <td>[{'label': 'display', 'lat': 35.16401589737672...</td>
      <td>106</td>
      <td>NaN</td>
      <td>KR</td>
      <td>NaN</td>
      <td>대한민국</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>51bc08ea498e6b52a69e0176</td>
    </tr>
    <tr>
      <th>2</th>
      <td>cafe hito</td>
      <td>Café</td>
      <td>해운대구 우동1로 21</td>
      <td>35.163754</td>
      <td>129.156430</td>
      <td>[{'label': 'display', 'lat': 35.163754, 'lng':...</td>
      <td>210</td>
      <td>48088</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[해운대구 우동1로 21, Haeundae - gu, 부산광역시, 48088]</td>
      <td>NaN</td>
      <td>5d9acca4d53c6400088d562e</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Cafe Candy</td>
      <td>None</td>
      <td>NaN</td>
      <td>35.163915</td>
      <td>129.161482</td>
      <td>[{'label': 'display', 'lat': 35.163915, 'lng':...</td>
      <td>249</td>
      <td>NaN</td>
      <td>KR</td>
      <td>NaN</td>
      <td>대한민국</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4d96f535b188721e563b0337</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Cafe Hop Chou (카페홉슈크림)</td>
      <td>Café</td>
      <td>해운대구 해운대로570번길 46</td>
      <td>35.159893</td>
      <td>129.158360</td>
      <td>[{'label': 'display', 'lat': 35.159893, 'lng':...</td>
      <td>431</td>
      <td>48093</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[해운대구 해운대로570번길 46, 부산광역시, 부산광역시, 48093]</td>
      <td>부산광역시</td>
      <td>5b8cb271cabcff002c532297</td>
    </tr>
    <tr>
      <th>5</th>
      <td>j.sun cafe</td>
      <td>Café</td>
      <td>NaN</td>
      <td>35.163774</td>
      <td>129.156740</td>
      <td>[{'label': 'display', 'lat': 35.163774, 'lng':...</td>
      <td>182</td>
      <td>NaN</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[해운대구, 부산광역시]</td>
      <td>NaN</td>
      <td>576f352b498e0917af22ae6f</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Café Creamã</td>
      <td>Ice Cream Shop</td>
      <td>해운대구 구남로 42</td>
      <td>35.160667</td>
      <td>129.161396</td>
      <td>[{'label': 'display', 'lat': 35.16066655561862...</td>
      <td>419</td>
      <td>612-847</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[해운대구 구남로 42, 부산광역시, 부산광역시, 612-847]</td>
      <td>부산광역시</td>
      <td>53801ce5498eefac455f3a97</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Cafe de Flore 302</td>
      <td>Breakfast Spot</td>
      <td>NaN</td>
      <td>35.159909</td>
      <td>129.161299</td>
      <td>[{'label': 'display', 'lat': 35.159909, 'lng':...</td>
      <td>486</td>
      <td>NaN</td>
      <td>KR</td>
      <td>NaN</td>
      <td>대한민국</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>55e12be8498eb6646fef627c</td>
    </tr>
    <tr>
      <th>8</th>
      <td>CAFE MINI</td>
      <td>Italian Restaurant</td>
      <td>해운대해변로 265번길 5</td>
      <td>35.159984</td>
      <td>129.160289</td>
      <td>[{'label': 'display', 'lat': 35.15998439149415...</td>
      <td>442</td>
      <td>NaN</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[해운대해변로 265번길 5, 해운대구, 부산광역시]</td>
      <td>부산광역시</td>
      <td>55e58118498e10ec27b3c938</td>
    </tr>
    <tr>
      <th>9</th>
      <td>cafe at home</td>
      <td>Café</td>
      <td>NaN</td>
      <td>35.165982</td>
      <td>129.163865</td>
      <td>[{'label': 'display', 'lat': 35.16598223274198...</td>
      <td>527</td>
      <td>NaN</td>
      <td>KR</td>
      <td>NaN</td>
      <td>대한민국</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4fc30ec2e4b0c9fe065f87ec</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Lake Cafe</td>
      <td>Café</td>
      <td>NaN</td>
      <td>35.162306</td>
      <td>129.156030</td>
      <td>[{'label': 'display', 'lat': 35.162306, 'lng':...</td>
      <td>295</td>
      <td>NaN</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[해운대구, 부산광역시]</td>
      <td>NaN</td>
      <td>5aa3890cb1538e4b65b9839d</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Canvas Cafe</td>
      <td>Café</td>
      <td>NaN</td>
      <td>35.159264</td>
      <td>129.152730</td>
      <td>[{'label': 'display', 'lat': 35.159264, 'lng':...</td>
      <td>741</td>
      <td>612-021</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[부산광역시, 부산광역시, 612-021]</td>
      <td>부산광역시</td>
      <td>5a6c497c6661160f93ac1a5f</td>
    </tr>
    <tr>
      <th>12</th>
      <td>cafe luca</td>
      <td>Café</td>
      <td>해운대구</td>
      <td>35.163946</td>
      <td>129.164301</td>
      <td>[{'label': 'display', 'lat': 35.163946, 'lng':...</td>
      <td>505</td>
      <td>NaN</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[해운대구, 부산광역시, 부산광역시]</td>
      <td>부산광역시</td>
      <td>4cb533e756fca1cd38884d18</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Cafe Munday</td>
      <td>Café</td>
      <td>NaN</td>
      <td>35.162187</td>
      <td>129.165007</td>
      <td>[{'label': 'display', 'lat': 35.162187, 'lng':...</td>
      <td>595</td>
      <td>NaN</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[Haeundae-gu, 부산광역시]</td>
      <td>부산광역시</td>
      <td>5c57c5597dc9e1002cfaf1e1</td>
    </tr>
    <tr>
      <th>14</th>
      <td>까페 미리내 (Mirinae Cafe)</td>
      <td>Café</td>
      <td>NaN</td>
      <td>35.161268</td>
      <td>129.160603</td>
      <td>[{'label': 'display', 'lat': 35.161268, 'lng':...</td>
      <td>324</td>
      <td>NaN</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[부산광역시, 부산광역시]</td>
      <td>부산광역시</td>
      <td>4e5f2d9cc65bba7166a2ba71</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Gani Cafe</td>
      <td>Café</td>
      <td>NaN</td>
      <td>35.160766</td>
      <td>129.161564</td>
      <td>[{'label': 'display', 'lat': 35.16076624350494...</td>
      <td>419</td>
      <td>NaN</td>
      <td>KR</td>
      <td>NaN</td>
      <td>대한민국</td>
      <td>[해운대 중동 1392-100]</td>
      <td>해운대 중동 1392-100</td>
      <td>50d55ed9e4b088aa250a30da</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Cafe Bene Hae-woon-dae</td>
      <td>None</td>
      <td>대한민국 부산광역시 해운대구 중1동 1124-14</td>
      <td>35.159892</td>
      <td>129.165490</td>
      <td>[{'label': 'display', 'lat': 35.15989232218668...</td>
      <td>749</td>
      <td>612-847</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[대한민국 부산광역시 해운대구 중1동 1124-14, 부산광역시, 부산광역시, 61...</td>
      <td>부산광역시</td>
      <td>4dc3a29152b1e8f9f7a6b2ee</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Cafe Icebean -Marina</td>
      <td>Café</td>
      <td>NaN</td>
      <td>35.159267</td>
      <td>129.151672</td>
      <td>[{'label': 'display', 'lat': 35.15926742553711...</td>
      <td>814</td>
      <td>NaN</td>
      <td>KR</td>
      <td>NaN</td>
      <td>대한민국</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4eabd4b99a522a65af501a02</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Kid's Cafe Wa</td>
      <td>Playground</td>
      <td>해운대구 중동 1417-2 7층</td>
      <td>35.159770</td>
      <td>129.161350</td>
      <td>[{'label': 'display', 'lat': 35.15977, 'lng': ...</td>
      <td>502</td>
      <td>NaN</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[해운대구 중동 1417-2 7층, 부산광역시, 부산광역시]</td>
      <td>부산광역시</td>
      <td>4d539ea1f9f9b60ca87025e6</td>
    </tr>
    <tr>
      <th>19</th>
      <td>cafe lee man's</td>
      <td>American Restaurant</td>
      <td>NaN</td>
      <td>35.157723</td>
      <td>129.150720</td>
      <td>[{'label': 'display', 'lat': 35.157723, 'lng':...</td>
      <td>992</td>
      <td>NaN</td>
      <td>KR</td>
      <td>NaN</td>
      <td>대한민국</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>554186f2498ea66a9375e159</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Dongbaek Cafe</td>
      <td>Café</td>
      <td>NaN</td>
      <td>35.160625</td>
      <td>129.165029</td>
      <td>[{'label': 'display', 'lat': 35.160625, 'lng':...</td>
      <td>669</td>
      <td>NaN</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[Haeundae-gu, 부산광역시]</td>
      <td>부산광역시</td>
      <td>5bc2f2581543c7002cd85fd9</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Cafe On</td>
      <td>Coffee Shop</td>
      <td>NaN</td>
      <td>35.160837</td>
      <td>129.144403</td>
      <td>[{'label': 'display', 'lat': 35.160837, 'lng':...</td>
      <td>1345</td>
      <td>NaN</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[부산광역시, 부산광역시]</td>
      <td>부산광역시</td>
      <td>4def9ae252b1d9bad3c2f187</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Homemade Brunch Cafe 37.5</td>
      <td>Café</td>
      <td>NaN</td>
      <td>35.157936</td>
      <td>129.150475</td>
      <td>[{'label': 'display', 'lat': 35.157936, 'lng':...</td>
      <td>992</td>
      <td>48091</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[해운대구, 부산광역시, 48091]</td>
      <td>NaN</td>
      <td>5ccf959a4a7aae002c1b871b</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Kids Cafe WA (키즈카페 와)</td>
      <td>General Entertainment</td>
      <td>해운대구 중동 1417-2 SEASTAR 빌딩 7층</td>
      <td>35.159919</td>
      <td>129.168761</td>
      <td>[{'label': 'display', 'lat': 35.159919, 'lng':...</td>
      <td>1006</td>
      <td>NaN</td>
      <td>KR</td>
      <td>Korea</td>
      <td>대한민국</td>
      <td>[해운대구 중동 1417-2 SEASTAR 빌딩 7층, Busan, Korea]</td>
      <td>Busan</td>
      <td>4bd2af3041b9ef3b0797fee5</td>
    </tr>
    <tr>
      <th>24</th>
      <td>The Grand Cafe</td>
      <td>Café</td>
      <td>NaN</td>
      <td>35.157389</td>
      <td>129.151197</td>
      <td>[{'label': 'display', 'lat': 35.157389, 'lng':...</td>
      <td>986</td>
      <td>NaN</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[Haeundae-gu, 부산광역시]</td>
      <td>NaN</td>
      <td>5b0a47bb8c812a002cb221d3</td>
    </tr>
    <tr>
      <th>25</th>
      <td>더까페 (the cafe)</td>
      <td>Café</td>
      <td>부산시 해운대구 마린시티3로 37</td>
      <td>35.158166</td>
      <td>129.148959</td>
      <td>[{'label': 'display', 'lat': 35.15816588127223...</td>
      <td>1086</td>
      <td>NaN</td>
      <td>KR</td>
      <td>NaN</td>
      <td>대한민국</td>
      <td>[부산시 해운대구 마린시티3로 37]</td>
      <td>NaN</td>
      <td>50397b47e4b0014516307a29</td>
    </tr>
    <tr>
      <th>26</th>
      <td>cafe6ixmiles</td>
      <td>Café</td>
      <td>NaN</td>
      <td>35.158073</td>
      <td>129.151733</td>
      <td>[{'label': 'display', 'lat': 35.15807342529297...</td>
      <td>898</td>
      <td>NaN</td>
      <td>KR</td>
      <td>NaN</td>
      <td>대한민국</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5286d13e498efc78f9c003d3</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Opus Bread.cafe</td>
      <td>Bakery</td>
      <td>NaN</td>
      <td>35.158643</td>
      <td>129.151261</td>
      <td>[{'label': 'display', 'lat': 35.15864253044026...</td>
      <td>887</td>
      <td>NaN</td>
      <td>KR</td>
      <td>NaN</td>
      <td>대한민국</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4e433fc9fa76eeb2dab5d17a</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Quick's_Cafe</td>
      <td>Coffee Shop</td>
      <td>NaN</td>
      <td>35.160586</td>
      <td>129.144689</td>
      <td>[{'label': 'display', 'lat': 35.160586, 'lng':...</td>
      <td>1327</td>
      <td>NaN</td>
      <td>KR</td>
      <td>부산광역시</td>
      <td>대한민국</td>
      <td>[부산광역시, 부산광역시]</td>
      <td>부산광역시</td>
      <td>4df0e0257d8ba370a0112071</td>
    </tr>
  </tbody>
</table>
</div>



### 2-2. Find Map


```python
venues_map = folium.Map(location=[latitude, longitude], zoom_start=13) # generate map centred around the Conrad Hotel

# add a red circle marker to represent the Conrad Hotel
folium.CircleMarker(
    [latitude, longitude],
    radius=10,
    color='red',
    popup='Haeundae',
    fill = True,
    fill_color = 'red',
    fill_opacity = 0.6
).add_to(venues_map)

for lat, lng, label in zip(dataframe_filtered.lat, dataframe_filtered.lng, dataframe_filtered.categories):
    folium.CircleMarker(
        [lat, lng],
        radius=5,
        color='blue',
        popup=label,
        fill = True,
        fill_color='blue',
        fill_opacity=0.6
    ).add_to(venues_map)

# display map
venues_map
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><span style="color:#565656">Make this Notebook Trusted to load map: File -> Trust Notebook</span><iframe src="about:blank" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" data-html=%3C%21DOCTYPE%20html%3E%0A%3Chead%3E%20%20%20%20%0A%20%20%20%20%3Cmeta%20http-equiv%3D%22content-type%22%20content%3D%22text/html%3B%20charset%3DUTF-8%22%20/%3E%0A%20%20%20%20%3Cscript%3EL_PREFER_CANVAS%20%3D%20false%3B%20L_NO_TOUCH%20%3D%20false%3B%20L_DISABLE_3D%20%3D%20false%3B%3C/script%3E%0A%20%20%20%20%3Cscript%20src%3D%22https%3A//cdn.jsdelivr.net/npm/leaflet%401.2.0/dist/leaflet.js%22%3E%3C/script%3E%0A%20%20%20%20%3Cscript%20src%3D%22https%3A//ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js%22%3E%3C/script%3E%0A%20%20%20%20%3Cscript%20src%3D%22https%3A//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/js/bootstrap.min.js%22%3E%3C/script%3E%0A%20%20%20%20%3Cscript%20src%3D%22https%3A//cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.js%22%3E%3C/script%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//cdn.jsdelivr.net/npm/leaflet%401.2.0/dist/leaflet.css%22/%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css%22/%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap-theme.min.css%22/%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//maxcdn.bootstrapcdn.com/font-awesome/4.6.3/css/font-awesome.min.css%22/%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.css%22/%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//rawgit.com/python-visualization/folium/master/folium/templates/leaflet.awesome.rotate.css%22/%3E%0A%20%20%20%20%3Cstyle%3Ehtml%2C%20body%20%7Bwidth%3A%20100%25%3Bheight%3A%20100%25%3Bmargin%3A%200%3Bpadding%3A%200%3B%7D%3C/style%3E%0A%20%20%20%20%3Cstyle%3E%23map%20%7Bposition%3Aabsolute%3Btop%3A0%3Bbottom%3A0%3Bright%3A0%3Bleft%3A0%3B%7D%3C/style%3E%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%3Cstyle%3E%20%23map_71f955d25ee249969203b0a7dfa2dd09%20%7B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20position%20%3A%20relative%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20width%20%3A%20100.0%25%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20height%3A%20100.0%25%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20left%3A%200.0%25%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20top%3A%200.0%25%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%3C/style%3E%0A%20%20%20%20%20%20%20%20%0A%3C/head%3E%0A%3Cbody%3E%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%3Cdiv%20class%3D%22folium-map%22%20id%3D%22map_71f955d25ee249969203b0a7dfa2dd09%22%20%3E%3C/div%3E%0A%20%20%20%20%20%20%20%20%0A%3C/body%3E%0A%3Cscript%3E%20%20%20%20%0A%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20bounds%20%3D%20null%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20map_71f955d25ee249969203b0a7dfa2dd09%20%3D%20L.map%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%27map_71f955d25ee249969203b0a7dfa2dd09%27%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7Bcenter%3A%20%5B35.1637531%2C129.1587469%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20zoom%3A%2013%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20maxBounds%3A%20bounds%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20layers%3A%20%5B%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20worldCopyJump%3A%20false%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20crs%3A%20L.CRS.EPSG3857%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7D%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20tile_layer_2aad11b723154eb68023136230f77d19%20%3D%20L.tileLayer%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%27https%3A//%7Bs%7D.tile.openstreetmap.org/%7Bz%7D/%7Bx%7D/%7By%7D.png%27%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22attribution%22%3A%20null%2C%0A%20%20%22detectRetina%22%3A%20false%2C%0A%20%20%22maxZoom%22%3A%2018%2C%0A%20%20%22minZoom%22%3A%201%2C%0A%20%20%22noWrap%22%3A%20false%2C%0A%20%20%22subdomains%22%3A%20%22abc%22%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_bd12056894c245838dde27cc68fc388d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.1637531%2C129.1587469%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22red%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22red%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%2010%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_fbdce6d76e404700a3ae39cefca354e2%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_9fd105ba478040fbb693cb716952e7bc%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_9fd105ba478040fbb693cb716952e7bc%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EHaeundae%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_fbdce6d76e404700a3ae39cefca354e2.setContent%28html_9fd105ba478040fbb693cb716952e7bc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_bd12056894c245838dde27cc68fc388d.bindPopup%28popup_fbdce6d76e404700a3ae39cefca354e2%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_3ce74ad4a22444e4a9a8c7f7fe9332c6%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.15997208166144%2C129.1587610644076%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_ab0885316fb848d8a72a4988a0379c73%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_34803dbe8482427aa203d948dd1cbf36%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_34803dbe8482427aa203d948dd1cbf36%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EBuffet%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_ab0885316fb848d8a72a4988a0379c73.setContent%28html_34803dbe8482427aa203d948dd1cbf36%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_3ce74ad4a22444e4a9a8c7f7fe9332c6.bindPopup%28popup_ab0885316fb848d8a72a4988a0379c73%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_92219b7cc6a447d6ad3358c330493ca6%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.16401589737672%2C129.15986701653176%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_8d8626e2b19f4949bec9d985a4e9983d%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_642b4f5a918e40238ab082af1a8ff5d1%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_642b4f5a918e40238ab082af1a8ff5d1%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_8d8626e2b19f4949bec9d985a4e9983d.setContent%28html_642b4f5a918e40238ab082af1a8ff5d1%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_92219b7cc6a447d6ad3358c330493ca6.bindPopup%28popup_8d8626e2b19f4949bec9d985a4e9983d%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_4fcdf909a0e44223b1366fdffcf3c442%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.163754%2C129.15643%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_fd2d1e6a1d2843d5a498dca38b5b0ba4%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_5adba6595d854ac387706512acecbf52%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_5adba6595d854ac387706512acecbf52%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_fd2d1e6a1d2843d5a498dca38b5b0ba4.setContent%28html_5adba6595d854ac387706512acecbf52%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_4fcdf909a0e44223b1366fdffcf3c442.bindPopup%28popup_fd2d1e6a1d2843d5a498dca38b5b0ba4%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_cec88c4e185048f0a4c4634c3fdb41ca%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.163915%2C129.161482%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_5495b056d80c41808abcaf1496713a6a%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.159893%2C129.15836%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_3db606d505fb450291802663f3e625ac%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_5afc59fc9a3a44cdb9494a417876dc62%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_5afc59fc9a3a44cdb9494a417876dc62%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_3db606d505fb450291802663f3e625ac.setContent%28html_5afc59fc9a3a44cdb9494a417876dc62%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_5495b056d80c41808abcaf1496713a6a.bindPopup%28popup_3db606d505fb450291802663f3e625ac%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_fd1163b29b2b4dfcb11729d0d5347b26%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.163774%2C129.15674%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_f48fd15c2ad045639dd969723898dea3%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_46ac805a61a34e46b3cf1b99943c6740%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_46ac805a61a34e46b3cf1b99943c6740%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_f48fd15c2ad045639dd969723898dea3.setContent%28html_46ac805a61a34e46b3cf1b99943c6740%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_fd1163b29b2b4dfcb11729d0d5347b26.bindPopup%28popup_f48fd15c2ad045639dd969723898dea3%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_f14f9fbfa68a4ab88f2bc423c2fb3a06%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.16066655561862%2C129.161396073311%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_16337a707ad24fa581ff91def4261091%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_841b7c915d1b41ed8bfd5373d3a239ea%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_841b7c915d1b41ed8bfd5373d3a239ea%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EIce%20Cream%20Shop%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_16337a707ad24fa581ff91def4261091.setContent%28html_841b7c915d1b41ed8bfd5373d3a239ea%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_f14f9fbfa68a4ab88f2bc423c2fb3a06.bindPopup%28popup_16337a707ad24fa581ff91def4261091%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_3736782140e34e71aac705e6d5a07936%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.159909%2C129.161299%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_9e6338d551f3405bb0050a1700142180%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_ed3ed76231434d968763e7681fee2683%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_ed3ed76231434d968763e7681fee2683%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EBreakfast%20Spot%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_9e6338d551f3405bb0050a1700142180.setContent%28html_ed3ed76231434d968763e7681fee2683%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_3736782140e34e71aac705e6d5a07936.bindPopup%28popup_9e6338d551f3405bb0050a1700142180%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_2b214f5ac04b485e85c04e906fd68ca6%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.15998439149415%2C129.1602886538134%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_09c2b5af1a1a498fa65c4eb75f206c8c%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_00d560b4101c47e9bad0894c12ca68c9%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_00d560b4101c47e9bad0894c12ca68c9%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EItalian%20Restaurant%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_09c2b5af1a1a498fa65c4eb75f206c8c.setContent%28html_00d560b4101c47e9bad0894c12ca68c9%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_2b214f5ac04b485e85c04e906fd68ca6.bindPopup%28popup_09c2b5af1a1a498fa65c4eb75f206c8c%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_cb1862f9a5cb439eac405d8f4ef22815%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.16598223274198%2C129.16386483792596%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_1afea8a984de4669bf0ab18b51c582cf%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_f78635d7d14540fb9b9d20c80564101b%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_f78635d7d14540fb9b9d20c80564101b%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_1afea8a984de4669bf0ab18b51c582cf.setContent%28html_f78635d7d14540fb9b9d20c80564101b%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_cb1862f9a5cb439eac405d8f4ef22815.bindPopup%28popup_1afea8a984de4669bf0ab18b51c582cf%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_004da422d78441268084c2343688e51b%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.162306%2C129.15603%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_a63f740ba0be47b8a75c5e3cefa5da0d%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_d3e65b12ad4440c6934fafc81fe41bf6%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_d3e65b12ad4440c6934fafc81fe41bf6%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_a63f740ba0be47b8a75c5e3cefa5da0d.setContent%28html_d3e65b12ad4440c6934fafc81fe41bf6%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_004da422d78441268084c2343688e51b.bindPopup%28popup_a63f740ba0be47b8a75c5e3cefa5da0d%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_941bf010a664449da1c5ed7053bf462a%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.159264%2C129.15273%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_c1d17a635dc8417eb56967afe458d5b5%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_44c503b8e2bb479899039359dcbd20b6%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_44c503b8e2bb479899039359dcbd20b6%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_c1d17a635dc8417eb56967afe458d5b5.setContent%28html_44c503b8e2bb479899039359dcbd20b6%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_941bf010a664449da1c5ed7053bf462a.bindPopup%28popup_c1d17a635dc8417eb56967afe458d5b5%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_bfc6cbb78c4a429b9ba244cc41d97ddd%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.163946%2C129.164301%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_fa82d0e3753b428b9c9af12c86e180b6%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_c8f861a8f1e943898b9228604650bf46%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_c8f861a8f1e943898b9228604650bf46%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_fa82d0e3753b428b9c9af12c86e180b6.setContent%28html_c8f861a8f1e943898b9228604650bf46%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_bfc6cbb78c4a429b9ba244cc41d97ddd.bindPopup%28popup_fa82d0e3753b428b9c9af12c86e180b6%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_538fa0e0fea845eebc7d6857a867c0cf%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.162187%2C129.165007%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_0626465cc33145299f60ed6acf932cf8%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_3c0c2dfbd38341bfbbac6f3288ffa144%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_3c0c2dfbd38341bfbbac6f3288ffa144%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_0626465cc33145299f60ed6acf932cf8.setContent%28html_3c0c2dfbd38341bfbbac6f3288ffa144%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_538fa0e0fea845eebc7d6857a867c0cf.bindPopup%28popup_0626465cc33145299f60ed6acf932cf8%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_1ec1b14887ae4c0eb3d1155c2cd41981%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.161268%2C129.160603%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_b6e8655ae7c340c0a16d78e919ae0932%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_42a9d6f97f6d49d9bffe76bb51327864%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_42a9d6f97f6d49d9bffe76bb51327864%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_b6e8655ae7c340c0a16d78e919ae0932.setContent%28html_42a9d6f97f6d49d9bffe76bb51327864%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_1ec1b14887ae4c0eb3d1155c2cd41981.bindPopup%28popup_b6e8655ae7c340c0a16d78e919ae0932%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_94417181c0cb486b96a45b5d131f6483%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.160766243504945%2C129.16156370852062%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_82b44481be7b46978d104891a38d1a14%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_a5b2fa3090b743d18ef2116ee094ba77%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_a5b2fa3090b743d18ef2116ee094ba77%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_82b44481be7b46978d104891a38d1a14.setContent%28html_a5b2fa3090b743d18ef2116ee094ba77%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_94417181c0cb486b96a45b5d131f6483.bindPopup%28popup_82b44481be7b46978d104891a38d1a14%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_716c624033a340e790c4831189bad993%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.159892322186685%2C129.16549030942394%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_cfb12153fbe44036815b55ce1f4c5565%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.15926742553711%2C129.15167236328125%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_f30da29b688d4420a2a45508155dedd1%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_a42a2d1ea12c4bcc900d7e8458f1b37f%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_a42a2d1ea12c4bcc900d7e8458f1b37f%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_f30da29b688d4420a2a45508155dedd1.setContent%28html_a42a2d1ea12c4bcc900d7e8458f1b37f%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_cfb12153fbe44036815b55ce1f4c5565.bindPopup%28popup_f30da29b688d4420a2a45508155dedd1%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_271e7042fd71492696b9ed5dbf18164c%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.15977%2C129.16135%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_63716a7bb3354c408eaac56b4644384b%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_a41a40e6a14c44b5ac9abcee7c204352%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_a41a40e6a14c44b5ac9abcee7c204352%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EPlayground%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_63716a7bb3354c408eaac56b4644384b.setContent%28html_a41a40e6a14c44b5ac9abcee7c204352%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_271e7042fd71492696b9ed5dbf18164c.bindPopup%28popup_63716a7bb3354c408eaac56b4644384b%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_f08ef134cc38425382062ba3dd5aa09b%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.157723%2C129.15072%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_acc9cafbe7424e65a72491b82320f101%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_6a9073c6c12944f890acbe61ba581ab2%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_6a9073c6c12944f890acbe61ba581ab2%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EAmerican%20Restaurant%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_acc9cafbe7424e65a72491b82320f101.setContent%28html_6a9073c6c12944f890acbe61ba581ab2%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_f08ef134cc38425382062ba3dd5aa09b.bindPopup%28popup_acc9cafbe7424e65a72491b82320f101%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_a85153cc445949a08e59bc185d09a1c4%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.160625%2C129.165029%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_b618d66b98f241bab16493557d15c46c%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_a000ee2267fd405c87818c7e8ff35296%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_a000ee2267fd405c87818c7e8ff35296%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_b618d66b98f241bab16493557d15c46c.setContent%28html_a000ee2267fd405c87818c7e8ff35296%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_a85153cc445949a08e59bc185d09a1c4.bindPopup%28popup_b618d66b98f241bab16493557d15c46c%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_8e16b2dbde514855bd3e3d4ae3770c3d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.160837%2C129.144403%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_2070b6e0c4e141db8f2aa86da2c8542b%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_2910a6104936449fab5dbe50eed49b7b%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_2910a6104936449fab5dbe50eed49b7b%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECoffee%20Shop%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_2070b6e0c4e141db8f2aa86da2c8542b.setContent%28html_2910a6104936449fab5dbe50eed49b7b%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_8e16b2dbde514855bd3e3d4ae3770c3d.bindPopup%28popup_2070b6e0c4e141db8f2aa86da2c8542b%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_86c7867d51514910a4762a520a241b38%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.157936%2C129.150475%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_e457c7b7faef459186dec9b49aa3a6f8%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_8288384a32384f92b1dde8339e0fd0bd%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_8288384a32384f92b1dde8339e0fd0bd%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_e457c7b7faef459186dec9b49aa3a6f8.setContent%28html_8288384a32384f92b1dde8339e0fd0bd%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_86c7867d51514910a4762a520a241b38.bindPopup%28popup_e457c7b7faef459186dec9b49aa3a6f8%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_5b0e292f33564144a25992d1ac5c0587%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.159919%2C129.168761%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_d0090e64ec27484cafeec2204ffc99b3%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_685629d40b414e719e6cd52753d8d565%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_685629d40b414e719e6cd52753d8d565%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EGeneral%20Entertainment%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_d0090e64ec27484cafeec2204ffc99b3.setContent%28html_685629d40b414e719e6cd52753d8d565%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_5b0e292f33564144a25992d1ac5c0587.bindPopup%28popup_d0090e64ec27484cafeec2204ffc99b3%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_5eff9154100c4a77800570896401fb48%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.157389%2C129.151197%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_ce1fbac7905348fdbcfdbfd11b8a3b65%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_ef92268abbc14109869e7f8b8f99955b%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_ef92268abbc14109869e7f8b8f99955b%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_ce1fbac7905348fdbcfdbfd11b8a3b65.setContent%28html_ef92268abbc14109869e7f8b8f99955b%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_5eff9154100c4a77800570896401fb48.bindPopup%28popup_ce1fbac7905348fdbcfdbfd11b8a3b65%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_1206ee3fff194d61976f543457c33c7d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.15816588127223%2C129.1489593519692%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_60558cc41df543c398f95d8fd1983a94%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_b868cbeb29cd4ec8b8a5f2fb7ce18f46%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_b868cbeb29cd4ec8b8a5f2fb7ce18f46%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_60558cc41df543c398f95d8fd1983a94.setContent%28html_b868cbeb29cd4ec8b8a5f2fb7ce18f46%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_1206ee3fff194d61976f543457c33c7d.bindPopup%28popup_60558cc41df543c398f95d8fd1983a94%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_d0536bc6543c42a09eb0ca08d0200784%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.15807342529297%2C129.1517333984375%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_5b3b0a16545a4143b0013d3ca15d22bf%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_29e8b42b99ce42bdb81f4cfc236df065%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_29e8b42b99ce42bdb81f4cfc236df065%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaf%C3%A9%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_5b3b0a16545a4143b0013d3ca15d22bf.setContent%28html_29e8b42b99ce42bdb81f4cfc236df065%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_d0536bc6543c42a09eb0ca08d0200784.bindPopup%28popup_5b3b0a16545a4143b0013d3ca15d22bf%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_9e85d881dea842678659824da57d9781%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.15864253044026%2C129.15126085280997%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_2b8f819bd3f04f4597a845f8aad727cc%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_dc8949a07b20466ba5fa061e1bb5d584%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_dc8949a07b20466ba5fa061e1bb5d584%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EBakery%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_2b8f819bd3f04f4597a845f8aad727cc.setContent%28html_dc8949a07b20466ba5fa061e1bb5d584%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_9e85d881dea842678659824da57d9781.bindPopup%28popup_2b8f819bd3f04f4597a845f8aad727cc%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_a3c6e4b0dad844da89950d6e54f8d803%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B35.160586%2C129.144689%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22blue%22%2C%0A%20%20%22fillOpacity%22%3A%200.6%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_71f955d25ee249969203b0a7dfa2dd09%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_8aedf93529ca4618afe0ccb683fa69b3%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_cc115233461f410ca33fba094e517ce4%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_cc115233461f410ca33fba094e517ce4%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECoffee%20Shop%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_8aedf93529ca4618afe0ccb683fa69b3.setContent%28html_cc115233461f410ca33fba094e517ce4%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_a3c6e4b0dad844da89950d6e54f8d803.bindPopup%28popup_8aedf93529ca4618afe0ccb683fa69b3%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%3C/script%3E onload="this.contentDocument.open();this.contentDocument.write(    decodeURIComponent(this.getAttribute('data-html')));this.contentDocument.close();" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



## 3. What about Shilla Stay Haeundae Cafe?

### Shilla Stay Haeundae Cafe is related to Samsung, the largest company in South Korea. Let's see if the cafe is as good as Samsung's reputation.


```python
venue_id = '590713f31543c754e1b6fa90'
url = 'https://api.foursquare.com/v2/venues/{}?client_id={}&client_secret={}&oauth_token={}&v={}'.format(venue_id, CLIENT_ID, CLIENT_SECRET,ACCESS_TOKEN, VERSION)
url
```




    'https://api.foursquare.com/v2/venues/590713f31543c754e1b6fa90?client_id=X3HMAFSD210LBZULFUWZ2YG1TWQ0M2LVYCMP5CXURNSC3SOX&client_secret=SXOYXNP4TY5KF0VHYC0KJ1KLE1QQXDKLTCROFCVQ0OXWHINW&oauth_token=W14AGTRXCHB0IX4EHJLGAYLVZOICRQQKNAFFT1ZRPOEWH3A5&v=20180604'




```python
result = requests.get(url).json()
print(result['response']['venue'].keys())
result['response']['venue']
```

    dict_keys(['id', 'name', 'contact', 'location', 'canonicalUrl', 'categories', 'verified', 'stats', 'likes', 'like', 'dislike', 'ok', 'rating', 'ratingColor', 'ratingSignals', 'allowMenuUrlEdit', 'beenHere', 'specials', 'photos', 'reasons', 'hereNow', 'createdAt', 'tips', 'shortUrl', 'timeZone', 'listed', 'seasonalHours', 'pageUpdates', 'inbox', 'parent', 'hierarchy', 'attributes', 'bestPhoto', 'colors'])
    




    {'id': '590713f31543c754e1b6fa90',
     'name': 'Shilla Stay Haeundae Cafe',
     'contact': {'phone': '0519119307', 'formattedPhone': '051-911-9307'},
     'location': {'address': '해운대로570번길 46',
      'lat': 35.15997208166144,
      'lng': 129.1587610644076,
      'labeledLatLngs': [{'label': 'display',
        'lat': 35.15997208166144,
        'lng': 129.1587610644076}],
      'postalCode': '48093',
      'cc': 'KR',
      'state': '부산광역시',
      'country': '대한민국',
      'formattedAddress': ['해운대로570번길 46', '해운대구', '부산광역시', '48093']},
     'canonicalUrl': 'https://foursquare.com/v/shilla-stay-haeundae-cafe/590713f31543c754e1b6fa90',
     'categories': [{'id': '52e81612bcbc57f1066b79f4',
       'name': 'Buffet',
       'pluralName': 'Buffets',
       'shortName': 'Buffet',
       'icon': {'prefix': 'https://ss3.4sqi.net/img/categories_v2/food/default_',
        'suffix': '.png'},
       'primary': True}],
     'verified': False,
     'stats': {'tipCount': 1},
     'likes': {'count': 13,
      'groups': [{'type': 'others', 'count': 13, 'items': []}],
      'summary': '13 Likes'},
     'like': False,
     'dislike': False,
     'ok': False,
     'rating': 7.8,
     'ratingColor': 'C5DE35',
     'ratingSignals': 16,
     'allowMenuUrlEdit': True,
     'beenHere': {'count': 0,
      'unconfirmedCount': 0,
      'marked': False,
      'lastCheckinExpiredAt': 0},
     'specials': {'count': 0, 'items': []},
     'photos': {'count': 11,
      'groups': [{'type': 'venue',
        'name': 'Venue photos',
        'count': 11,
        'items': [{'id': '5f04fc37d729a133676c3aa1',
          'createdAt': 1594162231,
          'source': {'name': 'Swarm for iOS', 'url': 'https://www.swarmapp.com'},
          'prefix': 'https://fastly.4sqi.net/img/general/',
          'suffix': '/20301648_mLUQfjJk5vf24XRyv5WiCndKRreSi4FPBHBpk1GK5ys.jpg',
          'width': 1920,
          'height': 1440,
          'user': {'id': '20301648',
           'firstName': 'Younghwa',
           'lastName': 'Han',
           'gender': 'female',
           'countryCode': 'KR',
           'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
            'suffix': '/20301648_p8RtPcFv_k-CQP9Si6j5Cdl0L5VkH5NUfwLqwuCBF7-56Ue-cNW6arfLYGjkPLpk80lFouWDd.jpg'}},
          'visibility': 'public'}]}]},
     'reasons': {'count': 0, 'items': []},
     'hereNow': {'count': 0, 'summary': 'Nobody here', 'groups': []},
     'createdAt': 1493636083,
     'tips': {'count': 1,
      'groups': [{'type': 'following',
        'name': 'Tips from people you follow',
        'count': 0,
        'items': []},
       {'type': 'others',
        'name': 'All tips',
        'count': 1,
        'items': [{'id': '5bd187a0c21cb1002c5ac3a3',
          'createdAt': 1540458400,
          'text': 'At night the cafor room is transformed into a lively bar, where people confraterfine. The service is good and the fitness center is quite complete. The price is very bad.',
          'type': 'user',
          'canonicalUrl': 'https://foursquare.com/item/5bd187a0c21cb1002c5ac3a3',
          'likes': {'count': 0, 'groups': []},
          'like': False,
          'logView': True,
          'agreeCount': 0,
          'disagreeCount': 0,
          'todo': {'count': 0},
          'user': {'id': '147304729',
           'firstName': 'Israel',
           'lastName': 'Teixeira',
           'gender': 'male',
           'countryCode': 'KR',
           'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
            'suffix': '/147304729-CEG0FMGRYUTZOXV1.jpg'}},
          'authorInteractionType': 'liked'}]}]},
     'shortUrl': 'http://4sq.com/2oOUm95',
     'timeZone': 'Asia/Seoul',
     'listed': {'count': 1,
      'groups': [{'type': 'others',
        'name': 'Lists from other people',
        'count': 1,
        'items': [{'id': '5c88f1e63fffb40025d7e75c',
          'name': 'อาหารเช้า',
          'description': '',
          'type': 'others',
          'user': {'id': '534904445',
           'firstName': 'siriyapron',
           'lastName': 'akka',
           'gender': 'female',
           'countryCode': 'TH',
           'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
            'suffix': '/blank_girl.png',
            'default': True}},
          'editable': False,
          'public': True,
          'collaborative': False,
          'url': '/user/534904445/list/%E0%B8%AD%E0%B8%B2%E0%B8%AB%E0%B8%B2%E0%B8%A3%E0%B9%80%E0%B8%8A%E0%B8%B2',
          'canonicalUrl': 'https://foursquare.com/user/534904445/list/%E0%B8%AD%E0%B8%B2%E0%B8%AB%E0%B8%B2%E0%B8%A3%E0%B9%80%E0%B8%8A%E0%B8%B2',
          'createdAt': 1552478694,
          'updatedAt': 1554752156,
          'photo': {'id': '52ad075011d2696d5ec977e6',
           'createdAt': 1387071312,
           'prefix': 'https://fastly.4sqi.net/img/general/',
           'suffix': '/787736_LbTQrdGwfe3BuqcuypXsVxOuVEGqpgZGMoiX1JMB554.jpg',
           'width': 640,
           'height': 640,
           'user': {'id': '787736',
            'firstName': 'Ryumi',
            'lastName': 'Pernok',
            'gender': 'none',
            'countryCode': 'KR',
            'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
             'suffix': '/787736-XWBC3E455QHTLR0Y.jpg'}},
           'visibility': 'public'},
          'followers': {'count': 0},
          'listItems': {'count': 18,
           'items': [{'id': 'v590713f31543c754e1b6fa90',
             'createdAt': 1554751988}]}}]}]},
     'seasonalHours': [],
     'pageUpdates': {'count': 0, 'items': []},
     'inbox': {'count': 0, 'items': []},
     'parent': {'id': '58d74fa4fb549a68f6ca5592',
      'name': 'Shilla Stay Haeundae (신라스테이 해운대)',
      'location': {'address': '해운대구 해운대로570번길 46',
       'lat': 35.15987906557542,
       'lng': 129.15873621971136,
       'labeledLatLngs': [{'label': 'display',
         'lat': 35.15987906557542,
         'lng': 129.15873621971136}],
       'postalCode': '48093',
       'cc': 'KR',
       'state': '부산광역시',
       'country': '대한민국',
       'formattedAddress': ['해운대구 해운대로570번길 46',
        'Haeundae - gu',
        '부산광역시',
        '48093']},
      'categories': [{'id': '4bf58dd8d48988d1fa931735',
        'name': 'Hotel',
        'pluralName': 'Hotels',
        'shortName': 'Hotel',
        'icon': {'prefix': 'https://ss3.4sqi.net/img/categories_v2/travel/hotel_',
         'suffix': '.png'},
        'primary': True}]},
     'hierarchy': [{'name': 'Shilla Stay Haeundae',
       'lang': 'KR',
       'id': '58d74fa4fb549a68f6ca5592',
       'canonicalUrl': 'https://foursquare.com/v/shilla-stay-haeundae/58d74fa4fb549a68f6ca5592'}],
     'attributes': {'groups': [{'type': 'reservations',
        'name': 'Reservations',
        'summary': 'Reservations',
        'count': 3,
        'items': [{'displayName': 'Reservations', 'displayValue': 'Yes'}]},
       {'type': 'payments',
        'name': 'Credit Cards',
        'summary': 'Credit Cards',
        'count': 5,
        'items': [{'displayName': 'Credit Cards', 'displayValue': 'Yes'}]}]},
     'bestPhoto': {'id': '5f04fc37d729a133676c3aa1',
      'createdAt': 1594162231,
      'source': {'name': 'Swarm for iOS', 'url': 'https://www.swarmapp.com'},
      'prefix': 'https://fastly.4sqi.net/img/general/',
      'suffix': '/20301648_mLUQfjJk5vf24XRyv5WiCndKRreSi4FPBHBpk1GK5ys.jpg',
      'width': 1920,
      'height': 1440,
      'visibility': 'public'},
     'colors': {'highlightColor': {'photoId': '5f04fc37d729a133676c3aa1',
       'value': -11528192},
      'highlightTextColor': {'photoId': '5f04fc37d729a133676c3aa1', 'value': -1},
      'algoVersion': 3}}




```python
try:
    print(result['response']['venue']['rating'])
except:
    print('This venue has not been rated yet.')
```

    7.8
    

### Although it is a bit disappointing compared to Samsung's name value, it is getting a good score. Let's figure out what that assessment is.


```python
limit = 15 # set limit to be greater than or equal to the total number of tips
url = 'https://api.foursquare.com/v2/venues/{}/tips?client_id={}&client_secret={}&oauth_token={}&v={}&limit={}'.format(venue_id, CLIENT_ID, CLIENT_SECRET,ACCESS_TOKEN, VERSION, limit)

results = requests.get(url).json()
results
```




    {'meta': {'code': 200, 'requestId': '602438a4d203981fd4a8500b'},
     'notifications': [{'type': 'notificationTray', 'item': {'unreadCount': 0}}],
     'response': {'tips': {'count': 1,
       'items': [{'id': '5bd187a0c21cb1002c5ac3a3',
         'createdAt': 1540458400,
         'text': 'At night the cafor room is transformed into a lively bar, where people confraterfine. The service is good and the fitness center is quite complete. The price is very bad.',
         'type': 'user',
         'canonicalUrl': 'https://foursquare.com/item/5bd187a0c21cb1002c5ac3a3',
         'likes': {'count': 0, 'groups': []},
         'like': False,
         'logView': True,
         'agreeCount': 0,
         'disagreeCount': 0,
         'todo': {'count': 0},
         'user': {'id': '147304729',
          'firstName': 'Israel',
          'lastName': 'Teixeira',
          'gender': 'male',
          'countryCode': 'KR',
          'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
           'suffix': '/147304729-CEG0FMGRYUTZOXV1.jpg'}},
         'authorInteractionType': 'liked'}]}}}




```python
tips = results['response']['tips']['items']

tip = results['response']['tips']['items'][0]
tip.keys()
```




    dict_keys(['id', 'createdAt', 'text', 'type', 'canonicalUrl', 'likes', 'like', 'logView', 'agreeCount', 'disagreeCount', 'todo', 'user', 'authorInteractionType'])




```python
pd.set_option('display.max_colwidth', -1)

tips_df = json_normalize(tips) # json normalize tips

# columns to keep
filtered_columns = ['text', 'agreeCount', 'disagreeCount', 'id', 'user.firstName', 'user.lastName', 'user.id']
tips_filtered = tips_df.loc[:, filtered_columns]

# display tips
tips_filtered.reindex()
```

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:1: FutureWarning: Passing a negative integer is deprecated in version 1.0 and will not be supported in future version. Instead, use None to not limit the column width.
      """Entry point for launching an IPython kernel.
    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:3: FutureWarning: pandas.io.json.json_normalize is deprecated, use pandas.json_normalize instead
      This is separate from the ipykernel package so we can avoid doing imports until
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>text</th>
      <th>agreeCount</th>
      <th>disagreeCount</th>
      <th>id</th>
      <th>user.firstName</th>
      <th>user.lastName</th>
      <th>user.id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>At night the cafor room is transformed into a lively bar, where people confraterfine. The service is good and the fitness center is quite complete. The price is very bad.</td>
      <td>0</td>
      <td>0</td>
      <td>5bd187a0c21cb1002c5ac3a3</td>
      <td>Israel</td>
      <td>Teixeira</td>
      <td>147304729</td>
    </tr>
  </tbody>
</table>
</div>



# Result

## It shows a good atmosphere, but there is an assessment that the price is very bad. It can't be said that it is worth 

# Discussion

## It may be reasonable to go to a Starbucks that is not necessarily good because of its high name value.

# Conclusion

## For reasonable consumption at cafes, it is necessary to give up the atmosphere.
