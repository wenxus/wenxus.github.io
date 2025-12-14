---
layout: default
title: Research
permalink: /research/
---

<h1 style="text-align: center;">"Any sufficiently advanced technology is indistinguishable from magic." </h1>

<h2 style="text-align: center;"> Arthur C. Clarke, <em>Profiles of the Future</em></h2>

<p>I'm actively conducting research on AI safety and alignment, focusing on making AI systems more transparent and controllable.</p>

<div class="post-grid research-grid">
  {% for post in site.categories.research %}
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