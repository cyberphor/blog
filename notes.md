---
layout: null
title: 'Notes'
permalink: 'notes'
---

<h3><img src="{{ site.baseurl }}/assets/pt.png"> Vulnerability Management</h3>
<ul class="notes-list">
  {% assign sorted_posts = site.posts | sort: 'title' %}
  {% for post in sorted_posts %}
    {% if post.category == 'notes' %}
      <li>
        <a href="{{ post.url | relative_url }}">
          {{ post.title }}
        </a>
      </li>
    {% endif %}
  {% endfor %}
</ul>
