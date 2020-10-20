---
layout: default
title: 'Notes'
permalink: 'notes'
---

<h3><img src="{{ site.baseurl }}/_assets/notes.png"> Notes</h3>
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

<h3><img src="{{ site.baseurl }}/_assets/notes.png"> Print-Friendly Notes</h3>
* [Windows Group Policy]({{ site.baseurl }}/_assets/notes-group-policy.pdf)
