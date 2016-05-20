---
layout: post
title:  "Causes of death in New York City"
date:   2016-05-19 17:15:14 -0400
categories: data
---
The leading causes of death by sex and ethnicity in New York City in since 2007.

The Department of Health and Mental Hygiene (DOHMH) publishes its data on the leading causes of death in New York City from 2007 through 2011. You may view the data set on [NYC OpenData](https://data.cityofnewyork.us/Health/New-York-City-Leading-Causes-of-Death/jb7j-dtam). 

This is what it looks like:

![fig]({{ site.url }}/assets/2016-05-19-fig2.png)

Let's see what this data tells us about health and death in New York City.

**A few data requests for the DOHMH:**
* DATE: Please include the dates of each death, rather than simply the year.
*HOSPITAL: Please include the address of the hospital where each individual passed away or was taken.
*AGE: Please include the age of individual at their time of birth.

With data requested above, we may be able to better understand the geography of health in New York City.

Below, let's use Python and several of its popular libraries to load, clean and explore the data.

## Set up the environment
Import the necessary libraries.

{% highlight python %}
from datetime import datetime
from IPython.display import Image
import matplotlib.pyplot as plt
import matplotlib.dates as mdates
import matplotlib
import numpy as np
import os
import pandas as pd
import seaborn as sns
%matplotlib inline
{% endhighlight %}

Set some visual parameters.

{% highlight python %}
# Bigger font sizes for plots
matplotlib.rcParams.update({'font.size': 32})
matplotlib.rc('xtick', labelsize=16) 
matplotlib.rc('ytick', labelsize=12) 
matplotlib.rc('labelsize: large') 

# Do not truncate dataframe displays up to 500 columns
pd.set_option('display.max_columns', 500)
{% endhighlight %}

## Load the raw data

Download the csv directly from the NYC Open Data site and load it into a data frame.

{% highlight python %}
# Upload image
url = 'https://data.cityofnewyork.us/api/views/jb7j-dtam/rows.csv?accessType=DOWNLOAD'
raw_data = pd.read_csv(url)
{% endhighlight %}

Preview the DataFrame and see what dtypes its columns are.

{% highlight python %}
raw_data.head()
raw_data.dtypes
{% endhighlight %}

## Clean the data
Assign the raw_data to a new DataFrame for cleaning.

{% highlight python %}
df = raw_data
{% endhighlight %}

Convert columns to their appropriate dtypes. We'll want Ethnicity, Sex and Cause of Death to be categorical.
{% highlight python %}
# Convert categorical columns to categories
df['Ethnicity'] = df['Ethnicity'].astype('category')
df['Sex'] = df['Sex'].astype('category')
df['Cause of Death'] = df['Cause of Death'].astype('category')

# Convert Percent column to an actual percent
df['Percent'] = df['Percent']/100
{% endhighlight %}

Sort the data chronologically.
{% highlight python %}
# Sort by year
df.sort_values(by='Year', inplace=True)

# Reset index
df.reset_index(drop=True, inplace=True)
{% endhighlight %}


Why do did fewer people die in 2011 than 2007?  
