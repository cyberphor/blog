---
layout: default
title: 'Downloads'
permalink: 'downloads'
---

<h3><img src="{{ site.baseurl }}/_assets/notes.png"> Downloads</h3>
* [The Investigative Process by Chris Sanders]({{ site.url }}{{ site.baseurl }}/_downloads/investigative-process.pdf)
* [Nessus: User Guide]({{ site.url }}{{ site.baseurl }}/_downloads/nessus.pdf)
* [Nmap]({{ site.url }}{{ site.baseurl }}/_downloads/nmap.pdf)
* [Windows Group Policy]({{ site.url }}{{ site.baseurl }}/_downloads/group-policy.pdf)

<h3><img src="{{ site.baseurl }}/_assets/notes.png"> Guides</h3>
<ul class="notes-list">
  {% assign sorted_posts = site.posts | sort: 'title' %}
  {% for post in sorted_posts %}
    {% if post.category == 'guides' %}
      <li>
        <a href="{{ post.url | relative_url }}">
          {{ post.title }}
        </a>
      </li>
    {% endif %}
  {% endfor %}
</ul>
