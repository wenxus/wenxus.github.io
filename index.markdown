---
layout: default
title: Home
---

<div class="post-grid">
  {% for post in site.posts %}
    <div class="post-item">
      <a href="{{ post.url }}">
        {% if post.thumbnail %}
          <img src="{{ post.thumbnail }}" alt="{{ post.title }} thumbnail">
        {% endif %}
        <div class="post-content">
          <h3 class="post-title">{{ post.title }}</h3>
          <p class="post-description">{% if post.subtitle %}{{ post.subtitle }}{% else %}{{ post.date | date: "%B %d, %Y" }}{% endif %}</p>
          </div>
      </a>
    </div>
  {% endfor %}
</div>

<link rel="stylesheet" href="/assets/css/style.css">

