---
layout: default
title: Blog
permalink: /blog/
---

# 📖 Blog

Announcements of new [4D components](/) surfacing from the deep. Old components don't get a
post — only the fresh arrivals.

Looking for AI / Swift / 4D notes too? They live on my other blog:
[**phimage.github.io**](https://phimage.github.io/).

---

{% for post in site.posts %}
### [{{ post.title }}]({{ post.url | relative_url }})
<small>{{ post.date | date: "%B %-d, %Y" }}{% if post.component %} · 🧩 {{ post.component }}{% if post.version %} <code>{{ post.version }}</code>{% endif %}{% endif %}</small>

{{ post.excerpt | strip_html | strip_newlines | truncate: 200 }}
{% else %}
_No posts yet. Stay tuned — something is rising from the twilight zone._
{% endfor %}

---

[← Home](/)
