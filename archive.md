---
layout: default
title: Archive
---

<div class="listing-prompt"><span class="gt">></span> ls -la archive/</div>

<div class="page-content">

{% assign postsByYear = site.posts | group_by_exp: "post", "post.date | date: '%Y'" %}
{% for year in postsByYear %}
<h2>{{ year.name }}</h2>
<ul class="post-list">
  {% for post in year.items %}
  <li class="post-item">
    <span class="post-date">{{ post.date | date: "%Y-%m-%d" }}</span>
    <a href="{{ post.url | prepend: site.baseurl | replace: '//', '/' }}" class="post-title-link">{{ post.title }}</a>
  </li>
  {% endfor %}
</ul>
{% endfor %}

</div>
