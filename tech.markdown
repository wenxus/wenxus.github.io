---
layout: default
title: Tech
permalink: /tech/
---

<h1 style="text-align: center;">"Any sufficiently advanced technology is indistinguishable from magic." </h1>

<h2 style="text-align: center;"> Arthur C. Clarke, <em>Profiles of the Future</em></h2>

<div class="post-grid">
  {% for post in site.categories.tech %}
    <div class="post-item">
      <a href="{{ post.url }}">
        {% if post.thumbnail %}
          <img src="{{ post.thumbnail }}" alt="{{ post.title }} thumbnail">
        {% endif %}
        <div class="post-content">
          <h3 class="post-title">{{ post.title }}</h3>
          <p class="post-description">{{ post.date | date: "%B %d, %Y" }}</p>
          </div>
      </a>
    </div>
  {% endfor %}
</div>

<link rel="stylesheet" href="/assets/css/style.css">