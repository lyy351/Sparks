---
layout: page
title: 分类
permalink: /category/
---

<!-- 全文搜索框（无放大镜） -->
<div style="margin-bottom: 30px;">
    <input type="text" id="search-input" placeholder="🔍 搜索文章标题或内容..." 
           style="width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 30px; font-size: 16px; box-sizing: border-box; -webkit-appearance: none; -moz-appearance: none; appearance: none;">
</div>
<div id="search-results" style="margin-top: 20px;"></div>

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

<!-- JavaScript 控制多级筛选和折叠（原有） -->
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

<!-- 全文搜索功能（带高亮） -->
<script>
  let searchIndex = [];

  fetch('{{ site.baseurl }}/search.json')
    .then(response => response.json())
    .then(data => {
      searchIndex = data;
      console.log('搜索索引加载完成，共', searchIndex.length, '篇文章');
    })
    .catch(error => console.error('搜索索引加载失败', error));

  // 转义正则特殊字符，用于安全的高亮匹配
  function escapeRegExp(string) {
    return string.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
  }

  // 高亮文本中的关键词（不区分大小写）
  function highlight(text, keyword) {
    if (!keyword || !text) return text;
    const escapedKeyword = escapeRegExp(keyword);
    const regex = new RegExp(`(${escapedKeyword})`, 'gi');
    return text.replace(regex, '<mark>$1</mark>');
  }

  document.getElementById('search-input').addEventListener('input', function(e) {
    const keyword = this.value.toLowerCase().trim();
    const resultsDiv = document.getElementById('search-results');
    
    if (keyword.length < 2) {
      resultsDiv.innerHTML = '';
      return;
    }
    
    const results = searchIndex.filter(post => {
      return post.title.toLowerCase().includes(keyword) || 
             post.content.toLowerCase().includes(keyword) ||
             post.excerpt.toLowerCase().includes(keyword);
    });
    
    if (results.length === 0) {
      resultsDiv.innerHTML = '<p style="color: #999;">没有找到相关文章</p>';
      return;
    }
    
    let html = '';
    results.slice(0, 20).forEach(post => {
      const highlightedTitle = highlight(post.title, keyword);
      const highlightedExcerpt = highlight(post.excerpt, keyword);
      
      html += `
        <div class="search-result-item">
          <h4><a href="${post.url}" class="search-result-title">${highlightedTitle}</a> <span class="search-result-date">${post.date}</span></h4>
          <div class="search-result-excerpt">${highlightedExcerpt}</div>
        </div>
      `;
    });
    
    resultsDiv.innerHTML = html;
  });
</script>

<!-- 样式（整合原有筛选样式 + 搜索结果样式） -->
<style>
  /* ===== 原有样式 ===== */
  .category-filter { margin-bottom: 20px; text-align: center; }
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
  .filter-btn:hover { background-color: #e0e0e0; }
  .filter-btn.active { background-color: #666; color: #fff; font-weight: bold; }

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
  .filter-header h3 { margin: 0; font-size: 16px; color: #666; }
  .toggle-btn {
    background: none;
    border: 1px solid #ccc;
    padding: 2px 8px;
    border-radius: 12px;
    cursor: pointer;
    font-size: 12px;
  }
  .toggle-btn:hover { background-color: #f0f0f0; }

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
  .secondary-btn:hover { background-color: #e0e0e0; }
  .secondary-btn.active { background-color: #666; color: #fff; font-weight: bold; }

  .category-section { margin-bottom: 30px; }
  .category-posts {
    list-style: none;
    padding-left: 0;
  }
  .category-posts li {
    border-bottom: 1px dashed #ddd;
  }
  .category-posts li:before { content: none !important; }
  .post-item { padding: 10px 0; }
  .post-item time {
    float: right;
    color: #999;
    font-size: 13px;
  }
  .post-tags, .post-attributes { margin-top: 5px; font-size: 12px; }
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
  .attr-link { background-color: #ffe6f0; }

  /* ===== 搜索结果样式（带段落感） ===== */
  #search-results {
    margin-bottom: 30px;
    border-bottom: 1px solid #eee;
  }
  .search-result-item {
    margin-bottom: 20px;
    padding-bottom: 15px;
    border-bottom: 1px dashed #ddd;
  }
  .search-result-item h4 {
    margin: 0 0 8px;
    font-size: 18px;
  }
  .search-result-title {
    color: #333;
    text-decoration: none;
    font-weight: 500;
  }
  .search-result-title:hover { text-decoration: underline; }
  .search-result-date {
    font-size: 13px;
    font-weight: normal;
    color: #999;
    margin-left: 10px;
  }
  .search-result-excerpt {
    font-size: 14px;
    color: #666;
    background-color: #f9f9f9;
    padding: 8px 12px;
    border-radius: 6px;
    /* 增加段落感 */
    text-indent: 2em;        /* 首行缩进 */
    line-height: 1.6;        /* 行高 */
    margin-bottom: 10px;     /* 段落间距 */
    white-space: pre-wrap;   /* 保留换行（如果有） */
  }
  /* 高亮样式 */
  mark {
    background-color: #ffecb3;  /* 淡黄色背景 */
    padding: 2px 0;
    border-radius: 2px;
    font-weight: 500;
  }
</style>
