---
#
# By default, content added below the "---" mark will appear in the home page
# between the top bar and the list of recent posts.
# To change the home page layout, edit the _layouts/home.html file.
# See: https://jekyllrb.com/docs/themes/#overriding-theme-defaults
#
layout: default
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
