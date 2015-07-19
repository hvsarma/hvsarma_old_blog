---
layout: post
title: Do statistics lie? - a case-study on NYC's MTA traffic pattern!
---

I set out to study New York City's MTA (subway) turnstile data to estimate the volume of commuters going in and out of each subway station. There were quite a few interesting patterns in this exploratory analysis leading me to believe that 'data make us lie' and do not blame on statistical methods! Well, that's not something new to us.

Here, I limit my notes to a part of the analysis that tells us a confusing story based on prior common sense knowledge. Let us test our knowledge (hypothesis) on MTA data. 

If you would want to download this data in python, feel free to use my code.

`urls = 'http://web.mta.info/developers/data/nyct/turnstile/turnstile_%s.txt'
for date in ['150627', '150620', '150613', '150606']:
    url = urls % date
    !wget {url}
 `

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


###Obscure patterns - Don't believe in it!

All charts show a peak around 4pm. Would this mean, traffic is 'almost always' peak at 4pm? how about during morning hours? Certainly NOT! 

Often, I have seen analysts jumping to conclusions, without looking at the data sources and its limitations.

