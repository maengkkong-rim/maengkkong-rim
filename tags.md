---
layout: tag
title: Tags
permalink: /tags/
---
<br>
{% assign rawtags = "" %}
{% for post in site.posts %}
{% assign ttags = post.tags | join:'|' | append:'|' %}
{% assign rawtags = rawtags | append:ttags %}
{% endfor %}
{% assign rawtags = rawtags | split:'|' | sort %}
{% assign tags = "" %}
{% for tag in rawtags %}
{% if tag != "" %}
{% if tags == "" %}
{% assign tags = tag | split:'|' %}
{% endif %}
{% unless tags contains tag %}
{% assign tags = tags | join:'|' | append:'|' | append:tag | split:'|' %}
{% endunless %}
{% endif %}
{% endfor %}
{% for tag in tags %}



{% var i=0 %}
{% for post in site.posts %}
  {% if post.tags contains tag %}
    {% i = i+1 %}
  {% endif %}
{% endfor %}



<a class="label" href="#{{ tag | slugify }}" >#{{ tag }[{{ i }}]</a>
{% endfor %}
<hr class="divider">

{% for tag in tags %}
<h4 id="{{ tag | slugify }}">{{ tag }}</h4>
<ul>
  {% for post in site.posts %}
  {% if post.tags contains tag %}
  <li>
      <a href="{{ post.url | prepend: site.baseurl }}">
        {{ post.title }}
      </a>
  </li>
  {% endif %}
  {% endfor %}
</ul>
{% endfor %}
