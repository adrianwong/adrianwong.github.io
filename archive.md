---
layout: page
title: Archive
---

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a> &middot; {{ post.date | date_to_string }}
    </li>
  {% endfor %}
</ul>
