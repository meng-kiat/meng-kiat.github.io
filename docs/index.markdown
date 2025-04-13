---
title: Welcome to my portfolio website!
layout: single
#collection: dashboards
classes: wide
author_profile: true
---

I keep track of various dashboards, analysis and videos I've done here.

## Dashboards
<ul style="list-style: none; padding-left: 0;">
  {% for item in site.dashboards %}
    <li>
      <a href="{{ item.url }}">{{ item.title }}</a>
    </li>
  {% endfor %}
</ul>

## Analysis
<ul style="list-style: none; padding-left: 0;">
  {% for item in site.analysis %}
    <li>
      <a href="{{ item.url }}">{{ item.title }}</a>
    </li>
  {% endfor %}
</ul>

## Videos
<ul style="list-style: none; padding-left: 0;">
  {% for item in site.videos %}
    <li>
      <a href="{{ item.url }}">{{ item.title }}</a>
    </li>
  {% endfor %}
</ul>