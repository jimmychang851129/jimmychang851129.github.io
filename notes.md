---
layout: page
title: Notes
icon: fa-pencil-alt
order: 2
---

{% for category in site.categories %}
{%- if category[0] != 'Daily' -%}
  <h3>{{ category[0] }}</h3>
  <ul>
    {% for post in category[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
  {%- endif -%}
{% endfor %}
