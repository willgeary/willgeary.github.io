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
Download the csv directly from the NYC Open Data site and load it into a dataframe.

{% highlight python %}
url = 'https://data.cityofnewyork.us/api/views/hyij-8hr7/rows.csv?accessType=DOWNLOAD'
raw_data = pd.read_csv(url)
{% endhighlight %}


[iquantny-post]: http://iquantny.tumblr.com/post/142278062424/in-nyc-more-robberies-happen-right-when-school
[nypd-felony-data]: https://data.cityofnewyork.us/Public-Safety/NYPD-7-Major-Felony-Incidents/hyij-8hr7
[jupyter-notebook]: https://nbviewer.jupyter.org/github/willgeary/personalblog/blob/master/NYC%20Crime%20Rates.ipynb


