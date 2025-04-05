---
layout: single
title:  "Personal TFT Data Project"
date:   2025-04-04
category: analysis
author_profile: true
toc: true
toc_label: "Table of Contents"
toc_icon: "file"
---

# Introduction
This is a personal project of mine to practise the full process from data collection to data analysis. 
In this project, I use Riot Games' API to fetch my personal match history data, process & query it, produce dashboards and perform analysis on it.

# Data Preparation
## Data Collection
Here is the .ipynb file where i fetch my match history data using Riot's api. 

[View Notebook](https://nbviewer.org/github/meng-kiat/My-jupyter-notebooks/blob/main/TFT_Data.ipynb){: .btn .btn--info .btn--small}

> **Note**: code is outdated as server names have changed
{: .notice--info}

## Data Parsing
The match data was a highly nested json, so I flattened it in python. Before flattening, I split and exported the data into df_traits, df_augments, df_units, df_items, making sure to keep match_number and placement information.

[View Notebook](https://nbviewer.org/github/meng-kiat/My-jupyter-notebooks/blob/main/Parsing%20TFT%20data%20copy.ipynb){: .btn .btn--info .btn--small}

## Data Querying
I use the previously exported dataframes (.csv files) and imported them for querying into mysql.