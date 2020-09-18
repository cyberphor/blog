---
layout: default
title: 'Demos'
permalink: 'demos'
---

<h3><img src="{{ site.baseurl }}/assets/notes.png">Demos</h3>
<ul class="notes-list">
  {% assign sorted_posts = site.posts | sort: 'title' %}
  {% for post in sorted_posts %}
    {% if post.subcategory == 'demos' %}
      <li>
        <a href="{{ post.url | relative_url }}">
          {{ post.title }}
        </a>
      </li>
    {% endif %}
  {% endfor %}
</ul>
