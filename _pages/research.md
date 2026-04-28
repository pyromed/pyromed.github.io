---
title: "Research"
layout: single
permalink: /research/
---

{% for project in site.research %}
  ### [{{ project.title }}]({{ project.url }})
  {{ project.excerpt }}
  
  [Read More]({{ project.url }})
  
  ---
{% endfor %}
