---
title: Welcome to my portfolio website!
layout: single
classes: wide
author_profile: true

gallery:
  - url: /dashboards/Watershed%20Dashboard/
    image_path: /assets/images/gallery1/radashboard.png
    alt: "placeholder image 1"
    title: "Image 1 title caption"
  - url:  # could be bigger image instead, to show off dashboards nicer
    image_path: /assets/images/gallery1/.png
    alt: "placeholder image 2"
    title: "Image 2 title caption"
  - url: /assets/images/gallery1/radashboard.png
    image_path: /assets/images/gallery1/radashboard.png
    alt: "placeholder image 3"
    title: Image 3 title caption

feature_row:
  - image_path: /assets/images/unsplash-gallery-image-1-th.jpg
    alt: "placeholder image 1"
    title: "Placeholder 1"
    excerpt: "This is some sample content that goes here with **Markdown** formatting."
  - image_path: /assets/images/gallery1/radashboard.png
    alt: "placeholder image 2"
    title: "Placeholder 2"
    excerpt: "This is some sample content that goes here with **Markdown** formatting."
    url: /dashboards/Watershed%20Dashboard/
    btn_label: "Read More"
    btn_class: "btn--inverse"
  - image_path: /assets/images/unsplash-gallery-image-3-th.jpg
    title: "Placeholder 3"
    excerpt: "This is some sample content that goes here with **Markdown** formatting."


---

I keep track of various projects, dashboards and videos I've done here.

{% include gallery %}

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
