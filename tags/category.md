---
layout: page
title: 分类
permalink: /category/
---

<!-- 分类筛选按钮区 -->
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
            <div class="post-item">
              <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
              <time>{{ post.date | date: "%Y-%m-%d" }}</time>
              
              <!-- 新增：显示这篇文章的其他标签（排除当前分类本身） -->
              {% if post.tags.size > 0 %}
                <div class="post-tags">
                  <span class="tags-label">标签：</span>
                  {% for tag in post.tags %}
                    <!-- 只显示非当前分类的标签 -->
                    {% if tag != category[0] %}
                      <a href="{{ site.baseurl }}/tags#{{ tag | slugify }}" class="tag-link">#{{ tag }}</a>
                    {% endif %}
                  {% endfor %}
                </div>
              {% endif %}
            </div>
          </li>
        {% endfor %}
      </ul>
    </div>
  {% endfor %}
</div>

<!-- JavaScript 控制筛选功能（保持不变） -->
<script>
  document.addEventListener('DOMContentLoaded', function() {
    const filterButtons = document.querySelectorAll('.filter-btn');
    const categorySections = document.querySelectorAll('.category-section');

    filterButtons.forEach(button => {
      button.addEventListener('click', function() {
        filterButtons.forEach(btn => btn.classList.remove('active'));
        this.classList.add('active');

        const selectedCategory = this.dataset.category;

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
