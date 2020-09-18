---
layout: default
title: 'Notes'
permalink: 'notes'
---

<h3><img src="{{ site.baseurl }}/assets/notes.png">Guides</h3>
<ul class="notes-list">
  {% assign sorted_posts = site.posts | sort: 'title' %}
  {% for post in sorted_posts %}
    {% if post.subcategory == 'guides' %}
      <li>
        <a href="{{ post.url | relative_url }}">
          {{ post.title }}
        </a>
      </li>
    {% endif %}
  {% endfor %}
</ul>

<h3><img src="{{ site.baseurl }}/assets/notes.png">Cheat Sheets</h3>
<ul class="notes-list">
  {% assign sorted_posts = site.posts | sort: 'title' %}
  {% for post in sorted_posts %}
    {% if post.subcategory == 'cheat-sheets' %}
      <li>
        <a href="{{ post.url | relative_url }}">
          {{ post.title }}
        </a>
      </li>
    {% endif %}
  {% endfor %}
</ul>

