---
layout: category
title: Schedules
---
{% assign pap = site.posts | where: "categories", "schedules" %}
<h1 class="post-title">{{ page.title }} [{{ pap.size }}]</h1><br>

{% if site.posts.size > 0 %}
  <ul>
    {% for post in pap reversed %}
      <li class="post-list-item">
        <span class="home-date">
          {{ post.date | date: site.theme_config.date_format }}»
        </span>
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      </li>
    {% endfor %}
  </ul>
{% endif %}
