---
layout: page
title: Projects
permalink: /projects/
---

Here are my projects.

<ul class="listing">
{% for project in site.projects %}
  {% capture y %}{{project.date | date:"%Y"}}{% endcapture %}
  {% if year != y %}
    {% assign year = y %}
    <li class="listing-separator">{{ y }}</li>
  {% endif %}
  <li class="listing-item">
    <time datetime="{{ post.date | date:"%Y-%m-%d" }}">{{ project.date | date:"%Y-%m-%d" }}</time>
    <a href="{{ post.url }}" title="{{ post.title }}">{{ project.title }}</a>
  </li>
{% endfor %}
</ul>
