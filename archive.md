---
layout: page
title : 归档
header : 所有文章归档
---

{% assign posts_collate = site.posts %}

  {% for post in posts_collate  %}
    {% capture this_year %}{{ post.date | date: "%Y" }}{% endcapture %}
    {% capture this_month %}{{ post.date | date: "%m" }}{% endcapture %}
    {% capture next_year %}{{ post.previous.date | date: "%Y" }}{% endcapture %}
    {% capture next_month %}{{ post.previous.date | date: "%m" }}{% endcapture %}
  
    {% if forloop.first %}
      <h2>{{this_year}}年{{this_month}}月</h2>
      <ul>
    {% endif %}
  
    <li><span>{{ post.date | date: "%Y-%m-%d" }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}" target="_blank">{{ post.title }}</a></li>
  
    {% if forloop.last %}
      </ul>
    {% else %}
      {% if this_year != next_year %}
        </ul>
        <h2>{{next_year}}年{{next_month}}月</h2>
        <ul>
      {% else %}
        {% if this_month != next_month %}
          </ul>
          <h2>{{next_year}}年{{next_month}}月</h2>
          <ul>
        {% endif %}
      {% endif %}
    {% endif %}
  {% endfor %}
  
{% assign posts_collate = nil %}