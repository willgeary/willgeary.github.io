---
layout: post
title:  "When does crime happen in New York City?"
date:   2016-05-18 17:15:14 -0400
categories: datascience
---
I was inspired by [this post][iquantny-post] on the fantastic blog I Quant NY. Author Ben Wellington analyzes the NYPD Major Felony Incidents [data set][nypd-felony-data] provided by NYC Open Data. The below analysis will recreate Ben's work using the Python programming language and even take it a bit further. 

You may view my complete notebook [here][jupyter-notebook].


## Set up the environment
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

## Load the raw data
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

## Clean the data
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


## What times of day do major felonies happen?
`groupby` Occurrence Hour and Offense. `count()` the number of rows.  
{% highlight python %}
offense_by_hour = df['Occurrence Date'].groupby([df['Occurrence Hour'], df['Offense']]).count().unstack()
offense_by_hour.head()
{% endhighlight %}

`plot` number of crimes grouped by Occurrence Hour and Offense type.
{% highlight python %}
offense_by_hour.plot(figsize=(10,8), title='Absolute Number of Crimes per Hour by Offense')
{% endhighlight %}
![fig1]({{ site.url }}/assets/2016-05-18-fig1.png)

Nice! We're starting to get somewhere. The chart above shows the absolute number of Crimes by hour over the 2006-2015 period. However, we want to see the *hourly crime rate* rather than the absolute number of crimes. So, we need to divide this the grouped data fram by the number of days in the time period to get the hourly crime rates that we want.

{% highlight python %}
# Calculate number days in 2006-2015
first_day = df['Occurrence Date'][2]
last_day = df['Occurrence Date'].max()
delta = last_day - first_day
days = delta.days-1
print "# of days in time period:", days
{% endhighlight %}

Convert the absolute number of crimes to hourly rates by dividing by the number of days in the period.

{% highlight python %}
crime_rate_hourly_by_offense = offense_by_hour.div(days)
crime_rate_hourly_by_offense.plot(figsize=(10,8), title='Hourly Felony Rate in NYC, 2006-2015');
{% endhighlight %}
![fig1]({{ site.url }}/assets/2016-05-18-fig1a.png)


This matches up nicely with Ben's chart:

![fig1]({{ site.url }}/assets/2016-05-18-fig2.png)

It appears that there are peaks around midnight and noon. There are also peaks around 8am and 3pm. This happens to be approximately when school starts and gets out in New York.

Is it possible that school getting out is responsible for higher crime rates? Let's dig deeper to find out.

Let's segment school days from non-school days to tease out the effect of school hours on crime rates. Specifically, categorize each day as a school day, summer vacation, weekday holiday or weekend day. To pinpoint the weekday holidays I referred to this NYC school calendar. 

PROBLEM: Only the 2015-2016 academic calendar is available here. So, I make the obviously flawed assumption that the academic recesses and holidays occurred on the same dates in 2006 through 2014 as they did in 2015. Ben also created his school 'vacation weekday' sub- data set. It's unclear exactly how he made it, so I'll make this assumption for now and move forward with the analysis and ask him :) .

## Do crime rates increase when school gets out?
In order to tease out crime trends on school days, I want to categorize each day from 2006 to 2015 as either a 1) School Day, 2) Weekday Holiday, 3) Weekend or 4) Summer Vacation.

In order to do so I'll need to insert a Date column into the dataframe that is simply a date (rather than a datetime stamp).

{% highlight python %}
# Calculate number days in 2006-2015
# Set datetime to simply date value (we may need this later)
df['Date'] = df['Occurrence Date'].dt.date
{% endhighlight %}

{% highlight python %}
# Create Month-Day column to store a (Month, Day) tuple in order to compare month-days
df['Month-Day'] = zip([date.date().month for date in df['Occurrence Date']],df['Occurrence Day'])
{% endhighlight %}

{% highlight python %}
# I made a CSV of school year holidays in 2015-2016 per http://schools.nyc.gov/Calendar/default.htm
holidays = pd.read_csv('C:/Users/Will/Desktop/2015_holidays.csv')
holidays['Date'] = pd.to_datetime(holidays['Date'])
holidays['Month'] = [date.date().month for date in holidays['Date']]
holidays['Day'] = [date.date().day for date in holidays['Date']]
holidays['Month-Day'] = zip(holidays.Month, holidays.Day)
holidays.head()
{% endhighlight %}

