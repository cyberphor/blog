---
layout: default
---

<h3><img src="{{ site.baseurl }}/_assets/pt.png"> Vulnerability Management</h3>
  <ul class="posts-list">
    {% for post in site.posts %}
      {% if post.category == 'essays' %}
        {% if post.subcategory == 'vulnerability-management' %}
          <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a></li>
        {% endif %}
      {% endif %}
    {% endfor %}
  </ul>

<h3><img src="{{ site.baseurl }}/_assets/nsm.png"> Network Security Monitoring</h3>
  <ul class="posts-list">
    {% for post in site.posts %}
      {% if post.category == 'essays' %}
        {% if post.subcategory == 'network-security-monitoring' %}
          <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a></li>
        {% endif %}
      {% endif %}
    {% endfor %}
  </ul>

<h3><img src="{{ site.baseurl }}/_assets/df.png"> Digital Forensics</h3>
  <ul class="posts-list">
    {% for post in site.posts %}
      {% if post.category == 'essays' %}
        {% if post.subcategory == 'digital-forensics' %}
          <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a></li>
        {% endif %}
      {% endif %}
    {% endfor %}
  </ul>

<h3><img src="{{ site.baseurl }}/_assets/ir.png"> Incident Response</h3>
  <ul class="posts-list">
    {% for post in site.posts %}
      {% if post.category == 'essays' %}
        {% if post.subcategory == 'incident-response' %}
          <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a></li>
        {% endif %}
      {% endif %}
    {% endfor %}
  </ul>
  
<h3><img src="{{ site.baseurl }}/_assets/pt.png"> Guides</h3>
  <ul class="posts-list">
    {% for post in site.posts %}
      {% if post.category == 'guides' %}
          <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a></li>
      {% endif %}
    {% endfor %}
  </ul>
