---
layout: page
title: 分类
permalink: /category/
---

<!-- 一级分类筛选按钮区 -->
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
  <div class="filter-header">
    <h3>角色/CP</h3>
    <button class="toggle-btn" data-target="tag-buttons">收起</button>
  </div>
  <div class="tag-buttons filter-buttons expanded"></div>
</div>

<!-- 三级属性按钮区（根据当前一级分类动态显示） -->
<div id="attr-filter" class="secondary-filter" style="display: none;">
  <div class="filter-header">
    <h3>题材/风格</h3>
    <button class="toggle-btn" data-target="attr-buttons">收起</button>
  </div>
  <div class="attr-buttons filter-buttons expanded"></div>
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

<!-- JavaScript 控制多级筛选和折叠 -->
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

    const allPosts = [];
    document.querySelectorAll('.post-item').forEach(item => {
      allPosts.push({
        parentLi: item.closest('li'),
        parentSection: item.closest('.category-section'),
        category: item.closest('.category-section').dataset.category,
        tags: (item.dataset.tags || '').trim().split(/\s+/).filter(t => t),
        attrs: (item.dataset.attrs || '').trim().split(/\s+/).filter(a => a)
      });
    });

    function updatePosts() {
      allPosts.forEach(post => {
        const matchesCategory = currentCategory === 'all' || post.category === currentCategory;
        const matchesTag = !currentTag || post.tags.includes(currentTag);
        const matchesAttr = !currentAttr || post.attrs.includes(currentAttr);
        post.parentLi.style.display = matchesCategory && matchesTag && matchesAttr ? '' : 'none';
      });
      categorySections.forEach(section => {
        const visiblePosts = Array.from(section.querySelectorAll('li')).filter(li => li.style.display !== 'none');
        section.style.display = visiblePosts.length > 0 ? 'block' : 'none';
      });
    }

    function generateSecondaryFilters(category) {
      const tagsSet = new Set();
      const attrsSet = new Set();
      allPosts.forEach(post => {
        if (category === 'all' || post.category === category) {
          post.tags.forEach(t => tagsSet.add(t));
          post.attrs.forEach(a => attrsSet.add(a));
        }
      });

      // 标签按钮
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

      // 属性按钮
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

      // 绑定二级按钮事件
      document.querySelectorAll('[data-tag]').forEach(btn => {
        btn.addEventListener('click', function() {
          document.querySelectorAll('[data-tag]').forEach(b => b.classList.remove('active'));
          this.classList.add('active');
          currentTag = this.dataset.tag || null;
          updatePosts();
        });
      });

      // 绑定三级按钮事件
      document.querySelectorAll('[data-attr]').forEach(btn => {
        btn.addEventListener('click', function() {
          document.querySelectorAll('[data-attr]').forEach(b => b.classList.remove('active'));
          this.classList.add('active');
          currentAttr = this.dataset.attr || null;
          updatePosts();
        });
      });
    }

    categoryButtons.forEach(button => {
      button.addEventListener('click', function() {
        categoryButtons.forEach(btn => btn.classList.remove('active'));
        this.classList.add('active');
        currentCategory = this.dataset.category;
        currentTag = null;
        currentAttr = null;
        generateSecondaryFilters(currentCategory);
        allPosts.forEach(post => post.parentLi.style.display = '');
        updatePosts();
      });
    });

    generateSecondaryFilters('all');

    // 折叠/展开功能
    document.querySelectorAll('.toggle-btn').forEach(btn => {
      btn.addEventListener('click', function() {
        const targetClass = this.dataset.target;
        const targetDiv = document.querySelector('.' + targetClass);
        if (targetDiv.classList.contains('expanded')) {
          targetDiv.classList.remove('expanded');
          targetDiv.classList.add('collapsed');
          this.textContent = '展开';
        } else {
          targetDiv.classList.remove('collapsed');
          targetDiv.classList.add('expanded');
          this.textContent = '收起';
        }
      });
    });
  });
</script>

<!-- 样式（只保留筛选相关样式） -->
<style>
  /* ===== 原有样式（无搜索） ===== */
  .category-filter {
    margin-bottom: 20px;
    text-align: center;
  }
  .filter-btn {
    display: inline-block;
    margin: 0 5px 10px 5px;
    padding: 6px 12px;
    background-color: #f0f0f0;
    border: none;
    border-radius: 20px;
    color: #333;
    font-size: 14px;
    cursor: pointer;
  }
  .filter-btn:hover {
    background-color: #e0e0e0;
  }
  .filter-btn.active {
    background-color: #666;
    color: #fff;
    font-weight: bold;
  }

  .secondary-filter {
    margin: 20px 0;
    padding: 15px;
    background-color: #f9f9f9;
    border-radius: 8px;
  }
  .filter-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;
  }
  .filter-header h3 {
    margin: 0;
    font-size: 16px;
    color: #666;
  }
  .toggle-btn {
    background: none;
    border: 1px solid #ccc;
    padding: 2px 8px;
    border-radius: 12px;
    cursor: pointer;
    font-size: 12px;
  }
  .toggle-btn:hover {
    background-color: #f0f0f0;
  }

  .filter-buttons {
    max-height: none;
    overflow: hidden;
    transition: max-height 0.3s ease;
  }
  .filter-buttons.collapsed {
    max-height: 80px;
    overflow-y: auto;
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
    margin-bottom: 5px;
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
</style>
