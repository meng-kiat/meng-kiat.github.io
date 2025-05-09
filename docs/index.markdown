---
title: Welcome to my portfolio website!
layout: single
classes: wide
author_profile: true

feature_row:
  - image_path: /assets/images/gallery1/radashboard.png
    alt: "placeholder image 1"
    title: "Watershed Dashboard"
    excerpt: "Capstone Project for Real Estate Management"
    url: /dashboards/Watershed%20Dashboard/
  - image_path: /assets/images/gallery1/radashboard.png
    alt: "placeholder image 2"
    title: "Time Series Forecasting"
    excerpt: "Forecasting Project optimising store inventory"
    url: /dashboards/Watershed%20Dashboard/
    btn_label: "Read More"
    btn_class: "btn--inverse"
  - image_path: /assets/images/unsplash-gallery-image-3-th.jpg
    title: "Personal TFT Data Project"
    excerpt: "This is some sample content that goes here with **Markdown** formatting."


---

I keep track of various projects, dashboards and videos I've done here.

{% include feature_row %}

## Projects
<ul>
  {% assign reversed_analysis = site.analysis | reverse %}
  {% for item in reversed_analysis %}
    <li>
      <a href="{{ item.url }}">{{ item.title }}</a>
      <span style="display: block; font-size: 0.75em;"> 
        {{ item.excerpt | strip_html }} 
      </span>
    </li>
  {% endfor %}
</ul>

## Dashboards
<ul>
  {% assign reversed_dashboards = site.dashboards | reverse %}
  {% for item in reversed_dashboards %}
    <li>
      <a href="{{ item.url }}">{{ item.title }}</a>
      <span style="display: block; font-size: 0.75em;"> 
        {{ item.excerpt | strip_html }} 
      </span>
    </li>
  {% endfor %}
</ul>

## Videos
<ul>
  {% assign reversed_videos = site.videos | reverse %}
  {% for item in reversed_videos %}
    <li>
      <a href="{{ item.url }}">{{ item.title }}</a>
      <span style="display: block; font-size: 0.75em;"> 
        {{ item.excerpt | strip_html }} 
      </span>
    </li>
  {% endfor %}
</ul>
