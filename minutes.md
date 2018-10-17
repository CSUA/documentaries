---
layout: page
title: Meeting Minutes
permalink: /minutes/
---
<ul>
{% for page in site.pages %}
{% if page.type == "minutes" %}
<li><a href="{{ page.url }}">{{ page.url | remove_first: "/minutes/" | url_decode }}</a></li>
{% endif %}
{% endfor %}
</ul>
