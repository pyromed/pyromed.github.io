---
title: ""
layout: single
permalink: /research/
author_profile: true
---

{% for project in site.research %}

<h3><a href="{{ project.url | relative_url }}">{{ project.title }}</a></h3>

<p>{{ project.excerpt }}</p>

<p><a href="{{ project.url | relative_url }}">Read More →</a></p>

<hr>

{% endfor %}
