---
layout: post
title: Diabetes Prediction using Machine Learning
date: 2017-04-27 22:10
tag: Data Analysis, Machine Learning, Statistics
image: https://koppl.in/indigo/assets/images/jekyll-logo-light-solid.png
headerImage: true
projects: true
hidden: true # don't count this post in blog pagination
description: "Predicting diabetes using Machine Learning"
category: project
author: Akhil
externalLink: false
---

Let's figure out how we can try and train an Algorithm model which can predict diabetes using a reasonably accurare PRIMA Data.
Before we proceed, let's download 'prima-data.csv' from [Akhil's Github Repo](https://github.com/akhil-sreehari/DiabetesPrediction)

Let's fire up the jupyter notebook or pycharm or any editor you like.


//We will be the using the following 3 imports

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

%matplotlib inline

Load and Review PIMA Data

df = pd.read_csv('pima-data.csv')
df.shape

df.head(6)

num_preg	glucose_conc	diastolic_bp	thickness	insulin	bmi	diab_pred	age	skin	diabetes
0	6	148	72	35	0	33.6	0.627	50	1.3790	True
1	1	85	66	29	0	26.6	0.351	31	1.1426	False
2	8	183	64	0	0	23.3	0.672	32	0.0000	True
3	1	89	66	23	94	28.1	0.167	21	0.9062	False
4	0	137	40	35	168	43.1	2.288	33	1.3790	True
5	5	116	74	0	0	25.6	0.201	30	0.0000	False


Check if any value is null

df.isnull().values.any()
False

Check for correlation

size = 11

corr = df.corr()
fig, ax = plt.subplots(figsize=(size,size))

ax.matshow(corr)

plt.xticks(range(len(corr.columns)), corr.columns)
plt.yticks(range(len(corr.columns)), corr.columns)

![placeholder](https://github.com/akhil-sreehari/DiabetesPrediction/raw/master/Screen%20Shot%202017-04-25%20at%204.13.30%20PM.png)

### Dark Red patch shows that skin and thickness are rather similar or correlated. Why would we want 2 things which essentially give us the same information?

Skin and Thickness are the same actually. Let us delete one.

df['skin'].head()

df['thickness'].head()

del df['skin']

df.head()

#### Let us now check the datatypes
Diabetes is in boolean. Let us change that to 1's and 0's

diabetes_map = {True:1, False:0}
df['diabetes'] = df['diabetes'].map(diabetes_map)
df.head()


num_preg	glucose_conc	diastolic_bp	thickness	insulin	bmi	diab_pred	age	diabetes
0	6	148	72	35	0	33.6	0.627	50	1
1	1	85	66	29	0	26.6	0.351	31	0
2	8	183	64	0	0	23.3	0.672	32	1
3	1	89	66	23	94	28.1	0.167	21	0
4	0	137	40	35	168	43.1	2.288	33	1


## Checking for representation
We need to make sure that each factors are represented in a noble amount. Like, if the number of false diabetes cases are too much, it doesn't make sense to use this data for accurately predicting a model!'

diab_true = len(df.loc[df['diabetes'] == 1])
diab_false = len(df.loc[df['diabetes'] == 0])

diab_false #False Well represented?
diab_true #True Well represented?

## We've prepared our data! Now we need an algorithm to train the data.
We could say that Algorithm is the engine that drives the process.

### Let us check 3 sample Algorthms
## Naive Bayes
1. Bayes Theorem
2. Based on likelihood and probability
3. Has an assumption - Each feature that we're passing are independent of each other and make equal impact.
4. In our case, this means that BP is as significant as bmi, age and others.
5. Needs small amount of Data to train.

1. Simple, easy to understand
2. Fast. (Other complex algorithms take 100x times the time)
3. Stable algorithm

## Logistic Regression

1. Returns a binary result
2. Measures the relationship of each features and weighs them based on their impact on the result.
3. Resultant value is mapped against a curve with 2 values, 1 and 0 - equivalent to diabetes or no diabetes in our case.

## Decision Tree
1. Binary Tree structure
2. Each Node contains decision. Each node decides if we want to go one way or another.
3. It requires significant amount of data to determine nodes and their splits.
4. Easy to follow along how train model works (because of visualization)


## How to decide on algorithms?

### Learning Type

1. Learning Type


We are trying to create a prediction model. So it's a *Supervised Algorithm*
2. Result Type


*Regression* (Continuous values) v/s *Classification* (discrete). We need discrete values (Diabetic or not?)

3. Complicated Algorithm


Ensemble(multiple/complex) can be avoided - difficult to comprehend, multiple child algorithms, difficult to debug


## Training Overview

Prepared data is split into two. One for Training and other for Test. Training data is passed onto a training algorithm which produces a train model based on the logic in the algorithm

**We train algorithm using training data and use test data to validate it. If we use test data also to train, the training algorithm would be familiar with this data and know it's biases. So we'd have a model which we think is accurate but might fail when we feed it with real world data with another set of biases.**


----------------------------------------------------------------------------------------------------------------------
### Splitting the Data
70% Training 30% Testing



from __future__ import division
from sklearn.cross_validation import train_test_split

featured_col_names = ['num_preg', 'glucose_conc', 'diastolic_bp', 'thickness', 'insulin', 'bmi', 'diab_pred', 'age']
predicted_class_names = ['diabetes']


x = df[featured_col_names].values #Predictor containing feature columns
y = df[predicted_col_names].values #Predicted class column

split_test_size = 0.30 #Give split as 30% for test

x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=split_test_size, random_state=666)

len(y_train[:])

print len(df.loc[df['glucose_conc'] == 0])
print len(df.loc[df['diastolic_bp'] == 0])

5
35

## Post split data modification

Wow. Seems like there are lots of hidden 0 values here! What to do now?
We shall Do **imputing**

### Imputing Options
1. Replace with Mean, Median
2. Replace with expert knowledge derived value

### Using Mean Imputing

from sklearn.preprocessing import Imputer

fill0 = Imputer(missing_values=0, strategy="mean", axis=0) # axis=0 means column

x_train = fill0.fit_transform(x_train)
x_test = fill0.fit_transform(x_test)


## Training Algorithm using Naive Bayes
from sklearn.naive_bayes import GaussianNB

#create Gaussian Naive Bayes model object and train it with Data
nb_model = GaussianNB()

nb_model.fit(x_train, y_train.ravel())


## Checking Accuracy - Performance on training Data
#Use training data to predict the model
nb_predict_train = nb_model.predict(x_train)

from sklearn import metrics # performance metrics library

metrics.accuracy_score(y_train, nb_predict_train) #Accuracy

## Checking Accuracy - Performance on testing Data

#Use testing data to predict the model
nb_predict_test = nb_model.predict(x_test)

from sklearn import metrics # performance metrics library

metrics.accuracy_score(y_test, nb_predict_test) #Accuracy
