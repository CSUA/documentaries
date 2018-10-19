---
layout: page
title: Meeting Minutes
permalink: /minutes/
---
<ul>
{% for post in site.posts %}
<li><a href="{{ post.url | relative_url }}">Last Edited: {{ post.date | date: "%m/%d/%Y" }} {{ post.title }}</a></li>
{% endfor %}
</ul>
