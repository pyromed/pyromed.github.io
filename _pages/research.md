---
title: "Research"
layout: collection
permalink: /research/
collection: research
entries_layout: grid
---

### Debugging List
{% for item in site.research %}
  - Found: {{ item.title }}
{% endfor %}
