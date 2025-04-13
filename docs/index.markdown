---
title: Welcome to my portfolio website!
layout: collection
collection: dashboards
classes: wide
author_profile: true
---

I keep track of various dashboards, analysis and videos I've done here.

# Projects
{% for item in site.dashboards %}
- [{{ item.title }}]({{ item.url }}) – {{ item.excerpt | strip_html }}
{% endfor %}

---

# Analysis
<ul>
  {% for item in site.analysis %}
    <li>
      <a href="{{ item.url }}">{{ item.title }}</a>
      <span style=" font-size: 0.85em; color: #666;">
        - {{ item.excerpt | strip_html }}
      </span>
    </li>
  {% endfor %}
</ul>
---

# Videos
{% for item in site.videos %}
- [{{ item.title }}]({{ item.url }}) – {{ item.excerpt | strip_html }}
{% endfor %}

# Test
<ul>
  {% for item in site.analysis %}
    <li>
      <a href="{{ item.url }}">{{ item.title }}</a>
      <span style="display: block; font-size: 0.85em; color: #666;">
        {{ item.excerpt | strip_html }}
      </span>
    </li>
  {% endfor %}
</ul>

# Again
<ul>
  {% for item in site.analysis %}
    <li>
      <a href="{{ item.url }}">{{ item.title }}</a>
      <span style=" font-size: 0.85em; color: #666;">
        {{ item.excerpt | strip_html }}
      </span>
    </li>
  {% endfor %}
</ul>
