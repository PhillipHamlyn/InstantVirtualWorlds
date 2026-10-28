
---
layout: default
title: Home
---

# Instant Virtual Worlds

Development notes on procedural terrain, rendering, and virtual worlds.

## Articles

<ul class="article-list">
{% assign articles = site.articles | sort: "title" %}
{% for article in articles %}
  <li>
    <a href="{{ article.url | relative_url }}">{{ article.title | escape }}</a>
    {% if article.description %}
      <p>{{ article.description | escape }}</p>
    {% endif %}
  </li>
{% endfor %}
</ul>
