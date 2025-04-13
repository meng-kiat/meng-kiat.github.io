---
title: Welcome to my portfolio website!
layout: single
#collection: dashboards
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
{% for item in site.analysis %}
- [{{ item.title }}]({{ item.url }})  
  <br><span class="small-excerpt">{{ item.excerpt | strip_html }}</span>
{% endfor %}

---

# Videos
{% for item in site.videos %}
- [{{ item.title }}]({{ item.url }}) – {{ item.excerpt | strip_html }}
{% endfor %}