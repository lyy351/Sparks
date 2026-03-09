---
layout: page
title: 分类
permalink: /category/
---

<!-- 一级分类筛选按钮区（不变） -->
<div class="category-filter">
  <button class="filter-btn active" data-category="all">全部</button>
  {% for category in site.categories %}
    <button class="filter-btn" data-category="{{ category[0] | slugify }}">
      {{ category[0] }} ({{ category[1] | size }})
    </button>
  {% endfor %}
</div>

<!-- 二级标签按钮区（根据当前一级分类动态显示） -->
<div id="tag-filter" class="secondary-filter" style="display: none;">
  <h3>角色/CP</h3>
  <div class="tag-buttons"></div>
</div>

<!-- 三级属性按钮区（根据当前一级分类动态显示） -->
<div id="attr-filter" class="secondary-filter" style="display: none;">
  <h3>题材/风格</h3>
  <div class="attr-buttons"></div>
</div>

<!-- 文章列表区 -->
<div class="categories">
  {% for category in site.categories %}
    {% assign current_category = category[0] %}
    <div class="category-section" data-category="{{ current_category | slugify }}">
      <h2 id="{{ current_category | slugify }}">{{ current_category }}</h2>
      <ul class="category-posts">
        {% for post in category[1] %}
          <li>
            <div class="post-item" 
                 data-tags="{% for tag in post.tags %}{{ tag | slugify }} {% endfor %}"
                 data-attrs="{% for attr in post.attributes %}{{ attr | slugify }} {% endfor %}">
              <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
              <time>{{ post.date | date: "%Y-%m-%d" }}</time>

              <!-- 显示这篇文章的其他标签（二级） -->
              {% if post.tags.size > 0 %}
                <div class="post-tags">
                  {% for tag in post.tags %}
                    <a href="{{ site.baseurl }}/tags#{{ tag | slugify }}" class="tag-link">{{ tag }}</a>
                  {% endfor %}
                </div>
              {% endif %}

              <!-- 显示这篇文章的属性（三级） -->
              {% if post.attributes.size > 0 %}
                <div class="post-attributes">
                  <span class="meta-label">属性：</span>
                  {% for attr in post.attributes %}
                    <a href="#" class="attr-link">{{ attr }}</a>
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

