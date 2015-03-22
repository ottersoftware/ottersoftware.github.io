---
layout: page
title: Categories
excerpt: "A list of categories."
search_omit: true
---

{% assign sorted_categories = site.categories | sort %}

<ul class="post-list">
{% for categoryItem in sorted_categories %} 
  <li><article><a href="#{{ categoryItem[0] }}">{{ categoryItem[0] | upcase }} ({{ categoryItem[1].size }} articles)</a></article></li>
{% endfor %}
</ul>



{% assign sorted_categories = site.categories | sort %}
{% for categoryItem in sorted_categories %} 
  <h2 id="{{ categoryItem[0] }}">{{ categoryItem[0] | upcase }}</h2>
  <ul class="post-list">
    {% assign pages_list = categoryItem[1] %}  
    {% for post in pages_list %}
      {% if post.title != null %}
      {% if group == null or group == post.group %}
	  <li><article><a href="{{ site.url }}{{ post.url }}">{{ post.title }}<span class="entry-date"><time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time></span></a></article></li>
      {% endif %}
      {% endif %}
    {% endfor %}
    {% assign pages_list = nil %}
    {% assign group = nil %}
  </ul>
{% endfor %}
