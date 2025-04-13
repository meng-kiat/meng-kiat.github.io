---
title: Welcome to my portfolio website!
layout: single
#collection: dashboards
classes: wide
author_profile: true
---

I keep track of various dashboards, analysis and videos I've done here.

## [Personal TFT Data Project (Dashboard)](https://meng-kiat.github.io/dashboards/TFT%20EDA/)
<small>Category: Dashboards</small>


<small>Category: Videos</small>
## [Real Estate Profit Management (Dashboard)](https://meng-kiat.github.io/dashboards/Watershed%20Dashboard/)

## [Digital Learning Internship Video](https://meng-kiat.github.io/videos/2025-25-03-First/)
<small>Category: Videos</small>

# Projects
{% for item in site.dashboards %}
- [{{ item.title }}]({{ item.url }}) – {{ item.excerpt | strip_html }}
{% endfor %}

---

#Research
{% for item in site.analysis %}
- [{{ item.title }}]({{ item.url }}) – {{ item.excerpt | strip_html }}
{% endfor %}

---

# Notes
{% for item in site.videos %}
- [{{ item.title }}]({{ item.url }}) – {{ item.excerpt | strip_html }}
{% endfor %}