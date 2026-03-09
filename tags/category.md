---
layout: page
title: 分类
permalink: /category/
---

<!-- 添加一个简单的分类筛选按钮区 -->
<div class="category-filter">
  <button class="filter-btn active" data-category="all">全部</button>
  {% for category in site.categories %}
    <button class="filter-btn" data-category="{{ category[0] | slugify }}">
      {{ category[0] }} ({{ category[1] | size }})
    </button>
  {% endfor %}
</div>

<!-- 分类文章列表区 -->
<div class="categories">
  {% for category in site.categories %}
    <div class="category-section" data-category="{{ category[0] | slugify }}">
      <h2 id="{{ category[0] | slugify }}">{{ category[0] }}</h2>
      <ul class="category-posts">
        {% for post in category[1] %}
          <li>
            <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
            <time>{{ post.date | date: "%Y-%m-%d" }}</time>
          </li>
        {% endfor %}
      </ul>
    </div>
  {% endfor %}
</div>

<!-- 添加 JavaScript 实现筛选功能 -->
<script>
  document.addEventListener('DOMContentLoaded', function() {
    const filterButtons = document.querySelectorAll('.filter-btn');
    const categorySections = document.querySelectorAll('.category-section');

    filterButtons.forEach(button => {
      button.addEventListener('click', function() {
        // 移除所有按钮的 active 类
        filterButtons.forEach(btn => btn.classList.remove('active'));
        this.classList.add('active');

        const selectedCategory = this.dataset.category;

        // 显示/隐藏分类区块
        categorySections.forEach(section => {
          if (selectedCategory === 'all' || section.dataset.category === selectedCategory) {
            section.style.display = 'block';
          } else {
            section.style.display = 'none';
          }
        });
      });
    });
  });
</script>
