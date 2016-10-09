---
published: true
layout: post
date: '2016-10-08 07:15:14 -0400'
categories: data
title: Mapping Jane Jacobs' Cities
---

# Mapping the Places in *The Death and Life of Great American Cities*

Will Geary

<iframe src="https://willgeary.carto.com/viz/31c6b2d0-8db4-11e6-ab40-0ef7f98ade21/embed_map" style="width:100%;height:500px;"></iframe>
<br>


*The Death and Life of Great American Cities* is a critique of 1950s urban planning dogma. It was written in 1961 by journalist-activist Jane Jacobs when she was living in Manhattan's Greenwich Village. In the text, Jacobs compares and contrasts many different cities. The book had a tremendous impact on me when I first read it, so I thought it would be fun to make a map of every place mentioned in the book.

## 1. Download 'Death and Life of Great American Cities' as a pdf

Full pdf available here. Don't ask how I found it :) https://github.com/willgeary/janejacobs/tree/master/PDF

As you can see below, the pdf is not in the best condition.

![fig]("https://raw.githubusercontent.com/willgeary/janejacobs/master/Images/Cover.png")




![png](output_4_0.png)



## 2. Covert the pdf to a text file

The first challenge is to convert the pdf into a searchable, editable text file. This proved to be quite difficult. There are many open source tools which claim to be able do this. Some failed entirely, others returned complete gibberish (presumabely due to the poor quality of the pdf).

However, I finally found a tool that works. I used PDFMiner to convert the pdf into a text file. PDFMiner is a tool for extracting information from PDF documents. It is written in Python. More info on PDFMiner here: http://www.unixuser.org/~euske/python/pdfminer/index.html#source

In short, it works like this:


```python
"""
1. Install Python 2.4 or newer. (Python 3 is not supported.)
2. Download the PDFMiner source.
3. Unpack it.
4. Run setup.py to install:
    # python setup.py install
5. Do the following test:
    $ pdf2txt.py samples/simple1.pdf
        Hello
        World
"""
```

Once I installed PDFMiner, I was able to convert the entire pdf into a text file using the following command line instruction: 


```python
"""
pdf2txt.py -o output.txt death_and_life_of_great_american_cities.pdf
"""
```

Here is a gif of PDFMiner doing it's thing. You can see that there is some gibberish / mispellings in the output.


```python
Image(url='https://raw.githubusercontent.com/willgeary/janejacobs/master/Images/screencast.gif') 
```




<img src="https://raw.githubusercontent.com/willgeary/janejacobs/master/Images/screencast.gif"/>



## 3. Open text file and read it into memory

Now we have the entire 'Death and Life' book in a searchable text file. Let's read it into memory and check it out.


```python
# Read in the text file into memory
with open('output.txt', 'r') as myfile:
    data=myfile.read().replace('\n', '')
```


```python
# Take a look at the text
data[:1000]
```




    ', ,,/ THEDEATH AND LIFE OFGREAT AMERICAN CITIES JANE JACOBS V-241 $4.95 \x0cTHE DEATH AND LIFE OF GREAT AMERICAN CITIES \xe2\x80\xa2 Jane Jacobs VINTAGE  BOOKS A Division of &ndo11l HOllse NEW  YORK \x0cAcknowledgment permission ill their pages: Arc/)ilrctllf\'lli F"nnll, Hilrpcr\'r MI1SIF,.,jflC, i\xef\xbf\xbd made to the following publications of this book which first  appeared Fo"nll, the Columbiil U\'fIi;;erJily to reprint portions for T/;c Reporter . \xe2\x80\xa2 @ Copyright, Ip61, by lillie Jacobi \xef\xbf\xbdIJ rights resen\'cd uncler Inlem\xef\xbf\xbdtional Published rrght COII,:,cntions. Inc., and snnult:lneously of Canada, Limited. and Pan-American Copy\xc2\xadill New York by Random House, ill Tvrunto, Dnacla, by Random House ,\\\'Ignufacrurcd in rhe United StlItcs uf America \x0cTo NEW  YOAIt  CITY where I ClIme [0 seek my fortune and found it by finding Bob, Jimmy, Ned and Mary for whom this book is written [00 \xe2\x80\xa2 \xe2\x80\xa2 \x0cAcknowledgment In particular I am grateful June Blythe, Gndy Clay, William for given by the: following So many scoru  of'



## 4. Parse through the text file

Use the natural language processing library **nltk** to tokenize all of the words in the text. See here for more info: http://www.nltk.org/


```python
# Decode the text as ASCII string, ignoring errors caused by some unrecognizable characters
s = data.decode('ascii', 'ignore')
```


```python
# Word tokenize the string of characters using nltk module
import nltk
tokens = nltk.word_tokenize(s)

# Preview
tokens[:10]
```




    [u',',
     u',',
     u',/',
     u'THEDEATH',
     u'AND',
     u'LIFE',
     u'OFGREAT',
     u'AMERICAN',
     u'CITIES',
     u'JANE']



## 5. Extract country and city mentions from text

Use the **geotext** library to extract country and cities from the text. More info on geotext here: https://pypi.python.org/pypi/geotext


```python
from geotext import GeoText
```


```python
places = GeoText(s)
```