I created a csv of school year holidays in 2015-2016 per http://schools.nyc.gov/Calendar/default.htm
{% highlight python %}
holidays = pd.read_csv('C:/Users/Will/Desktop/2015_holidays.csv')
holidays['Date'] = pd.to_datetime(holidays['Date'])
holidays['Month'] = [date.date().month for date in holidays['Date']]
holidays['Day'] = [date.date().day for date in holidays['Date']]
holidays['Month-Day'] = zip(holidays.Month, holidays.Day)
holidays.head()
{% endhighlight %}

Indicate whether days are Summer, Weekend, Weekday Holiday or School Days using 1's and 0's.
{% highlight python %}
# Set summer vacation = 1 where month is July, August or September (and before Sept 9)
df['Summer Vacation'] = np.where((df['Occurrence Month'] == 'Jul') | (df['Occurrence Month'] =='Aug')
                                 | ((df['Occurrence Month'] == 'Sep') & (df['Occurrence Day'] < 9)),1,0)

# Set weekend day = 1 where day is Saturday or Sunday
df['Weekend Day'] = np.where(((df['Day of Week'] == 'Saturday') | (df['Day of Week'] == 'Sunday')),1,0)

# Set weekday holiday = 1 if the month-day was a holiday in 2015. This assumes month-day holidays will be same in other years
df['Weekday Holiday'] = [1 if monthday in list(holidays['Month-Day']) else 0 for monthday in df['Month-Day']]

# Set school day = 1 if none of the previous three new columns are = 1
df['School Day'] = np.where((df['Summer Vacation'] != 1) & (df['Weekend Day'] != 1) & (df['Weekday Holiday'] !=1),1,0)
{% endhighlight %}

Set Day Type equal to the correct type of day.
{% highlight python %}
def day_type(row):
    if row['Summer Vacation'] == 1:
        return 'Summer Vacation'
    if row['Weekend Day'] == 1:
        return 'Weekend Day'
    if row['Weekday Holiday'] == 1:
        return 'Weekday Holiday'
    if row['School Day'] == 1:
        return 'School Day'

df['Day Type'] = df.apply(lambda row: day_type(row), axis=1)

# Convert Day Type column to categorical
df['Day Type'] = df['Day Type'].astype('category')
{% endhighlight %}

Plot Hourly Felony Rate in NYC by day type.

{% highlight python %}
crimes_by_day_type = df['Occurrence Date'].groupby([df['Occurrence Hour'], df['Day Type']]).count().unstack()
crime_rate_hourly_by_day_type = crimes_by_day_type.div(days)
crime_rate_hourly_by_day_type.plot(figsize=(10,8), title='Hourly Felony Rate in NYC is Higher on School Days');
{% endhighlight %}

![fig]({{ site.url }}/assets/2016-05-18-fig3.png)

You can see that the overall level of felonies is much higher on school days than holiday or weekend days. Let's dive deeper and segment this analysis across the various types of offenses. We'll want to calculate two things:

1. Number of crimes
2. Rate of crimes

And segment them across three attributes:

1. Day Type: school day, summer vacation, weekday holiday, weekend day
2. Offense Type: burglary, felony assault, grand larceny, grand larceny of motor vehicle, murder, rape and robbery
3. Occurrence Hour: hourly from 12am through 11pm

If we were working in Excel, this would be pretty time intensive. Thankfully Python's groupby and aggregate functions can take care of this pretty easily. Below we define an aggregation to count the number of occurrences. Then, we group the data frame by Offense, Occurrence Hour and Day Type and apply the aggregation. Finally, we divide the 'hourly_rate' aggregation by the appropriate number of  days for each day type to convert crime counts into crime rates.

{% highlight python %}
# Define the aggregation calculations
aggregations = {
    'OBJECTID': { # work on the "OBJECT ID" column
        'num_occurrences': 'count',  # get the count, and call this result 'num_occurences'
        'hourly_rate': 'count'} # placeholder for now - we will divide this by number of days next
    }
 
# Perform groupby aggregation by occurrence hour and day type
g = df.groupby([df['Offense'].astype('str'), df['Occurrence Hour'], df['Day Type']]).agg(aggregations).unstack()

