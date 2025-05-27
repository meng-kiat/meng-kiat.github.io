---
layout: single
title:  "Personal TFT Data Project (Python, MySQL, Tableau)"
date:   2025-4-09
category: analysis
author_profile: true
toc: true
toc_label: "Table of Contents"
toc_icon: "file"
toc_sticky: false
order: 1
classes: wide
---
Date Posted: 2025-04-05

Category: [Data Projects](https://meng-kiat.github.io/analysis/){: .btn .btn--info .btn--small}

This is a personal project of mine to practise the full process from data collection to data analysis. 
In this project, I use Riot Games' API to fetch my personal match history data, process & query it, produce dashboards and perform analysis on it.

# Data Preparation
Data was collected using Riot's API, fetching my match history specifically. Here's my profile on [lolchess](https://lolchess.gg/profile/sea/siden-mks/set11). The data is from Set 11.


## Data Collection
Here is the .ipynb file where i fetch my match history data using Riot's API. 

[View Notebook](https://github.com/meng-kiat/My-jupyter-notebooks/blob/main/TFT_Data2.ipynb){: .btn .btn--info .btn--small}

> **Note**: code is outdated as server names have changed
{: .notice--info}

## Data Parsing
The match data was a highly nested json, so I flattened it in Python. Before flattening, I split and exported the data into df_traits, df_augments, df_units, df_items, making sure to keep match_number and placement information.

[View Notebook](https://github.com/meng-kiat/My-jupyter-notebooks/blob/main/Parsing%20TFT%20data%20copy.ipynb){: .btn .btn--info .btn--small}

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
You can refer to my [story](https://meng-kiat.github.io/dashboards/TFT%20EDA/) and my Tableau dashboard for surface level insights on my match data.

## Deep Dive Analysis

### What are the best units if you have an extra slot?
In TFT, most compositions have a set of units to play for optimal trait webs. Sometimes, you have extra slot(s), and we call units that take these slots "plus ones". To filter for them, maybe we investigate units with no items?

{% highlight ruby %}
#Best Units with no items (What units are the best flexible inserts / plus ones)
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

---

### What are my best/worst units?

Referring to Units most played:

![unitsmostplayed](/assets/images/tft1/unitsmostplayed.png)

Size depicts the amount of games a unit is played, and a darker colour indicates a better placement average.

Firstly, we omit the units from my best composition (Fated). These are all my most played units Ornn-Kindred-Azir-Thresh-Syndra-Sett-Ahri. Although **Ornn** is a great flexible addition as well, which explains his significantly higher play rate.

**Wukong** and **Udyr** are the best flexible add-ins amongst 5 costs. Udyr has a built-in revive, and a strong crowd control ability, making him a flexible tank addition to any team. Wukong provides strong teamwide bonuses with his heavenly and sage traits.

<ins>Best Carries:</ins> <br>
Kindred (3.21 in 72 games)<br>
Gnar (2.96 in 45 games)<br>
Syndra (3.24 in 58 games)<br>

<ins>Worst Carries:</ins> <br>
Lillia (5.21 in 14 games)<br>
Kog'Maw (4.69 in 13 games)<br>

**Neeko** stands out as the worst unit amongst my most played units with an average placement of 5.16.

**Neeko** is commonly played in Heavenly boards. Each heavenly unit gives different stat bonuses. Her bonus is flat hp, which is less valuable than other Heavenly bonuses such as additional resistances and attack speed later into the game.

Having Neeko on my board is a likely indicator that:
1.	I do not have **Wukong**, a powerful 5-cost heavenly unit that would adequately replace Neeko and has a more valuable heavenly bonus (omnivamp)
2.	In heavenly games where I died early because I did not play well, I am more likely to not have Wukong, a 5-cost unit which shows up later.

---

### What are my best/worst augments?
{% highlight ruby %}
#Best and worst augments
select das.name as augment, avg(da.participant_placement) as placement_average, count(da.participant_placement) as games, AVG(CASE WHEN da.participant_placement = 1 THEN 1 ELSE 0 END) AS win_rate
from df_augments da left join df_augment_static as das on da.participant_augments = das.id
group by das.name
having games > 2
order by placement_average;
{% endhighlight %}

![worstaugments](/assets/images/tft1/worstaugments.png)

**Tiny Titans** has poor performance in my games.
-	However, it is likely associated with riskier plays
    -	Lose-streak traits like fortune, where I sacrifice tactician health in the early game for future rewards.
-	Does not give any combat power or economy unlike most augments.

**Big Grab Bag** stands out to me as I believed that it was a flexible augment that I could take in most games, however, my placement average (5.20 in 10 games) suggests otherwise.

>**Big Grab Bag:** Gain 3 random components, 2 gold, and 1 Reforger.

It has bad value, offering only 3 components, and likely should be taken only in spots where I need the **Reforger** (For example, in games where I have too many offensive components and need defensive ones instead.) 

>**Reforger:** Unequip all items and randomly transform them into new ones of a similar type and quality.

**Big Grab Bag** offering 3 components also means it is significantly weaker when the game gives all players an even number of components, as I will have a leftover component. (2 components make 1 item, which is significantly stronger.)

![topaugments](/assets/images/tft1/topaugments1.png)

Notably, **Salvage Bin** is an extremely good augment for me. 

>**Salvage Bin:** Gain 1 random completed item now, and 1 component after 7 player combats. Selling champions breaks completed items into components (excluding Tactician's Crown).

In terms of combat strength, Salvage Bin is relatively worse than other augments, as it only offers 3 components in overall combat strength (Combat augments that buff whole teams would be worse in early game, buffing only a few units, but scale better as you are able to field more units in the late game). Its strength comes from allowing you to complete subpar items in the early game for early board strength for good tempo early. 

- I could be too passive in other games, saving my item components, resulting in disparity in performance.
- I am comfortable playing a tempo-style where I conserve tactician hp by winning early. But this could also be an indicator that I am good at converting these spots to high placements despite relatively less combat power from Salvage Bin.
- Tempo-style is generally less risky and results in generally higher average placements.

### What are my best/worst traits?

{% highlight ruby %}
#Best Traits
Select avg(participant_placement) as placement_average, dts.name as trait, participant_traits_tier_current as trait_tier, min(participant_traits_tier_total) as no_of_tiers, min(participant_traits_num_units) as no_of_units, count(participant_placement) as games, AVG(CASE WHEN participant_placement = 1 THEN 1 ELSE 0 END) AS win_rate 
from df_traits dt left join df_trait_static dts on dt.participant_traits_name = dts.id 
where dts.name is not null
group by trait, trait_tier
having games > 20 and trait_tier >0
order by placement_average;
{% endhighlight %}

![toptraits](/assets/images/tft1/toptraits.png)

As expected, aligned with many past observations, **Dryad** stands out as my best trait / team composition, especially when it is tier 3, averaging a strong 2.09, with a 0.45 winrate.

**Exalted** (3.47) was a special trait introduced in this set, which gave you teamwide benefits for fielding 3 of such units. The unique feature of this trait was that units who had the exalted trait varied from game to game, with a total of around 60 variations. When possible, trying to fit this trait was extremely valuable.

**Great** (3.69 in 48 games!) is a unique trait of 5-cost unit Wukong, which further supports the deduction earlier that he is a great unit to flexibly slot into team compositions.