<!-- JavaScript 控制多级筛选 -->
<script>
  document.addEventListener('DOMContentLoaded', function() {
    const categoryButtons = document.querySelectorAll('.filter-btn');
    const categorySections = document.querySelectorAll('.category-section');
    const tagFilterDiv = document.getElementById('tag-filter');
    const attrFilterDiv = document.getElementById('attr-filter');
    const tagButtonsDiv = document.querySelector('.tag-buttons');
    const attrButtonsDiv = document.querySelector('.attr-buttons');
    
    let currentCategory = 'all';
    let currentTag = null;
    let currentAttr = null;

    // 收集所有文章数据（用于筛选）
    const allPosts = [];
    document.querySelectorAll('.post-item').forEach((item, index) => {
      allPosts.push({
        element: item,
        parentLi: item.closest('li'),
        parentSection: item.closest('.category-section'),
        category: item.closest('.category-section').dataset.category,
        tags: (item.dataset.tags || '').trim().split(/\s+/).filter(t => t),
        attrs: (item.dataset.attrs || '').trim().split(/\s+/).filter(a => a)
      });
    });

    // 更新文章显示状态
    function updatePosts() {
      allPosts.forEach(post => {
        const matchesCategory = currentCategory === 'all' || post.category === currentCategory;
        const matchesTag = !currentTag || post.tags.includes(currentTag);
        const matchesAttr = !currentAttr || post.attrs.includes(currentAttr);

        if (matchesCategory && matchesTag && matchesAttr) {
          post.parentLi.style.display = '';
        } else {
          post.parentLi.style.display = 'none';
        }
      });

      // 更新分类区块的显示（如果该分类下没有可见文章，隐藏整个区块）
      categorySections.forEach(section => {
        const visiblePosts = Array.from(section.querySelectorAll('li')).filter(li => li.style.display !== 'none');
        section.style.display = visiblePosts.length > 0 ? 'block' : 'none';
      });
    }

    // 根据当前一级分类生成二级、三级按钮
    function generateSecondaryFilters(category) {
      const tagsSet = new Set();
      const attrsSet = new Set();

      allPosts.forEach(post => {
        if (category === 'all' || post.category === category) {
          post.tags.forEach(t => tagsSet.add(t));
          post.attrs.forEach(a => attrsSet.add(a));
        }
      });

      // 生成标签按钮
      tagButtonsDiv.innerHTML = '';
      if (tagsSet.size > 0) {
        tagFilterDiv.style.display = 'block';
        const allTagBtn = document.createElement('button');
        allTagBtn.className = 'secondary-btn active';
        allTagBtn.dataset.tag = '';
        allTagBtn.textContent = '全部';
        tagButtonsDiv.appendChild(allTagBtn);

        Array.from(tagsSet).sort().forEach(tag => {
          const btn = document.createElement('button');
          btn.className = 'secondary-btn';
          btn.dataset.tag = tag;
          btn.textContent = tag;
          tagButtonsDiv.appendChild(btn);
        });
      } else {
        tagFilterDiv.style.display = 'none';
      }

      // 生成属性按钮
      attrButtonsDiv.innerHTML = '';
      if (attrsSet.size > 0) {
        attrFilterDiv.style.display = 'block';
        const allAttrBtn = document.createElement('button');
        allAttrBtn.className = 'secondary-btn active';
        allAttrBtn.dataset.attr = '';
        allAttrBtn.textContent = '全部';
        attrButtonsDiv.appendChild(allAttrBtn);

        Array.from(attrsSet).sort().forEach(attr => {
          const btn = document.createElement('button');
          btn.className = 'secondary-btn';
          btn.dataset.attr = attr;
          btn.textContent = attr;
          attrButtonsDiv.appendChild(btn);
        });
      } else {
        attrFilterDiv.style.display = 'none';
      }

      // 为二级按钮绑定事件
      document.querySelectorAll('[data-tag]').forEach(btn => {
        btn.addEventListener('click', function() {
          document.querySelectorAll('[data-tag]').forEach(b => b.classList.remove('active'));
          this.classList.add('active');
          currentTag = this.dataset.tag || null;
          updatePosts();
        });
      });

      // 为三级按钮绑定事件
      document.querySelectorAll('[data-attr]').forEach(btn => {
        btn.addEventListener('click', function() {
          document.querySelectorAll('[data-attr]').forEach(b => b.classList.remove('active'));
          this.classList.add('active');
          currentAttr = this.dataset.attr || null;
          updatePosts();
        });
      });
    }

    // 一级分类按钮点击事件
    categoryButtons.forEach(button => {
      button.addEventListener('click', function() {
        categoryButtons.forEach(btn => btn.classList.remove('active'));
        this.classList.add('active');

        currentCategory = this.dataset.category;
        currentTag = null;
        currentAttr = null;

        // 重新生成二级、三级按钮
        generateSecondaryFilters(currentCategory);

        // 先显示所有文章（由 generateSecondaryFilters 内的按钮事件负责筛选）
        // 但需要先重置显示
        allPosts.forEach(post => {
          post.parentLi.style.display = '';
        });
        updatePosts();
      });
    });

    // 初始化：默认选中“全部”，生成二级按钮
    generateSecondaryFilters('all');
  });
</script>

<!-- 样式（建议移到 style.scss） -->
<style>
  .secondary-filter {
    margin: 20px 0;
    padding: 15px;
    background-color: #f9f9f9;
    border-radius: 8px;
  }
  .secondary-filter h3 {
    margin-top: 0;
    margin-bottom: 10px;
    font-size: 16px;
    color: #666;
  }
  .secondary-btn {
    display: inline-block;
    margin: 0 5px 10px 0;
    padding: 4px 10px;
    background-color: #f0f0f0;
    border: none;
    border-radius: 16px;
    color: #333;
    font-size: 13px;
    cursor: pointer;
  }
  .secondary-btn:hover {
    background-color: #e0e0e0;
  }
  .secondary-btn.active {
    background-color: #666;
    color: #fff;
    font-weight: bold;
  }
  .post-item {
    padding: 10px 0;
  }
  .post-item time {
    float: right;
    color: #999;
    font-size: 13px;
  }
  .post-tags, .post-attributes {
    margin-top: 5px;
    font-size: 12px;
  }
  .tag-link, .attr-link {
    display: inline-block;
    margin-right: 5px;
    padding: 2px 6px;
    background-color: #f0f0f0;
    border-radius: 12px;
    color: #333;
    text-decoration: none;
    font-size: 11px;
  }
  .attr-link {
    background-color: #ffe6f0;
  }
  .category-section {
    margin-bottom: 30px;
  }
  .category-posts {
    list-style: none;
    padding-left: 0;
  }
  .category-posts li {
    border-bottom: 1px dashed #ddd;
  }
  .category-posts li:before {
    content: none !important;
  }
</style>
