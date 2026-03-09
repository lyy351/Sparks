---
layout: page
title: 分类
permalink: /category/
---

<div class="categories">
  <ul class="category-list">
    {% for category in site.categories %}
      <li>
        <a href="#{{ category[0] | slugify }}">
          <span>{{ category[0] }}</span>
          <span class="count">{{ category[1] | size }}</span>
        </a>
      </li>
    {% endfor %}
  </ul>

  {% for category in site.categories %}
    <h2 id="{{ category[0] | slugify }}">{{ category[0] }}</h2>
    <ul class="category-posts">
      {% for post in category[1] %}
        <li>
          <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
          <time>{{ post.date | date: "%Y-%m-%d" }}</time>
        </li>
      {% endfor %}
    </ul>
  {% endfor %}
</div>