# Divide the hourly_rate columns by the appropriate number of days to convert them into hourly rates
g[('OBJECTID', 'hourly_rate', 'School Day')] = g[('OBJECTID', 'hourly_rate', 'School Day')] / school_days
g[('OBJECTID', 'hourly_rate', 'Summer Vacation')] = g[('OBJECTID', 'hourly_rate', 'Summer Vacation')] / summer_days
g[('OBJECTID', 'hourly_rate', 'Weekday Holiday')] = g[('OBJECTID', 'hourly_rate', 'Weekday Holiday')] / weekday_holidays
g[('OBJECTID', 'hourly_rate', 'Weekend Day')] = g[('OBJECTID', 'hourly_rate', 'Weekend Day')] / weekend_days
g.head()
{% endhighlight %}

![fig]({{ site.url }}/assets/2016-05-18-fig4.png)

{% highlight python %}
for x in np.unique(g.index.get_level_values(0)):
    print x
{% endhighlight %}

![fig]({{ site.url }}/assets/2016-05-18-fig5.png)

Make a plot for each type of offense.

{% highlight python %}
for x in np.unique(g.index.get_level_values(0)):
    ax = plt.figure()
    g.loc[x,('OBJECTID', 'hourly_rate')].plot(figsize=(16,8), title=x.lower() + ' Hourly Rates in NY, 2006-2015')
    hours = ['12AM', 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, '12PM', 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11]
    plt.xticks(range(len(hours)), hours)
    plt.ylabel('Number of Occurrences')
    plt.xlabel('Time of Day')
{% endhighlight %}

### Robbery peaks on school days at 3pm, right when school gets out.
![fig]({{ site.url }}/assets/2016-05-18-fig6.png)

### Burglary peaks on school days at 8am, before school starts.
![fig]({{ site.url }}/assets/2016-05-18-fig7.png)

### Felony assault peaks on weekend days at 3am.
![fig]({{ site.url }}/assets/2016-05-18-fig8.png)

### Grand larceny tends to be higher on school days, with peaks at 8am, 12pm and 3pm.
![fig]({{ site.url }}/assets/2016-05-18-fig9.png)

### Motor vehicle theft peaks at 10pm on summer nights.
![fig]({{ site.url }}/assets/2016-05-18-fig10.png)

### Murder peaks at 4am on weekends.
![fig]({{ site.url }}/assets/2016-05-18-fig11.png)

### Rape peaks at midnight.
![fig]({{ site.url }}/assets/2016-05-18-fig12.png)

Some fascinating, albeit morbid, takeaways.

## Which crimes happen most frequently?
So far we've looked at the varies types of crimes individually, but how to they compare to one another? To answer this question, we will perform another **aggregate** / **groupby** function to count the number of occurrences by Day Type and Offense Type.

{% highlight python %}
# Define the aggregation calculations
aggregations = {
    'OBJECTID': { # work on the "OBJECT ID" column
        'num_occurrences': 'count'}  # get the count, and call this result 'num_occurences'
    }
 
# Perform groupby aggregation by occurrence hour and day type
c = df.groupby([df['Offense'].astype('str'), df['Day Type']]).agg(aggregations).unstack()

# Divide the num_occurrences columns by the appropriate number of days to convert them into daily rates
c[('OBJECTID', 'num_occurrences', 'School Day')] = c[('OBJECTID', 'num_occurrences', 'School Day')] / school_days
c[('OBJECTID', 'num_occurrences', 'Summer Vacation')] = c[('OBJECTID', 'num_occurrences', 'Summer Vacation')] / summer_days
c[('OBJECTID', 'num_occurrences', 'Weekday Holiday')] = c[('OBJECTID', 'num_occurrences', 'Weekday Holiday')] / weekday_holidays
c[('OBJECTID', 'num_occurrences', 'Weekend Day')] = c[('OBJECTID', 'num_occurrences', 'Weekend Day')] / weekend_days
c
{% endhighlight %}
![fig]({{ site.url }}/assets/2016-05-18-fig13.png)











[iquantny-post]: http://iquantny.tumblr.com/post/142278062424/in-nyc-more-robberies-happen-right-when-school
[nypd-felony-data]: https://data.cityofnewyork.us/Public-Safety/NYPD-7-Major-Felony-Incidents/hyij-8hr7
[jupyter-notebook]: https://nbviewer.jupyter.org/github/willgeary/personalblog/blob/master/NYC%20Crime%20Rates.ipynb


