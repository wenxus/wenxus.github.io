---
layout: default
title: Lifting
permalink: /lifting/
---

# Lifting Posts

<div class="post-grid">
  {% for post in site.categories.lifting %}
    <div class="post-item">
      <a href="{{ post.url }}">
        {% if post.thumbnail %}
          <img src="{{ post.thumbnail }}" alt="{{ post.title }} thumbnail">
        {% endif %}
        <h2>{{ post.title }}</h2>
      </a>
      <p>{{ post.date | date: "%B %d, %Y" }}</p>
    </div>
  {% endfor %}
</div>