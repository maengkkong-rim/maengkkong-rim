---
layout: category
title: Paper review
---
<h1 class="post-title">{{ page.title }}</h1><br>

{% if site.posts.size > 0 %}
  <ul>
    {% assign pap = site.posts | where: "categories", "paper-review" %}
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
