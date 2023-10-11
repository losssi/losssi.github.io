---
layout: single
title: Blog
author_profile: true
permalink: /blog/
---
<h3 class="archive__subtitle">Posts</h3>

{% for post in site.posts %}
  {% include archive-single.html %}
{% endfor %}
