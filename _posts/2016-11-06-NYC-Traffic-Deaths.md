---
published: true
layout: post
date: '2016-11-06 07:15:14 -0400'
categories: data
title: NYC Traffic Deaths
---
<iframe width="100%" height="520" frameborder="0" src="https://willgeary.carto.com/viz/0891f228-a478-11e6-b997-0e3ebc282e83/embed_map" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

This map was visualized using Carto. The data behind it can be found [here](https://data.cityofnewyork.us/Public-Safety/NYPD-Motor-Vehicle-Collisions/h9gi-nx95/data).

I used python to wrangle the data into a suitable form for the visualization. My steps are below.


# Motor Vehicle Crashes in NYC


```python
url = 'https://data.cityofnewyork.us/api/views/h9gi-nx95/rows.csv?accessType=DOWNLOAD' # Date of download = 11/6/2016
data = pd.read_csv(url)
data.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>DATE</th>
      <th>TIME</th>
      <th>BOROUGH</th>
      <th>ZIP CODE</th>
      <th>LATITUDE</th>
      <th>LONGITUDE</th>
      <th>LOCATION</th>
      <th>ON STREET NAME</th>
      <th>CROSS STREET NAME</th>
      <th>OFF STREET NAME</th>
      <th>...</th>
      <th>CONTRIBUTING FACTOR VEHICLE 2</th>
      <th>CONTRIBUTING FACTOR VEHICLE 3</th>
      <th>CONTRIBUTING FACTOR VEHICLE 4</th>
      <th>CONTRIBUTING FACTOR VEHICLE 5</th>
      <th>UNIQUE KEY</th>
      <th>VEHICLE TYPE CODE 1</th>
      <th>VEHICLE TYPE CODE 2</th>
      <th>VEHICLE TYPE CODE 3</th>
      <th>VEHICLE TYPE CODE 4</th>
      <th>VEHICLE TYPE CODE 5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>09/18/2016</td>
      <td>19:09</td>
      <td>QUEENS</td>
      <td>11422.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>234-32    131 AVENUE</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3522011</td>
      <td>SPORT UTILITY / STATION WAGON</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>09/18/2016</td>
      <td>19:09</td>
      <td>BROOKLYN</td>
      <td>11237.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>140       MORGAN AVENUE</td>
      <td>...</td>
      <td>Driver Inattention/Distraction</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3522438</td>
      <td>TAXI</td>
      <td>SPORT UTILITY / STATION WAGON</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>08/25/2015</td>
      <td>19:00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40.732941</td>
      <td>-73.920382</td>
      <td>(40.7329414, -73.9203819)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>Unspecified</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3284922</td>
      <td>PASSENGER VEHICLE</td>
      <td>TAXI</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>07/27/2012</td>
      <td>22:35</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>Unspecified</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2833714</td>
      <td>PASSENGER VEHICLE</td>
      <td>PASSENGER VEHICLE</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>05/17/2014</td>
      <td>0:45</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>PENNSYLVANIA AVENUE</td>
      <td>RIVERDALE AVENUE</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>336679</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 29 columns</p>
</div>




```python
df = data.copy()
```


```python
# Drop rows that have are missing coordinates
df = df[df.LATITUDE.notnull()]
```


```python
df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>DATE</th>
      <th>TIME</th>
      <th>BOROUGH</th>
      <th>ZIP CODE</th>
      <th>LATITUDE</th>
      <th>LONGITUDE</th>
      <th>LOCATION</th>
      <th>ON STREET NAME</th>
      <th>CROSS STREET NAME</th>
      <th>OFF STREET NAME</th>
      <th>...</th>
      <th>CONTRIBUTING FACTOR VEHICLE 2</th>
      <th>CONTRIBUTING FACTOR VEHICLE 3</th>
      <th>CONTRIBUTING FACTOR VEHICLE 4</th>
      <th>CONTRIBUTING FACTOR VEHICLE 5</th>
      <th>UNIQUE KEY</th>
      <th>VEHICLE TYPE CODE 1</th>
      <th>VEHICLE TYPE CODE 2</th>
      <th>VEHICLE TYPE CODE 3</th>
      <th>VEHICLE TYPE CODE 4</th>
      <th>VEHICLE TYPE CODE 5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>08/25/2015</td>
      <td>19:00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40.732941</td>
      <td>-73.920382</td>
      <td>(40.7329414, -73.9203819)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>Unspecified</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3284922</td>
      <td>PASSENGER VEHICLE</td>
      <td>TAXI</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>6</th>
      <td>09/18/2016</td>
      <td>1:09</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40.862498</td>
      <td>-73.934185</td>
      <td>(40.8624983, -73.9341848)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>Unspecified</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3524356</td>
      <td>PASSENGER VEHICLE</td>
      <td>SPORT UTILITY / STATION WAGON</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>23</th>
      <td>05/03/2016</td>
      <td>8:00</td>
      <td>QUEENS</td>
      <td>11385.0</td>
      <td>40.707891</td>
      <td>-73.909622</td>
      <td>(40.7078905, -73.9096224)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1934 GREENE AVENUE</td>
      <td>...</td>
      <td>Unspecified</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3434031</td>
      <td>PASSENGER VEHICLE</td>
      <td>PASSENGER VEHICLE</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>25</th>
      <td>05/03/2016</td>
      <td>8:09</td>
      <td>BROOKLYN</td>
      <td>11208.0</td>
      <td>40.678828</td>
      <td>-73.866171</td>
      <td>(40.6788277, -73.866171)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1168 LIBERTY AVENUE</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3433897</td>
      <td>PASSENGER VEHICLE</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>26</th>
      <td>05/03/2016</td>
      <td>8:10</td>
      <td>MANHATTAN</td>
      <td>10031.0</td>
      <td>40.830669</td>
      <td>-73.944886</td>
      <td>(40.8306685, -73.9448864)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>528 WEST 153 STREET</td>
      <td>...</td>
      <td>Unspecified</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3434479</td>
      <td>PASSENGER VEHICLE</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 29 columns</p>
</div>




```python
df = df[['DATE', 'TIME', 'BOROUGH','LATITUDE', 'LONGITUDE', 'NUMBER OF PERSONS KILLED','NUMBER OF PEDESTRIANS INJURED',\
       'NUMBER OF PEDESTRIANS KILLED', 'NUMBER OF CYCLIST INJURED',
       'NUMBER OF CYCLIST KILLED', 'NUMBER OF MOTORIST INJURED',
       'NUMBER OF MOTORIST KILLED']]
```


```python
df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>DATE</th>
      <th>TIME</th>
      <th>BOROUGH</th>
      <th>LATITUDE</th>
      <th>LONGITUDE</th>
      <th>NUMBER OF PERSONS KILLED</th>
      <th>NUMBER OF PEDESTRIANS INJURED</th>
      <th>NUMBER OF PEDESTRIANS KILLED</th>
      <th>NUMBER OF CYCLIST INJURED</th>
      <th>NUMBER OF CYCLIST KILLED</th>
      <th>NUMBER OF MOTORIST INJURED</th>
      <th>NUMBER OF MOTORIST KILLED</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>08/25/2015</td>
      <td>19:00</td>
      <td>NaN</td>
      <td>40.732941</td>
      <td>-73.920382</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>09/18/2016</td>
      <td>1:09</td>
      <td>NaN</td>
      <td>40.862498</td>
      <td>-73.934185</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>05/03/2016</td>
      <td>8:00</td>
      <td>QUEENS</td>
      <td>40.707891</td>
      <td>-73.909622</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>05/03/2016</td>
      <td>8:09</td>
      <td>BROOKLYN</td>
      <td>40.678828</td>
      <td>-73.866171</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>05/03/2016</td>
      <td>8:10</td>
      <td>MANHATTAN</td>
      <td>40.830669</td>
      <td>-73.944886</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
df[['NUMBER OF PERSONS KILLED','LATITUDE', 'LONGITUDE']].head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>NUMBER OF PERSONS KILLED</th>
      <th>LATITUDE</th>
      <th>LONGITUDE</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>40.732941</td>
      <td>-73.920382</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0</td>
      <td>40.862498</td>
      <td>-73.934185</td>
    </tr>
    <tr>
      <th>23</th>
      <td>0</td>
      <td>40.707891</td>
      <td>-73.909622</td>
    </tr>
    <tr>
      <th>25</th>
      <td>0</td>
      <td>40.678828</td>
      <td>-73.866171</td>
    </tr>
    <tr>
      <th>26</th>
      <td>0</td>
      <td>40.830669</td>
      <td>-73.944886</td>
    </tr>
  </tbody>
</table>
</div>




```python
final = df[df['NUMBER OF PERSONS KILLED'] > 0].copy()
```


```python
final['DATE'] = pd.to_datetime(final['DATE'], format='%m/%d/%Y')
```


```python
final = final.sort_values(by='DATE')
```


```python
final = final.reset_index(drop=True)
```


```python
final.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>DATE</th>
      <th>TIME</th>
      <th>BOROUGH</th>
      <th>LATITUDE</th>
      <th>LONGITUDE</th>
      <th>NUMBER OF PERSONS KILLED</th>
      <th>NUMBER OF PEDESTRIANS INJURED</th>
      <th>NUMBER OF PEDESTRIANS KILLED</th>
      <th>NUMBER OF CYCLIST INJURED</th>
      <th>NUMBER OF CYCLIST KILLED</th>
      <th>NUMBER OF MOTORIST INJURED</th>
      <th>NUMBER OF MOTORIST KILLED</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2012-07-02</td>
      <td>16:30</td>
      <td>BROOKLYN</td>
      <td>40.685846</td>
      <td>-73.947310</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2012-07-03</td>
      <td>20:55</td>
      <td>QUEENS</td>
      <td>40.764014</td>
      <td>-73.881216</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2012-07-05</td>
      <td>3:30</td>
      <td>BROOKLYN</td>
      <td>40.662736</td>
      <td>-73.957122</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2012-07-08</td>
      <td>21:54</td>
      <td>BROOKLYN</td>
      <td>40.680665</td>
      <td>-73.902622</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2012-07-10</td>
      <td>10:59</td>
      <td>NaN</td>
      <td>40.725176</td>
      <td>-73.837379</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
final.to_csv('nypdcrashes.csv')
```


```python
ped = final[final['NUMBER OF PEDESTRIANS KILLED']>0].copy()
ped.rename(columns={'NUMBER OF PEDESTRIANS KILLED':'Pedestrians Killed'},inplace=True)
ped_killed = ped[['DATE', 'TIME', 'BOROUGH', 'LATITUDE','LONGITUDE','Pedestrians Killed']]
ped_killed = ped_killed.reset_index(drop=True)
ped_killed['Type'] = 'Pedestrians Killed'
ped_killed.to_csv('ped_killed.csv')
ped_killed.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>DATE</th>
      <th>TIME</th>
      <th>BOROUGH</th>
      <th>LATITUDE</th>
      <th>LONGITUDE</th>
      <th>Pedestrians Killed</th>
      <th>Type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2012-07-05</td>
      <td>3:30</td>
      <td>BROOKLYN</td>
      <td>40.662736</td>
      <td>-73.957122</td>
      <td>1</td>
      <td>Pedestrians Killed</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2012-07-08</td>
      <td>21:54</td>
      <td>BROOKLYN</td>
      <td>40.680665</td>
      <td>-73.902622</td>
      <td>1</td>
      <td>Pedestrians Killed</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2012-07-10</td>
      <td>10:59</td>
      <td>NaN</td>
      <td>40.725176</td>
      <td>-73.837379</td>
      <td>1</td>
      <td>Pedestrians Killed</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2012-07-14</td>
      <td>21:20</td>
      <td>MANHATTAN</td>
      <td>40.796597</td>
      <td>-73.970370</td>
      <td>1</td>
      <td>Pedestrians Killed</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2012-07-14</td>
      <td>1:21</td>
      <td>BRONX</td>
      <td>40.838722</td>
      <td>-73.913771</td>
      <td>1</td>
      <td>Pedestrians Killed</td>
    </tr>
  </tbody>
</table>
</div>




```python
cyc = final[final['NUMBER OF CYCLIST KILLED']>0].copy()
cyc.rename(columns={'NUMBER OF CYCLIST KILLED':'Cyclists Killed'},inplace=True)
cyc_killed = cyc[['DATE', 'TIME', 'BOROUGH', 'LATITUDE','LONGITUDE','Cyclists Killed']]
cyc_killed = cyc_killed.reset_index(drop=True)
cyc_killed['Type'] = 'Cyclists Killed'
cyc_killed.to_csv('cyc_killed.csv')
cyc_killed.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>DATE</th>
      <th>TIME</th>
      <th>BOROUGH</th>
      <th>LATITUDE</th>
      <th>LONGITUDE</th>
      <th>Cyclists Killed</th>
      <th>Type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2012-07-13</td>
      <td>23:18</td>
      <td>BRONX</td>
      <td>40.878645</td>
      <td>-73.871598</td>
      <td>1</td>
      <td>Cyclists Killed</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2012-07-18</td>
      <td>22:51</td>
      <td>QUEENS</td>
      <td>40.739064</td>
      <td>-73.926158</td>
      <td>1</td>
      <td>Cyclists Killed</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2012-07-30</td>
      <td>19:50</td>
      <td>MANHATTAN</td>
      <td>40.794233</td>
      <td>-73.946946</td>
      <td>1</td>
      <td>Cyclists Killed</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2012-08-26</td>
      <td>21:08</td>
      <td>BROOKLYN</td>
      <td>40.698677</td>
      <td>-73.958858</td>
      <td>1</td>
      <td>Cyclists Killed</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2012-09-25</td>
      <td>6:03</td>
      <td>QUEENS</td>
      <td>40.712001</td>
      <td>-73.825456</td>
      <td>1</td>
      <td>Cyclists Killed</td>
    </tr>
  </tbody>
</table>
</div>




```python
car = final[final['NUMBER OF MOTORIST KILLED']>0].copy()
car.rename(columns={'NUMBER OF MOTORIST KILLED':'Motorists Killed'},inplace=True)
car_killed = car[['DATE', 'TIME', 'BOROUGH', 'LATITUDE','LONGITUDE','Motorists Killed']]
car_killed = car_killed.reset_index(drop=True)
car_killed['Type'] = 'Drivers Killed'
car_killed.to_csv('car_killed.csv')
car_killed.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>DATE</th>
      <th>TIME</th>
      <th>BOROUGH</th>
      <th>LATITUDE</th>
      <th>LONGITUDE</th>
      <th>Motorists Killed</th>
      <th>Type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2012-07-02</td>
      <td>16:30</td>
      <td>BROOKLYN</td>
      <td>40.685846</td>
      <td>-73.947310</td>
      <td>1</td>
      <td>Drivers Killed</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2012-07-03</td>
      <td>20:55</td>
      <td>QUEENS</td>
      <td>40.764014</td>
      <td>-73.881216</td>
      <td>1</td>
      <td>Drivers Killed</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2012-07-12</td>
      <td>0:57</td>
      <td>BRONX</td>
      <td>40.843007</td>
      <td>-73.848547</td>
      <td>1</td>
      <td>Drivers Killed</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2012-07-16</td>
      <td>10:35</td>
      <td>BROOKLYN</td>
      <td>40.609674</td>
      <td>-73.965223</td>
      <td>1</td>
      <td>Drivers Killed</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2012-07-16</td>
      <td>23:17</td>
      <td>BRONX</td>
      <td>40.878645</td>
      <td>-73.871598</td>
      <td>1</td>
      <td>Drivers Killed</td>
    </tr>
  </tbody>
</table>
</div>




```python
frames = [car_killed, cyc_killed, ped_killed]
full = pd.concat(frames)
full = full.reset_index(drop=True)
full.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>BOROUGH</th>
      <th>Cyclists Killed</th>
      <th>DATE</th>
      <th>LATITUDE</th>
      <th>LONGITUDE</th>
      <th>Motorists Killed</th>
      <th>Pedestrians Killed</th>
      <th>TIME</th>
      <th>Type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>BROOKLYN</td>
      <td>NaN</td>
      <td>2012-07-02</td>
      <td>40.685846</td>
      <td>-73.947310</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>16:30</td>
      <td>Drivers Killed</td>
    </tr>
    <tr>
      <th>1</th>
      <td>QUEENS</td>
      <td>NaN</td>
      <td>2012-07-03</td>
      <td>40.764014</td>
      <td>-73.881216</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>20:55</td>
      <td>Drivers Killed</td>
    </tr>
    <tr>
      <th>2</th>
      <td>BRONX</td>
      <td>NaN</td>
      <td>2012-07-12</td>
      <td>40.843007</td>
      <td>-73.848547</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>0:57</td>
      <td>Drivers Killed</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BROOKLYN</td>
      <td>NaN</td>
      <td>2012-07-16</td>
      <td>40.609674</td>
      <td>-73.965223</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>10:35</td>
      <td>Drivers Killed</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BRONX</td>
      <td>NaN</td>
      <td>2012-07-16</td>
      <td>40.878645</td>
      <td>-73.871598</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>23:17</td>
      <td>Drivers Killed</td>
    </tr>
  </tbody>
</table>
</div>




```python
totalDeaths = []

for i in range(0,len(full)):
    if full['Motorists Killed'][i] > 0:
        totalDeaths.append(full['Motorists Killed'][i])
    elif full['Cyclists Killed'][i] > 0:
        totalDeaths.append(full['Cyclists Killed'][i])
    elif full['Pedestrians Killed'][i] > 0:
        totalDeaths.append(full['Pedestrians Killed'][i])
        
full['Total Deaths'] = totalDeaths
full.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>BOROUGH</th>
      <th>Cyclists Killed</th>
      <th>DATE</th>
      <th>LATITUDE</th>
      <th>LONGITUDE</th>
      <th>Motorists Killed</th>
      <th>Pedestrians Killed</th>
      <th>TIME</th>
      <th>Type</th>
      <th>Total Deaths</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>BROOKLYN</td>
      <td>NaN</td>
      <td>2012-07-02</td>
      <td>40.685846</td>
      <td>-73.947310</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>16:30</td>
      <td>Drivers Killed</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>QUEENS</td>
      <td>NaN</td>
      <td>2012-07-03</td>
      <td>40.764014</td>
      <td>-73.881216</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>20:55</td>
      <td>Drivers Killed</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>BRONX</td>
      <td>NaN</td>
      <td>2012-07-12</td>
      <td>40.843007</td>
      <td>-73.848547</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>0:57</td>
      <td>Drivers Killed</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BROOKLYN</td>
      <td>NaN</td>
      <td>2012-07-16</td>
      <td>40.609674</td>
      <td>-73.965223</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>10:35</td>
      <td>Drivers Killed</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BRONX</td>
      <td>NaN</td>
      <td>2012-07-16</td>
      <td>40.878645</td>
      <td>-73.871598</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>23:17</td>
      <td>Drivers Killed</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
full.to_csv('Traffic Fatalities by Type.csv')
```

