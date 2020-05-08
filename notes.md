---
layout: page
title: Notes
icon: fa-pencil-alt
order: 2
---

{% for category in site.categories %}
 {% assign total = 0 %}
  {% for post in category[1] %}
     {% if post.tags[0] != "Daily" %}
      {% assign total = total | plus: 1 %}
     {% endif %}
  {% endfor %}
 {% unless total == 0 %}
   <h3>{{ category[0] }}</h3>
  <ul>
    {% for post in category[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
  {% endunless %}
{% endfor %}