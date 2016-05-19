---
layout: post
title:  "When does crime happen in New York City?"
date:   2016-05-18 17:15:14 -0400
categories: data science, recreate
---
I was inspired by [this post][iquantny-post] on the fantastic blog I Quant NY. Author Ben Wellington analyzes the NYPD Major Felony Incidents [data set][nypd-felony-data] provided by NYC Open Data. The below analysis will recreate Ben's work using the Python programming language and even take it a bit further. 

You may view my complete notebook [here][jupyter-notebook].


### Set up the environment
Import the necessary libraries.

{% highlight python %}
from datetime import datetime
import matplotlib.pyplot as plt
import matplotlib.dates as mdates
import numpy as np
import os
import pandas as pd
import seaborn as sns
import time
%matplotlib inline
{% endhighlight %}

### Load the raw data
Download the csv directly from the NYC Open Data site and load it into a data frame.

{% highlight python %}
url = 'https://data.cityofnewyork.us/api/views/hyij-8hr7/rows.csv?accessType=DOWNLOAD'
raw_data = pd.read_csv(url)
{% endhighlight %}

Preview the data frame and see what `dtypes` the columns are.

{% highlight python %}
raw_data.head()
raw_data.dtypes
{% endhighlight %}

### Clean the data
Select years 2006 through 2015 into new data frame.

{% highlight python %}
df = raw_data[(raw_data['Occurrence Year'] >= 2006) & (raw_data['Occurrence Year'] <= 2015)].copy()
{% endhighlight %}

Convert columns in dataframe to the appropriate types.

{% highlight python %}
# Convert floats to integers
df['OBJECTID'] = df['OBJECTID'].fillna(0).astype(int)
df['Occurrence Day'] = df['Occurrence Day'].fillna(0).astype(int)
df['Occurrence Year'] = df['Occurrence Year'].fillna(0).astype(int)
df['Occurrence Hour'] = df['Occurrence Hour'].fillna(0).astype(int)
df['CompStat Month'] = df['CompStat Month'].fillna(0).astype(int)
df['CompStat Day'] = df['CompStat Day'].fillna(0).astype(int)
df['CompStat Year'] = df['CompStat Year'].fillna(0).astype(int)
df['Precinct'] = df['Precinct'].fillna(0).astype(int)

# Convert categorical columns to categories
df['Offense'] = df['Offense'].astype('category')
df['Offense Classification'] = df['Offense Classification'].astype('category')
df['Sector'] = df['Sector'].astype('category')
df['Precinct'] = df['Precinct'].astype('category')
df['Borough'] = df['Borough'].astype('category')

# Convert Occurence Date column to datetime
df['Occurrence Date'] = pd.to_datetime(df['Occurrence Date'])
df.dtypes
{% endhighlight %}

Sort data frame by `Occurrence Date` and then reset the index using `reset_index`
{% highlight python %}
df.sort('Occurrence Date', inplace=True)
df.reset_index(drop=True, inplace=True)
{% endhighlight %}


### What times of day do major felonies happen?
`groupby` Occurrence Hour and Offense. `count()` the number of rows 
{% highlight python %}
offense_by_hour = df['Occurrence Date'].groupby([df['Occurrence Hour'], df['Offense']]).count().unstack()
offense_by_hour.head()
{% endhighlight %}

`plot` number of crimes grouped by Occurrence Hour and Offense type.
![fig1]({{ site.url }}/assets/2016-05-18-fig1.png)



[iquantny-post]: http://iquantny.tumblr.com/post/142278062424/in-nyc-more-robberies-happen-right-when-school
[nypd-felony-data]: https://data.cityofnewyork.us/Public-Safety/NYPD-7-Major-Felony-Incidents/hyij-8hr7
[jupyter-notebook]: https://nbviewer.jupyter.org/github/willgeary/personalblog/blob/master/NYC%20Crime%20Rates.ipynb


