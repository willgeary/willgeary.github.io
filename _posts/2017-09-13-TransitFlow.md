---
published: true
layout: post
date: '2017-09-30 02:42:14 -0400'
categories: data
publish: true
---
# TransitFlow — using Python and Processing to visualize regional transit frequency

Urban planner [Jarrett Walker](http://humantransit.org/2015/07/mega-explainer-the-ridership-recipe.html) emphasizes the importance of transit frequency:

> Transit frequency is the elapsed time between consecutive buses (or trains, or ferries) on a line, which determines the maximum waiting time. People who are used to getting around by a private vehicle (car or bike) often underestimate the importance of frequency, because there isn’t an equivalent to it in their experience. A private vehicle is ready to go when you are, but transit is not going until it comes. High frequency means transit is coming soon, which means that it approximates the feeling of liberty you have with your private vehicle – that you can go anytime. Frequency is freedom!

This makes intuitive sense. Transit networks with higher frequency and shorter waiting times will yield a more reliable and empowering experience for passengers than those with lower frequency and longer waiting times.

Analyzing transit frequency in an intuitive way, however, can be difficult. Static transit maps provide geographic context but do not give any information about frequency. Timetables provide information about frequency but can be overwhelming, unintuitive and lacking geographic context. Perhaps we can use spatial-temporal visualization to combine the spatial information of a static transit map with the temporal information of a timetable, and make it easer to think and talk about transit frequency!

This is the motivation behind [TransitFlow](https://github.com/transitland/transitland-processing-animation), an experimental set of tools to generate spatial-temporal transit frequency datasets and visualizations from the command line.

![]({{site.baseurl}}/https://s3.amazonaws.com/mapzen-assets/images/transitflow/table_happy.png)
*Mmmm… timetables. Provided by the San Francisco Municipal Transportation Agency.*

TransitFlow consists of two parts:

1. A few python scripts to retrieve and wrangle transit schedule data from [Transitland](https://transit.land/), an open-source data service sponsored by [Mapzen](https://mapzen.com/) that aggregates transit network data from around the world. Transitland enables us to merge transit schedules from different operators without having to worry about the granular details of each operator’s GTFS data.

2. A template to generate a [Processing](https://processing.org/) sketch to animate that data.

Let’s look at a few examples of what you can do with TransitFlow.

### Bay Area
`python transitflow.py --name=bay_area --bbox=-123.280334,37.011326,-120.607910,38.955137 --clip_to_bbox`

<iframe src="https://player.vimeo.com/video/226987064?quality=1080p&byline=0" width="640" height="640" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
<p><a href="https://vimeo.com/226987064">San Francisco Bay Area Transit Flows</a> from <a href="https://vimeo.com/willgeary">Will Geary</a> on <a href="https://vimeo.com">Vimeo</a>.</p>
