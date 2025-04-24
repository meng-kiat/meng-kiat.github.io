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
from df_augments da 
left join df_augment_static das 
on da.participant_augments = das.id;
{% endhighlight %}

![cleaned](/assets/images/tft1/cleaned.png)

## Data Querying
I use the previously exported dataframes (.csv files) and imported them for querying into mysql.

# Data Analysis

## Exploratory Data Analysis
You can find refer to my [story](https://meng-kiat.github.io/dashboards/TFT%20EDA/) and my Tableau dashboard for surface level insights on my match data.

## Deep Dive Analysis

### What are the best units if you have an extra slot?
In TFT, most compositions have a set of units to play for optimal trait webs. Sometimes, you have extra slot(s), and we call units that take these slots "plus ones". To filter for them, maybe we investigate units with no items?

{% highlight ruby %}
##Best Units with no items (What units are the best flexible inserts / plus ones)
select avg(participant_placement) as placement_average, dus.unit_name as unit, dus.rarity, count(participant_placement) as games, AVG(CASE WHEN participant_placement = 1 THEN 1 ELSE 0 END) AS win_rate
from df_items di left join df_item_static dis on di.itemNames = dis.id left join df_unit_static_new dus on di.character_id = dus.id
where itemNames = ''
group by dus.unit_name, dus.rarity
having games >2
order by placement_average;
{% endhighlight %}

![no items](/assets/images/tft1/noitems.png)

I expected higher cost units here, as generally, higher cost units should be associated with having overall more strength as add-ins. 
Additionally, higher cost units are only acquirable typically at later stages of the game – it is reasonable to assume that high cost units would be associated with higher placements as you survive later into the game.

Instead, Many of the units are "trait-bots", which essentially are units you play for certain strong trait breakpoints. 
For example:

Reksai (2.13333)
-	is a pretty good unit as a 1 cost, since she has an area of effect crowd-control ability
-	But her strength is likely tied with her trait, Dryad.
-	When players play a Rek’Sai, it is likely that they have Azir (5-cost) and +1 trait, which allows them to play the very strong scaling 6 Dryad trait.
-	6 Dryad averages a 2.0909 in my games.

I made an inaccurate assumption as it is likely that I give items to these flexible add-ons once I have itemised key carries and tanks in my compositions. We will refer to units most played to further investigate flexible add-ons while investigating what are my best units in general.

### What are my best/worst units?

Referring to Units most played:

![unitsmostplayed](/assets/images/tft1/unitsmostplayed.png)

Firstly, we omit the units from my best composition (Fated). These are all my most played units Ornn-Kindred-Azir-Thresh-Syndra-Sett-Ahri. Although Ornn is a great flexible addition as well, which explains his significantly higher play rate.

Wukong and Udyr are the best flexible add-ins amongst 5 costs. Udyr has a built-in revive, and strong crowd control ability, making him a flexible tank addition to any team. Wukong provides strong teamwide bonuses with his heavenly and sage traits.

Best Carries: 
Kindred (3.21 in 72 games)
Gnar (2.96 in 45 games)
Syndra (3.24 in 58 games)

Worst Carries: 
Lillia (5.21 in 14 games), 
Kog'Maw (4.69 in 13 games)

Neeko stands out as the worst unit amongst my most played units with an average placement of 5.16.

Neeko is commonly played in Heavenly boards. In the heavenly trait, each heavenly unit gives different stat bonuses. Her bonus is flat hp, which is worse than other Heavenly bonuses such as additional resistances and attack speed, as flat hp is far less valuable later into the game.
Having Neeko on my board is a likely indicator that:
1.	I do not have Wukong, a powerful 5-cost heavenly unit that would adequately replace Neeko and has a more powerful stat bonus (omnivamp)
2.	In games where I died early because of a weak board / tempo etc., I am more likely to not have Wukong, a 5-cost unit.

### What are my best/worst augments?
![topaugments](/assets/images/tft1/topaugments.png)

Tiny Titans has poor performance in my games.
-	However, it is likely associated with riskier plays
o	Lose-streak traits like fortune, where I sacrifice tactician health in the early game for future rewards.
-	Does not give any combat power or economy unlike most augments.

Notably, Salvage Bin is an extremely good augment for me. 

>Salvage Bin: Gain 1 random completed item now, and 1 component after 7 player combats. Selling champions breaks completed items into components (excluding Tactician's Crown).

In terms of combat strength, Salvage Bin is relatively worse than other augments, as it only offers 3 components in overall combat strength (Combat augments that buff whole teams would be worse in early game, buffing only a few units, but scale better as you are able to field more units in the late game). Its strength comes from allowing you to complete subpar items in the early game for early board strength for good tempo early. 

- I could be too passive in other games, saving my item components, resulting in disparity in performance.
- I am comfortable playing a tempo-style where I conserve tactician hp by winning early. But this could also be an indicator that I am good at converting these spots to high placements despite relatively less combat power from Salvage Bin.
- Tempo-style is generally less risky and results in generally higher average placements.
