---
title: "Tie Analysis"
layout: post
date: 2016-01-23 22:10
tag: Data Analysis
image: https://koppl.in/indigo/assets/images/jekyll-logo-light-solid.png
headerImage: true
projects: true
hidden: true # don't count this post in blog pagination
description: "A simple data analysis project"
category: project
author: Akhil
externalLink: false
---

![Screenshot](https://raw.githubusercontent.com/sergiokopplin/indigo/gh-pages/assets/screen-shot.png)

Hi folks! Let's figure out how we can plot a simple histogram analyzing the price of Gucci ties available in a reasonably big data sample (around 5000 rec).
Before we proceed, let's download 'data.csv' from [Akhil's Github Repo](https://github.com/akhil-sreehari/BasicDataAnalysis)

Let's fire up the jupyter notebook or pycharm or any editor you like.

{% highlight js %}
//We will be the using the following 3 imports

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

{% endhighlight %}

Okay. This seems fairly straightforward! We've imported the essentials. What to do next?

Creating a dataframe raw_data from the csv file available to us.
{% highlight js %}

raw_data = pd.read_csv('data.csv', encoding='utf-8',skiprows=1, delimiter='\t', names=['id', 'price', 'name',
                                                                           'brand', 'brand_name',
                                                                           'image', 'desc', 'vendor',
                                                                           'striped', 'material'])
{% endhighlight %}                                                                           
Just to get a feel of things

{% highlight js %}
type(raw_data)
raw_data.iloc[1:2]

//Finding the total price of all the ties available to us (without using innate df method - by iterating the df)
data_list = []
for i, v in raw_data.iterrows():
    data_list.append(v)
data_list

prices = []
for data in data_list:
    prices.append(data[1])
prices_total = float( '%.2f' % sum(prices))
{% endhighlight %}  

Let's fing the average, min and max now!

{% highlight js %}
float('%.2f' % (prices_total/ len(prices)))
min(prices)
max(prices)
{% endhighlight %}  

Adding a new field bool_field with a condition, i.e, brand > 1000
{% highlight js %}
raw_data.loc[raw_data.brand > 1000, 'bool_field'] = False
raw_data.iloc[:,2:]

//Fill all False values with the string unknown
raw_data = raw_data.replace(False, 'unknown')
{% endhighlight %}

Find DF with Gucci brand_name and average

{% highlight js %}
len(raw_data.loc[raw_data.brand_name.str.contains('Gucci')])
gucci_dataframe = raw_data.loc[raw_data.brand_name.str.contains('Gucci')]

gucci_sum = 0
for k, v in gucci_dataframe[['price']].iterrows():
    gucci_sum += v
gucci_sum/ len(gucci_dataframe[['price']])
{% endhighlight %}

Export result to a csv and excel file

{% highlight js %}
gucci_dataframe.to_csv('exported_from_gucci_dataframe.csv', sep='\t', encoding='utf-8')

gucci_dataframe.to_excel('exported_from_gucci_dataframe.xls')
{% endhighlight %}

Plotting a line chart for a better idea about the gucci tie prices
{% highlight js %}
plt.plot(gucci_dataframe['price'].tolist())
plt.xlabel('Price level')
plt.ylabel('Number of ties')
plt.show()
{% endhighlight %}

![placeholder](https://github.com/akhil-sreehari/BasicDataAnalysis/raw/master/linechart.png)

Plotting a hist chart
{% highlight js %}
plt.hist(gucci_dataframe['price'].tolist())
plt.xlabel('Price level')
plt.ylabel('Number of ties')
plt.show()   
{% endhighlight %}
![placeholder](https://github.com/akhil-sreehari/BasicDataAnalysis/raw/master/histchart.png)



---

[Check it out](https://github.com/akhil-sreehari/BasicDataAnalysis) here.
If you need some help, just [tell me](https://github.com/akhil-sreehari/BasicDataAnalysis/issues).
