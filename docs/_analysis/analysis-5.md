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
classes: wide
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

COVID-19 caused heavy drop in retail sales. We created an indicator feature ['is_covid'] for the machine learning algorithm to identify the COVID-19 period as outliers, ensuring that the model can account for these outliers.

{% highlight ruby %}
###Adding COVID-19 indicator feature
df['is_covid'] = df.index.to_series().between('2020-04', '2020-06').astype(int)
{% endhighlight %}

### Feature Engineering

Creating time-step features: 'month', 'quarter' and 'year'.

{% highlight ruby %}
#Time Step Features
def create_features(df):
    """
    Creates time-step features (month, quarter, year) from the DataFrame's index.

    Args:
        df (pd.DataFrame): The input DataFrame with a DatetimeIndex.

    Returns:
        pd.DataFrame: The DataFrame with added time-step features.
    """
    df_copy = df.copy()

    # df_copy['hour'] = df_copy.index.hour # Uncomment if needed
    # df_copy['dayofweek'] = df_copy.index.dayofweek # Uncomment if needed
    df_copy['month'] = df_copy.index.month
    df_copy['quarter'] = df_copy.index.quarter
    df_copy['year'] = df_copy.index.year
    # df_copy['dayofyear'] = df_copy.index.dayofyear # Uncomment if needed

    return df_copy

df = create_features(df)
{% endhighlight %}

With time-step features, we can do some visualisations to investigate signs of trends and/or seasonality

![monthboxplot](/assets/images/retailsalessg/monthboxplot.png)

We also created lag features: 'sales_lag_1' and 'sales_lag_2'

{% highlight ruby %}
#Lag Features
df['sales_lag_1'] = df['retail_sales_value_estimated'].shift(1)
df['sales_lag_2'] = df['retail_sales_value_estimated'].shift(2)
{% endhighlight %}

We assessed that lag features were unsuitable and worsened prediction results instead.

# Model Building
## Time Series Cross Validation

We used the TimeSeriesSplit module to conduct time series cross validation by creating n folds to assess the efficacy of using time series forecasting for retail sales values. This method helps to prevent lookahead bias by repeatedly evaluating a different set of 12 months towards the end of the dataset. 

It should be worth considering that our dataset is quite small, so it will be expected to see natural improvements as 12 datarows worth of data are added after each fold.

We used parameters n_split = 3 & test_size = 12.

{% highlight ruby %}
tss = TimeSeriesSplit(n_splits = 3, test_size = 12
                      , gap = 0)
{% endhighlight %}

## XGBoost Regression
We used the XGB Reg module to build our model. 

{% highlight ruby %}
tss = TimeSeriesSplit(n_splits = 3, test_size = 12
                      , gap = 0)

fold = 0
preds = []
scores = []
for train_idx, val_idx in tss.split(df):
    train = df.iloc[train_idx]
    test = df.iloc[val_idx]

    train = create_features(train)
    test = create_features(test)

    FEATURES = ['quarter', 'month', 'year','is_covid']
    TARGET = 'retail_sales_value_estimated'

    X_train = train[FEATURES]
    y_train = train[TARGET]

    X_test = test[FEATURES]
    y_test = test[TARGET]

    reg = xgb.XGBRegressor(base_score=0.5, booster='gbtree',    
                           n_estimators=1000,
                           early_stopping_rounds=50,
                           objective='reg:linear',
                           max_depth=3,
                           learning_rate=0.01)
    reg.fit(X_train, y_train,
            eval_set=[(X_train, y_train), (X_test, y_test)],
            verbose=100)

    y_pred = reg.predict(X_test)
    preds.append(y_pred)
    score = np.sqrt(mean_squared_error(y_test, y_pred))
    scores.append(score)
{% endhighlight %}

## Model initial evaluation
The RMSE results of the folds were:
Score across folds: 132.5939
Fold scores:[147.9255505042701, 138.56629972923642, 111.28990440456352]

The image below shows the model's prediction against the actual data for dates past 2024.
![prediction1year](/assets/images/retailsalessg/prediction1year.png)

As we can see, the model has generally done well to capture the monthly seasonality of retail sales in SG, capturing the shape without too much of an error.

# Prediction & Visualisation
## Final Model & Forecasting

{% highlight ruby %}
#Final Model Building

FEATURES = ['quarter', 'month', 'year', 'is_covid']
TARGET = 'retail_sales_value_estimated'

X_full = df[FEATURES]
y_full = df[TARGET]
{% endhighlight %}

{% highlight ruby %}
reg = xgb.XGBRegressor(
    base_score=0.5,
    booster='gbtree',
    n_estimators=1000,
    early_stopping_rounds=50,
    objective='reg:squarederror',  # use this instead of 'reg:linear' (deprecated)
    max_depth=3,
    learning_rate=0.1
)

reg.fit(X_full, y_full, eval_set=[(X_full, y_full)], verbose=100)
{% endhighlight %}

## Tableau Visualisation
We then visualised the actual & forecasted data in Tableau.

{% raw %}
<div class='tableauPlaceholder' id='viz1753042835389' style='position: relative'>
  <noscript>
    <a href='#'>
      <img alt='Dashboard 1'
           src='https://public.tableau.com/static/images/RS/RSGForecasting/Dashboard1/1_rss.png'
           style='border: none' />
    </a>
  </noscript>
  
  <object class='tableauViz' style='display:none;'>
    <param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' />
    <param name='embed_code_version' value='3' />
    <param name='site_root' value='' />
    <param name='name' value='RSGForecasting/Dashboard1' />
    <param name='tabs' value='no' />
    <param name='toolbar' value='yes' />
    <param name='static_image' value='https://public.tableau.com/static/images/RS/RSGForecasting/Dashboard1/1.png' />
    <param name='animate_transition' value='yes' />
    <param name='display_static_image' value='yes' />
    <param name='display_spinner' value='yes' />
    <param name='display_overlay' value='yes' />
    <param name='display_count' value='yes' />
    <param name='language' value='en-US' />
    <param name='filter' value='publish=yes' />
    <param name='filter' value='showOnboarding=true' />
  </object>
</div>

<script type='text/javascript'>
  var divElement = document.getElementById('viz1753042835389');
  var vizElement = divElement.getElementsByTagName('object')[0];

  if (divElement.offsetWidth > 800) {
    vizElement.style.width = '1266px';
    vizElement.style.height = '795px';
  } else if (divElement.offsetWidth > 500) {
    vizElement.style.width = '1266px';
    vizElement.style.height = '795px';
  } else {
    vizElement.style.width = '1266px';
    vizElement.style.height = '795px';
  }

  var scriptElement = document.createElement('script');
  scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';
  vizElement.parentNode.insertBefore(scriptElement, vizElement);
</script>

{% endraw %}

# Conclusion
Using machine learning models to predict retail sales forecasting showed some promise as seen in the results of the time series cross validation. It is worth acknowledging that this dataset is lacking in volume, both in terms of depth and breadth. There could be more data points, i.e. using weekly or even daily sales for analysis, and there could be more exogenous variables such as promotions, holidays, etc.
