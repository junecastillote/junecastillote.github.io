---
layout: page
title: Authors
permalink: /author/
---

<ul>
  {% for author in site.authors %}
    <li>
      <h2><a href="{{ author.url }}">{{ author.name }}</a></h2>
      <p>{{ author.content }}</p>
    </li>
  {% endfor %}
</ul>
