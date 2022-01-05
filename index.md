---
layout: default
---

<ul class="posts-list">
    {% for post in site.posts %}
        <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a></li>
    {% endfor %}
</ul>