---
title: Welcome to my portfolio website!
layout: single
#collection: dashboards
classes: wide
author_profile: true
---

I keep track of various dashboards, analysis and videos I've done here.

# Projects
<ul style="list-style: none; padding-left: 0;">
  {% for item in site.dashboards %}
    <li>
      <a href="{{ item.url }}">{{ item.title }}</a>
      <span style="display: block; font-size: 0.75em;">
        {{ item.excerpt | strip_html }}
      </span>
    </li>
  {% endfor %}
</ul>


# Analysis
<ul style="list-style: none; padding-left: 0;">
  {% for item in site.analysis %}
    <li>
      <a href="{{ item.url }}">{{ item.title }}</a>
      <span style="display: block; font-size: 0.75em;">
        {{ item.excerpt | strip_html }}
      </span>
    </li>
  {% endfor %}
</ul>
---

# Videos
<ul style="list-style: none; padding-left: 0;">
  {% for item in site.videos %}
    <li>
      <a href="{{ item.url }}">{{ item.title }}</a>
      <span style="display: block; font-size: 0.75em;">
        {{ item.excerpt | strip_html }}
      </span>
    </li>
  {% endfor %}
</ul>