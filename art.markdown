---
layout: default
title: Art
permalink: /art/
---

# "My painting is what I have to give back to the world for what the world gives to me."
## -- Georgia O'Keeffe


<div class="post-grid">
  {% for post in site.categories.art %}
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