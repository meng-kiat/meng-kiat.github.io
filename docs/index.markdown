---
title: Welcome to my portfolio website!
layout: single
#collection: dashboards
classes: wide
author_profile: true
---

I keep track of various dashboards, analysis and videos I've done here.

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

## Analysis
<ul>
  {% for item in site.analysis %}
    <li>
      <a href="{{ item.url }}">{{ item.title }}</a>
      {{ item.excerpt | strip_html }}
    </li>
  {% endfor %}
</ul>

## Videos
<ul>
  {% for item in site.videos %}
    <li>
      <a href="{{ item.url }}">{{ item.title }}</a>
      {{ item.excerpt | strip_html }}
    </li>
  {% endfor %}
</ul>