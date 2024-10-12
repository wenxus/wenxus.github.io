---
layout: default
title: Outdoors
permalink: /outdoors/
---

<h2 style="text-align: center;">"Where every way will go, trails become unnecessary. Every hiker takes a personal route here, never repeated by anyone else. That individual route makes it your place. Your life can begin to look like that. </h2>
<h2 style="text-align: center;">When is it ever different?"</h2>

<h1 style="text-align: center;">Kim Stanley Robinson, <em>The High Sierra</em></h1>

<!-- <div class="post-grid">
  {% for post in site.categories.outdoors %}
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
</div> -->

<div class="post-grid">
  {% for post in site.categories.outdoors %}
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