Look below at a few of the cities found using this tool. Clearly, there are some mistakes (Mary and Nelson are names, not places) but some cities are correctly identified (Chicago, Philadelphia). Let's ignore this problem for now and come back to it later.


```python
places.cities[:10]
```




    [u'YORK',
     u'Mary',
     u'Nelson',
     u'Kent',
     u'Most',
     u'Of',
     u'Chicago',
     u'Chicago',
     u'York',
     u'Philadelphia']




```python
places.countries[:10]
```




    [u'Canada',
     u'Chad',
     u'Ireland',
     u'United States',
     u'Sweden',
     u'Solomon Islands',
     u'Jersey',
     u'United States',
     u'United States',
     u'Venezuela']



## 6. Try using Google Maps API to pull lat/lon coordinates for a city

Try pulling latitude and longitude coordinates for one city.


```python
import googlemaps
```


```python
API_key = "AIzaSyBft2Y_yT23uMct_Isw-Zhj5JEp0e-p88g"
gmaps = googlemaps.Client(key=API_key)
```

Test the API by attempting to geocode one of the cities:


```python
places.cities[10]
```




    u'Boston'




```python
geocode_result = gmaps.geocode(places.cities[10])
lat = geocode_result[0]['geometry']['location']['lat']
lon = geocode_result[0]['geometry']['location']['lng']

print (lat, lon)
```

    (42.3600825, -71.0588801)


It works! Correctly returns the lat/lon coordinates for Boston City Hall.


```python
Image("https://raw.githubusercontent.com/willgeary/janejacobs/master/Images/Boston.png")
```




![png](output_30_0.png)



## 7. Pull latitude and longitutude coordinates for each city

Iterate through the list of cities and ask the Google Maps API for each city's lat/lon coordinates.


```python
coords = {}

for city in places.cities:
    geocode_result = gmaps.geocode(city)
    
    # if google maps request doesn't return any data, don't do anything
    if len(geocode_result) == 0:
        pass
    else:
        lat = geocode_result[0]['geometry']['location']['lat']
        lon = geocode_result[0]['geometry']['location']['lng']
        coords[city] = ((lat,lon))
```


```python
coords['Baltimore']
```




    (39.2903848, -76.6121893)



Create a table of each city and its latitude / longitude coordinates.


```python
import pandas as pd
df = pd.DataFrame(coords).transpose()
df.rename(columns={0: "Latitude", 1: "Longitude"}, inplace=True)
df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>ALVIN</th>
      <td>29.423847</td>
      <td>-95.244101</td>
    </tr>
    <tr>
      <th>AMI</th>
      <td>36.030795</td>
      <td>140.214786</td>
    </tr>
    <tr>
      <th>ATH</th>
      <td>52.991834</td>
      <td>-6.985728</td>
    </tr>
    <tr>
      <th>Along</th>
      <td>28.162784</td>
      <td>94.805414</td>
    </tr>
    <tr>
      <th>Baltimore</th>
      <td>39.290385</td>
      <td>-76.612189</td>
    </tr>
  </tbody>
</table>
</div>



It would also be cool to count how many times each city is mentioned in the book. Let's do that.


```python
city_counts = [tokens.count(city) for city in list(df.index)]
```


```python
df['Frequency'] = city_counts
```


```python
df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Frequency</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>ALVIN</th>
      <td>29.423847</td>
      <td>-95.244101</td>
      <td>2</td>
    </tr>
    <tr>
      <th>AMI</th>
      <td>36.030795</td>
      <td>140.214786</td>
      <td>3</td>
    </tr>
    <tr>
      <th>ATH</th>
      <td>52.991834</td>
      <td>-6.985728</td>
      <td>18</td>
    </tr>
    <tr>
      <th>Along</th>
      <td>28.162784</td>
      <td>94.805414</td>
      <td>6</td>
    </tr>
    <tr>
      <th>Baltimore</th>
      <td>39.290385</td>
      <td>-76.612189</td>
      <td>14</td>
    </tr>
  </tbody>
</table>
</div>



Save this table to a csv.


```python
df.to_csv('mapdata.csv')
```

## 8. Map the coordinates

I mapped the cities using Carto. The size of the bubbles represents the number of times that each city is mentioned in the text.


```python
from IPython.display import IFrame
IFrame("https://willgeary.carto.com/viz/31c6b2d0-8db4-11e6-ab40-0ef7f98ade21/embed_map", 800, 500)
```





        <iframe
            width="800"
            height="500"
            src="https://willgeary.carto.com/viz/31c6b2d0-8db4-11e6-ab40-0ef7f98ade21/embed_map"
            frameborder="0"
            allowfullscreen
        ></iframe>
        



## Conclusion

I'm excited to have somewhat successfully converted the pdf into text, parsed it for locations and created a map.

However, there are clearly some problems here. Namely, some of the cities parsed are not actually cities (or atleast not in the context that Jacobs intended).

Some problems that I noticed include:
* Some neighborhoods/boroughs are being treated like cities. Chelsea, for example, is a neighborhood in New York that Jacobs mentions five times. It is also, however, the name of a town in Massachussetts. The latter Chelsea is being displayed in my map. The same has happened with Manhattan.
* Some non-places are being treated as cities. For example: University, Most, Martin, Of, etc. There are lots of these.

In order to be fully satisfied with this map, I would need to parse through the cities manually and remove the nonsensical ones.
