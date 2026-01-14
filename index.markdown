---
layout: default
title: "Home"
---

# Welcome to {{ site.title }}

This is my personal space on the web. I write articles, share projects, and document my journey.

## Latest Articles
<ul class="article-list">
{% for post in site.posts limit:3 %}
<li>
  <a href="{{ post.url }}"><strong>{{ post.title }}</strong></a><br>
  <small>{{ post.date | date: "%B %d, %Y" }}</small><br>
  {{ post.excerpt | strip_html | truncatewords: 30 }}
</li>
{% endfor %}
</ul>

[View all articles →](/articles/)

## Featured Projects
Check out my [Projects](/projects/) page to see what I'm working on.