---
published: true
layout: post
date: '2017-04-07 04:15:14 -0400'
categories: data
---
<iframe src="https://player.vimeo.com/video/212484620?byline=0&portrait=0" width="640" height="360" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

-------

**Data sources:**

1) [Taxi trips](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml)

2) [Citibike trips](https://www.citibikenyc.com/system-data)

3) [Subway, Bus, Metro-North](http://web.mta.info/developers/developer-data-terms.html#data)

4) [Amtrak](http://www.gtfs-data-exchange.com/agency/amtrak/)

5) [PATH Train](http://data.trilliumtransit.com/gtfs/path-nj-us/)

6) [Staten Island Ferry](https://data.cityofnewyork.us/Transportation/Staten-Island-Ferry-Schedule-GTFS-/mwxp-krtu)

7) [NY Waterways Ferries](https://github.com/BetaNYC/NY-Waterways-GTFS-data)


**Methodology:**

I followed Todd Schneider's excellent [documentation](https://github.com/toddwschneider/nyc-taxi-data) to download the NYC Taxi dataset and load it into a PostgreSQL database with PostGIS extension. I wrote a python script to download and manipulate all of the GTFS data into a format necessary for visualization (to be shared on Github soon). Once all the data was in a consistent format, I used [Processing](processing.org) and [Unfolding Maps](http://unfoldingmaps.org/) to create the animation, making use of tutorials and examples generously published by [Juan Francisco Saldarriaga](https://github.com/juanfrans-courses/DataScienceSocietyWorkshop) of Columbia University's *Center for Spatial Research* and [Till Nagel](http://unfoldingmaps.org/tutorials/), one of the creators of Unfolding Maps.
