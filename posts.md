---
layout: default
title: 'Essays'
permalink: 'essays'
---

<h3><img src="{{ site.baseurl }}/assets/pt.png"> Vulnerability Management</h3>
  <ul class="posts-list">
    {% for post in site.posts %}
      {% if post.category == 'posts' %}
        {% if post.subcategory == 'vulnerability-management' %}
          <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a></li>
        {% endif %}
      {% endif %}
    {% endfor %}
  </ul>

<h3><img src="{{ site.baseurl }}/assets/nsm.png"> Network Security Monitoring</h3>
  <ul class="posts-list">
    {% for post in site.posts %}
      {% if post.category == 'posts' %}
        {% if post.subcategory == 'network-security-monitoring' %}
          <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a></li>
        {% endif %}
      {% endif %}
    {% endfor %}
  </ul>

<h3><img src="{{ site.baseurl }}/assets/ir.png"> Incident Response</h3>
  <ul class="posts-list">
    {% for post in site.posts %}
      {% if post.category == 'posts' %}
        {% if post.subcategory == 'incident-response' %}
          <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a></li>
        {% endif %}
      {% endif %}
    {% endfor %}
  </ul>

<h3><img src="{{ site.baseurl }}/assets/df.png"> Digital Forensics</h3>
  <ul class="posts-list">
    {% for post in site.posts %}
      {% if post.category == 'posts' %}
        {% if post.subcategory == 'digital-forensics' %}
          <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a></li>
        {% endif %}
      {% endif %}
    {% endfor %}
  </ul>
