---
published: true
layout: post
date: '2016-10-08 07:15:14 -0400'
categories: data
title: Mapping Jane Jacobs
---

<iframe width="100%" height="520" frameborder="0" src="https://willgeary.carto.com/viz/31c6b2d0-8db4-11e6-ab40-0ef7f98ade21/embed_map?zoom=4&center_lat=40&center_lon=-100" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>



I used optical character recognition to convert a pdf into text, named entity recognition algorithm to parse the text for places, Google Maps' geocoding API to pull latitude / longitude coordinates and Carto to map everything. All of code is available [here](http://nbviewer.jupyter.org/github/willgeary/janejacobs/blob/master/Notebook.ipynb).

A quick overview of my steps...

### Find the pdf

![fig](https://raw.githubusercontent.com/willgeary/janejacobs/master/Images/Cover.png)


### Convert pdf to text


![fig](https://raw.githubusercontent.com/willgeary/janejacobs/master/Images/screencast.gif)


### Parse text for locations

Use the [**geotext**](https://pypi.python.org/pypi/geotext) library to extract country and cities from the text.


```python
from geotext import GeoText
places = GeoText(s)
```

Look below at a few of the cities found using this tool. Clearly, there are some mistakes (Mary and Nelson are names, not places) but some cities are correctly identified (Chicago, Philadelphia). Let's ignore this problem for now and come back to it later.


```python
places.cities[:10]

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
```


### Use Google Maps API to pull lat/lon coordinates 


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
Checking that it works correctly for Boston:

![fig](https://raw.githubusercontent.com/willgeary/janejacobs/master/Images/Boston.png)


### Map it

I mapped the cities using [Carto](https://willgeary.carto.com/viz/31c6b2d0-8db4-11e6-ab40-0ef7f98ade21/public_map). The size of the bubbles represents the number of times that each city is mentioned in the text.

<iframe width="100%" height="520" frameborder="0" src="https://willgeary.carto.com/viz/31c6b2d0-8db4-11e6-ab40-0ef7f98ade21/embed_map" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>
