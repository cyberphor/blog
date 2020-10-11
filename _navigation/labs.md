---
layout: default
title: 'Labs'
permalink: 'labs'
---

<h3><img src="{{ site.baseurl }}/assets/notes.png"> Labs</h3>
<ul class="notes-list">
  {% assign sorted_posts = site.posts | sort: 'title' %}
  {% for post in sorted_posts %}
    {% if post.category == 'labs' %}
      <li>
        <a href="{{ post.url | relative_url }}">
          {{ post.title }}
        </a>
      </li>
    {% endif %}
  {% endfor %}
</ul>
