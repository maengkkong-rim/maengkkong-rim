---
layout: category
title: Paper review
---
<h1 class="post-title">{{ page.title }}</h1>

{% if site.posts.size > 0 %}
  <ul>
    {% for post in site.posts reversed %}
      {% if post.categories == "paper-review" %}
        <li class="post-list-item">
          <span class="home-date">
            {{ post.date | date: site.theme_config.date_format }}»
          </span>
          <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        </li>
      {% endif %}
    {% endfor %}
  </ul>
{% endif %}
