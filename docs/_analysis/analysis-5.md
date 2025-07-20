---
layout: single
title:  "Singapore Retail Sales Forecasting Project (Python, ML, Tableau) "
date:   2025-6-03
category: analysis
author_profile: true
toc: true
toc_label: "Table of Contents"
toc_icon: "file"
toc_sticky: true
order: 5
#classes: wide
---

Date Posted: 2025-06-03

Category: [Data Projects](https://meng-kiat.github.io/analysis/){: .btn .btn--info .btn--small}

# Project Objectives

This project aims to predict monthly retail sales (in million SGD) in Singapore using machine learning. The goal is to provide accurate and interpretable forecasts that could help businesses and policymakers anticipate market demand, allocate inventory more effectively, and make data-driven decisions.

Specific objectives include: 
1. Feature Engineering: Exploring adding various time-step features and exogenous variables for machine learning model
2. Prediction & Accuracy: Evaluate the efficacy of using machine learning in forecasting retail sales in SG, and create predictions along with visualizations for simple & effective presentation
3. Visualization & Communication: Present insights and forecasts through dashboards using Tableau

The full code can be found below:

[View Notebook](){: .btn .btn--info .btn--small}

# Dataset

In this analysis, I used the [SingStat Table Builder](https://tablebuilder.singstat.gov.sg/table/TS/M601741) from Department of Statistics, Singapore (DOS) to retrieve 29 years of monthly retail sales data for Singapore from 1997 to 2025. 

## Preprocessing the Dataset
### Outlier Analysis

COVID-19 caused heavy drop in retail sales. We created an indicator feature ['is_covid'] for the machine learning algorithm to identify the COVID-19 period as outliers, ensuring that the model is not misled.

{% highlight ruby %}
{% endhighlight %}

![](/assets/images/wisconsin/corrplot.png)

### Feature Engineering

Creating time-step features: 'month', 'quarter' and 'year'.

{% highlight ruby %}
{% endhighlight %}

We also created lag features: 'sales_lag_1' and 'sales_lag_2'

{% highlight ruby %}
{% endhighlight %}

# Model Building

## Time Series Cross Validation
{% highlight ruby %}
{% endhighlight %}

![](/assets/images/wisconsin/rf_parameter.png)

{% highlight ruby %}
{% endhighlight %}

## XGBoost Regression
We used the XGB Reg module to build our model. We used parameters 

# Overall Evaluation
![](/assets/images/wisconsin/accuracy.png)

# Conclusion
