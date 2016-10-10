---
published: true
layout: post
date: '2016-10-08 07:15:14 -0400'
categories: data
title: Mapping Jane Jacobs' Cities
---

# Mapping the Places in *The Death and Life of Great American Cities*

Will Geary

<iframe width="100%" height="520" frameborder="0" src="https://willgeary.carto.com/viz/31c6b2d0-8db4-11e6-ab40-0ef7f98ade21/embed_map" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

I used optical character recognition to convert a pdf into text, named entity recognition algorithm to parse the text for places, Google Maps' geocoding API to pull latitude / longitude coordinates and Carto to map everything. All of code is available [here](http://nbviewer.jupyter.org/github/willgeary/janejacobs/blob/master/Notebook.ipynb).

A quick overview of my steps

### Find the pdf

![fig]("https://raw.githubusercontent.com/willgeary/janejacobs/master/Images/Cover.png")


### Convert pdf to text


![fig]("https://raw.githubusercontent.com/willgeary/janejacobs/master/Images/screencast.gif")


### Parse text for locations

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


### Use Google Maps API to pull lat/lon coordinates 


Create a table of each city and its latitude / longitude coordinates.


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



### Map it

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