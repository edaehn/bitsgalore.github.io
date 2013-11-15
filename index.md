---
layout: page
title: bitsgalore.org
---
{% include JB/setup %}


{% for post in site.posts %}
   <h3><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a> <small>{{ post.date | date_to_string }}</small></h3>
   {{ post.excerpt }}
   <small><a href="{{ BASE_PATH }}{{ post.url }}">More ...</a></small>
{% endfor %}

