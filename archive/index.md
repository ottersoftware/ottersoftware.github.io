---
layout: page
title: Archive
excerpt: "A list of all articles sorted by date."
search_omit: true
---

<ul class="post-list">
{% for post in site.posts %} 
  <li><article><a href="{{ site.url }}{{ post.url }}">{{ post.title }}<br><span class="entry-categories">Posted in: {{ post.categories }}</span> <span class="entry-date"><time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time></span></a></article></li>
{% endfor %}
</ul>
