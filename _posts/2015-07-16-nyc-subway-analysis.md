---
layout: post
title: Do statistics lie? - a case-study on NYC's MTA traffic pattern!
---

I set out to study New York City's MTA (subway) turnstile data to estimate the volume of commuters going in and out of each subway station. There were quite a few interesting patterns in this exploratory analysis leading me to believe that 'data make us lie' and do not blame on statistical methods! Well, that's not something new to us.

Here, I limit my notes to a part of the analysis that tells us a confusing story based on prior common sense knowledge. Let us test our knowledge (hypothesis) on MTA data. 

If you would want to download this data in python, feel free to use my code.

{% highlight ruby %}
urls = 'http://web.mta.info/developers/data/nyct/turnstile/turnstile_%s.txt'
for date in ['150627', '150620', '150613', '150606']:
    url = urls % date
    !wget {url}
{% endhighlight %}

-- So, hypothesis is: In any part of the world, peak hours would mean 8am - 10am, and 4pm - 7pm.

But, what do you see from these graphs? What insights/patterns can you extract from these graphs?

Legend: X-axis --> hourly time slices for 3 days in June; Y-axis --> Total commuter volume (entries+exits) per station.


####Exhibit a:

Hourly commuter volume at World Trade Center station.
![world trade center station]({{hvsarma.github.io}}/images/wtc-plot.png)

####Exhibit b:
Hourly commuter volume at Wall Street station.
![wall street station]({{hvsarma.github.io}}/images/wallst-plot.png)

####Exhibit c:

Hourly commuter volume at Time Square station.
![time square station]({{hvsarma.github.io}}/images/timesq-plot.png)

####Exhibit d:

Hourly commuter volume at NYC Penn - 34st station.
![time square station]({{hvsarma.github.io}}/images/pennst34-plot.png)


###Obscure patterns - Don't believe in them!

All charts show a peak around 4pm. Would this mean, traffic is 'almost always' peak at 4pm? how about during morning hours?

Often, I have seen analysts jumping to conclusions, without looking at the data sources and its limitations.

1. MTA turnstiles data are recorded every hours (at least what is available to us).

2. If you look carefully, data from 00:00hr - 0400hr is grouped under 00:00hr bucket. Similarly, 16:00hr data includes 16:00 to 20:00.

###In conclusion,

though we find interesting patterns in the data, they only give us a vague idea of when most people are getting in/out of each station. In this case, this is pretty useless, as we already know what peak hours mean, in general.


P.S: Keen to know what code was used in plotting? Here it is:

{% highlight ruby %}
import matplotlib.pyplot as plt
import matplotlib
matplotlib.style.use('ggplot')
%matplotlib inline


def plot_hourly_stn_vol(df,stationname, date1, date2):
    hourly_df = df[stationname & date1 & date2]
    plt.figure(figsize=(18,5))
    plt.plot(hourly_df['DATE'], hourly_df['TOTAL_TRAFFIC'] ,linestyle='--', marker='o',color='b')
    plt.title('Hourly Commuter Volume at the Station: %s' stationname)
    plt.xticks(rotation=0)
    plt.xlabel('Hourly (June/22 - June/25, 2015)')
    plt.ylabel('# of commuters going in/out of the station')
    return plt
{% endhighlight %}






