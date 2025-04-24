---
layout: single
title:  "Personal TFT Data Project (Python, MySQL, Tableau)"
date:   2025-4-05
category: analysis
author_profile: true
toc: true
toc_label: "Table of Contents"
toc_icon: "file"
toc_sticky: "true"
order: 1
---
Date Posted: 2025-04-05

Category: [Data Projects](https://meng-kiat.github.io/analysis/){: .btn .btn--info .btn--small}

This is a personal project of mine to practise the full process from data collection to data analysis. 
In this project, I use Riot Games' API to fetch my personal match history data, process & query it, produce dashboards and perform analysis on it.

# Data Preparation
Data was collected using Riot's API, fetching my match history specifically. Here's my profile on [lolchess](https://lolchess.gg/profile/sea/siden-mks/set11). The data is from Set 11.


## Data Collection
Here is the .ipynb file where i fetch my match history data using Riot's API. 

[View Notebook](https://nbviewer.org/github/meng-kiat/My-jupyter-notebooks/blob/main/TFT_Data.ipynb){: .btn .btn--info .btn--small}

> **Note**: code is outdated as server names have changed
{: .notice--info}

## Data Parsing
The match data was a highly nested json, so I flattened it in Python. Before flattening, I split and exported the data into df_traits, df_augments, df_units, df_items, making sure to keep match_number and placement information.

[View Notebook](https://nbviewer.org/github/meng-kiat/My-jupyter-notebooks/blob/main/Parsing%20TFT%20data%20copy.ipynb){: .btn .btn--info .btn--small}

### Data Cleaning
The raw match history data used system-level identifiers that were confusing. For example, in the match history data, the augment mulched's identifier was 'TFT11_Augment_Dryad'.

To clean this data, we will use Riot's static JSON data source (Data Dragon) to create csv files with cleaned names that we can then merge with our dataframes.

![flatten](/assets/images/tft1/flatten.png)

{% highlight ruby %}
#units
with open("tft-champion-minus.json") as json_file:
    json_data = json.load(json_file)

df_unit = pd.DataFrame([
    [value["id"], value["name"]] for value in json_data.values()
],columns=["id","name"])
{% endhighlight %}


After exporting these as csv files, we imported them into MySQL for querying to merge and clean the tables.

{% highlight ruby %}
# Uncleaned Augments
select * from df_augments; 
{% endhighlight %}

![uncleaned](/assets/images/tft1/uncleaned.png)

{% highlight ruby %}
# Cleaned Augments
select match_number, participant_placement, name, CASE WHEN da.participant_placement = 1 THEN 1 ELSE 0 END as First
from df_augments da left join df_augment_static das on da.participant_augments = das.id;
{% endhighlight %}

![cleaned](/assets/images/tft1/cleaned.png)

## Data Querying
I use the previously exported dataframes (.csv files) and imported them for querying into mysql.

# Data Analysis

## Exploratory Data Analysis


