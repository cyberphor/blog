---
layout: page
title: 'Notes & Cheatsheets'
permalink: 'notes'
---

<ul class="notes-list">
  {% for post in site.posts %}
    {% if post.category == 'notes' %}
      <li>
        <a href="{{ post.url | relative_url }}">
          {{ post.title }}
        </a>
      </li>
    {% endif %}
  {% endfor %}
</ul>
