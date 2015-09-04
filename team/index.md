---
layout: page
title: "Team"
excerpt: "Team"
tags: ["team"]
---

{% for author in site.data.authors %}
  {% include _team-member.html %}
{% endfor %}
