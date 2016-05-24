---
layout: post
title:  "What Percentage of Police Officers Live in their Cities?"
date:   2016-05-24 11:15:14 -0400
categories: data
---
Most police officers don't live in the cities they serve.

I was intrigued by Nate Silver's fantastic post on his blog FiveThirtyEight titled [*Most Police Don’t Live In The Cities They Serve*](http://fivethirtyeight.com/datalab/most-police-dont-live-in-the-cities-they-serve/).

Silver writes:

> In Ferguson, Missouri, where protests continue following the shooting of a teenager by a police officer this month, more than two-thirds of the civilian population is black. Only 11 percent of [the police force is](http://www.politifact.com/punditfact/statements/2014/aug/17/andrea-mitchell/ferguson-police-department-has-50-white-officers-t/). The racial disparity is troubling enough on its own, but it’s also suggestive of another type of misrepresentation. Given Ferguson’s racial gap, it’s likely that many of its police officers live outside city limits.

This metric is heavily dependent on race. Black and Hispanic police officers are more likely to reside in the cities they serve than white officers.

This data comes from the U.S. Equal Employment Opportunity Commission (EEOC) and the Census Bureau, which together provide detail on the racial composition of government workers in large American cities. We were alerted to the data set by a [Washington Post analysis](https://www.washingtonpost.com/news/wonk/wp/2014/08/14/where-minority-communities-still-have-overwhelmingly-white-police/) of the racial demographics of each city’s police force. The census data also includes detail on how many police officers live in the cities where they serve.

Silver visualizes this metric by ethnicity for each city in the census in the below figure. I think this is a very effective visualization because it answers several important questions in one clear, aesthetically-appealing, self-explantory scatter plot.

![fig]({{ site.url }}/assets/2016-05-24-fig1.png)

The data behind this plot can be found on [Github](https://github.com/fivethirtyeight/data/tree/master/police-locals). Let's see if we can recreate this plot.

## About the data

Police residence data

The raw data behind the story, [Most Police Don’t Live In The Cities They Serve](http://fivethirtyeight.com/datalab/most-police-dont-live-in-the-cities-they-serve/)

Includes the cities with the 75 largest police forces, with the exception of Honolulu for which data is not available. All calculations are based on data from the U.S. Census.

The Census Bureau numbers are potentially going to differ from other counts for three reasons:

The census category for police officers also includes sheriffs, transit police and others who might not be under the same jurisdiction as a city’s police department proper. The census category won’t include private security officers.
The census data is estimated from 2006 to 2010; police forces may have changed in size since then.
There is always a margin of error in census numbers; they are estimates, not complete counts.

Data definitions:

* city:               U.S. city
* police_force_size:  Number of police officers serving that city
* all:              Percentage of the total police force that lives in the city
* white:              Percentage of white (non-Hispanic) police officers who live in the city
* non-white:          Percentage of non-white police officers who live in the city
* black:              Percentage of black police officers who live in the city
* hispanic:         Percentage of Hispanic police officers who live in the city
* asian:              Percentage of Asian police officers who live in the city

Note: When a cell contains '**' it means that there are fewer than 100 police officers of that race serving that city.

## Prepare the environment

```python
from IPython.display import Image
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import statsmodels.api as sm
from statsmodels.nonparametric.kde import KDEUnivariate
from statsmodels.nonparametric import smoothers_lowess
from pandas import Series, DataFrame

plt.style.use('fivethirtyeight')
%matplotlib inline
```

## Load in the data

```python
url = "https://raw.githubusercontent.com/fivethirtyeight/data/master/police-locals/police-locals.csv"
df = pd.read_csv(url)
df.head()
```
![fig]({{ site.url }}/assets/2016-05-24-fig2.png)

## Visualize the data

```python
# Create figure using matplotlib
fig, ax = plt.subplots(figsize=(10, 25))

# Set the title
ax.set_title("Percentage of Police Officers that Live in the City",fontsize=14,y=1.03)
# Set the axes labels
ax.set_xlabel("Percentage",fontsize=12)
ax.set_ylabel("City",fontsize=12)
# Make the grid lighter
ax.grid(True,linestyle='-',color='0.9')
# Set the axes limits
ax.set_ylim([-1,len(df['city'])])
ax.set_xlim(0,1)
# Set y axis ticks to be the sorted list of cities
plt.yticks(range(len(df['city'])), df['city'])
# Set x axis labels on both bottom and top
ax.tick_params(labelbottom='on',labeltop='on')
# Set alpha so the scatter dots are a bit faded
alpha=0.7

# Plot the 'Total' data
total = ax.scatter(df['all'], range(len(df['all'])), color='black', marker='o', alpha=alpha)

# Plot the 'White' data
white = ax.scatter(df['white'], range(len(df['white'])), marker='o', alpha=alpha)

# Plot the 'Nonwhite' data
nonwhite = ax.scatter(df['non-white'], range(len(df['non-white'])), color='green', marker='o', alpha=alpha)

# Create legend
plt.legend([total, white, nonwhite], ['TOTAL', 'WHITE', 'NONWHITE'],
          bbox_to_anchor=(0., 1.02, 1., .102), loc=3, mode="expand", ncol=3,
           borderaxespad=-0.1, fontsize=10)

fig.tight_layout()

# Save figure
fig.savefig('police.png', dpi=600)
```

![fig]({{ site.url }}/assets/2016-05-24-fig3.png)

Not quite as pretty, but we're getting somewhere!

