---
layout: post
title: A 'linear-regression' approach to predicting strike rate in cricket...
---

In the sport of cricket, a batting strike rate is a measure of how frequently a batsman achieves scoring runs. The higher the strike rate, the more effective a batsman is at scoring quickly.

I am making a religious attempt to find relationship between a batsman's strike rate and other stats available. Data is sourced from ESPN cricinfo, and have been wrangled, and grokked for demonstrating a linear relationship. Though admittedly, there exist other ('efficient') methods of analysis, I applied my python skills to do regression concepts. 

[LINK to my analysis in detail is here.](https://github.com/hvsarma/Cricket-Prediction/blob/master/Luther-presentation.pdf)

![Modeling flow]({{hvsarma.github.io}}/images/cricppt.jpg)

You may also tweak my code below.

###1. Import relevant modules. I use a few statsmodel functions here. You may want to try with Scikit learn modules as well.

{% highlight ruby %}
# Scraping and loading 
from __future__ import print_function
import csv
import lxml.html

# Dataframe handling
import pandas as pd
import numpy as np

# Regression and other stats modules
from patsy import dmatrices
from scipy import stats
import statsmodels.api as sm
from statsmodels.sandbox.regression.predstd import wls_prediction_std
from statsmodels.iolib.table import (SimpleTable, default_txt_fmt)
from sklearn import 

# Plotting
import matplotlib.pyplot as plt
%matplotlib inline
import seaborn as sns
from statsmodels.graphics.regressionplots import plot_leverage_resid2
from statsmodels import graphics

# Random Seed Generator to make this 'world' still.
np.random.seed(1024)
{% endhighlight %}

###2. Scrape from ESPN page using lxml. Be respectful and do not hack in.

{% highlight ruby %}
url1 = 'http://stats.espncricinfo.com/ci/engine/stats/index.html?class=2;filter=advanced;orderby=matches;'

out = csv.writer(open('ODI_batting.csv','wb',))
out.writerow(('Player','Matches','Inns','NO','Runs','HS','Ave','BF','SR','Centuries','Fifties', 'Ducks', 'Season'))

for page in url5:
    Player = []
    Matches = []
    Inns = []
    NO = []
    Runs = []
    HS = []
    Ave=[]
    BF=[]
    SR = []
    Centuries = []
    Fifties = []
    Ducks = []
    Season = []
    
    content = lxml.html.parse(page)
    
    Player.extend(content.xpath('//tr[@class="data1"]/td[1]/a/text()'))
    Matches.extend(content.xpath('//tr[@class="data1"]/td[2]/b/text()'))
    Inns.extend(content.xpath('//tr[@class="data1"]/td[3]/text()'))
    NO.extend(content.xpath('//tr[@class="data1"]/td[4]/text()'))
    Runs.extend(content.xpath('//tr[@class="data1"]/td[5]/text()'))
    HS.extend(content.xpath('//tr[@class="data1"]/td[6]/text()'))
    Ave.extend(content.xpath('//tr[@class="data1"]/td[7]/text()'))
    BF.extend(content.xpath('//tr[@class="data1"]/td[8]/text()'))
    SR.extend(content.xpath('//tr[@class="data1"]/td[9]/text()'))
    Centuries.extend(content.xpath('//tr[@class="data1"]/td[10]/text()'))
    Fifties.extend(content.xpath('//tr[@class="data1"]/td[11]/text()'))
    Ducks.extend(content.xpath('//tr[@class="data1"]/td[12]/text()'))
    Season.extend(content.xpath('//tr[@class="data1"]/td[13]/text()'))
    
    # This part of code may not be needed
    zipped = zip(Player, Matches, Inns, NO, Runs, HS, Ave, BF, SR, Centuries, Fifties, Ducks, Season)
    for row in zipped:
        out.writerow(row)
        zipped = None
{% endhighlight %}

###3. Read from the file, after a bunch of cleaning on the dataset.

{% highlight ruby %}
# Read in to a Pandas dataframe
pl_bat_df = pd.read_csv(r"ODI_batting_cleaned.csv")

# Filter dataset to last 7 years of data, with StrikeRate <150.
from scipy import stats

pl_bat_df['AvgRunRate'] = pl_bat_df.TotalRuns / pl_bat_df.Matches / 50
filtered_df = pl_bat_df[(pl_bat_df.StrikeRate < 150)]
filtered_df = filtered_df[(filtered_df.SeasonYear > 2008)]

filtered_df['AvgRunRate'] = pl_bat_df.TotalRuns / pl_bat_df.Matches / 50
{% endhighlight %}

###4. Dmatrix in Patsy is a pretty cool option to set a regression formula.

{% highlight ruby %}
filtered_df = filtered_df.iloc[np.random.permutation(len(filtered_df))]
y_all, X_all = dmatrices('StrikeRate ~ BattingAvg*AvgRunRate+HighestScore*Matches'\
                 ,data=filtered_df, return_type='dataframe')
{% endhighlight %}

###5. Run Regression on X and Y.

{% highlight ruby %}
from sklearn import datasets, linear_model
from sklearn import cross_validation

filtered_df = filtered_df.iloc[np.random.permutation(len(filtered_df))]
linear_reg_model = linear_model.LinearRegression()
rsq_scores = cross_validation.cross_val_score(linear_reg_model, X_all, y_all, n_jobs=3,cv=5)
rsq_scores
{% endhighlight %}

###6. Look at my regression plane over two X variable against StrikeRate.

{% highlight ruby %}
from mpl_toolkits.mplot3d import Axes3D

def linear_model3D(df_adv):
    X = df_adv[['AvgRunRate', 'BattingAvg']]
    y = df_adv['StrikeRate']

    ## fit a OLS model with intercept on TV and Radio
    X = sm.add_constant(X)
    est = sm.OLS(y, X).fit()

    ## Create the 3d plot -- skip reading this
    # TV/Radio grid for 3d plot
    xx1, xx2 = np.meshgrid(np.linspace(X.AvgRunRate.min(), X.AvgRunRate.max(), 100), 
                           np.linspace(X.BattingAvg.min(), X.BattingAvg.max(), 100))
    # plot the hyperplane by evaluating the parameters on the grid
    Z = est.params[0] + est.params[1] * xx1 + est.params[2] * xx2

    # create matplotlib 3d axes
    fig = plt.figure(figsize=(12, 8))
    ax = Axes3D(fig, azim=-115, elev=15)

    # plot hyperplane
    surf = ax.plot_surface(xx1, xx2, Z, cmap=plt.cm.RdBu_r, alpha=0.6, linewidth=0)

    # plot data points - points over the HP are white, points below are black
    resid = y - est.predict(X)
    ax.scatter(X[resid >= 0].AvgRunRate, X[resid >= 0].BattingAvg, y[resid >= 0], color='black', alpha=1.0, facecolor='white')
    ax.scatter(X[resid < 0].AvgRunRate, X[resid < 0].BattingAvg, y[resid < 0], color='black', alpha=1.0)

    # set axis labels
    ax.set_xlabel('AvgRunRate')
    ax.set_ylabel('BattingAvg')
    ax.set_zlabel('StrikeRate')
{% endhighlight %}


