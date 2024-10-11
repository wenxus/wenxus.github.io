---
layout: default
title: Tech
permalink: /tech/
---

<h1 style="text-align: center;">"Any sufficiently advanced technology is indistinguishable from magic." </h1>

<h2 style="text-align: center;"> Arthur C. Clarke, <em>Profiles of the Future</em></h2>

# Tech Posts

<div class="post-grid">
  {% for post in site.categories.tech %}
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