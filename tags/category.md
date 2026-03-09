---
layout: page
title: 分类
permalink: /category/
---

<!-- ===== 全局搜索框（可折叠） ===== -->
<div class="global-search">
  <div class="search-toggle" onclick="toggleSearch()">🔍 搜索文章</div>
  <div id="search-container" style="display: none;">
    <input type="text" id="search-input" placeholder="输入关键词，搜索全文..." autocomplete="off">
    <div id="search-results" style="display: none;"></div>
  </div>
</div>

<script>
function toggleSearch() {
  var container = document.getElementById('search-container');
  var toggle = document.querySelector('.search-toggle');
  if (container.style.display === 'none') {
    container.style.display = 'block';
    toggle.textContent = '✕ 关闭搜索';
    document.getElementById('search-input').focus();
  } else {
    container.style.display = 'none';
    toggle.textContent = '🔍 搜索文章';
  }
}
</script>

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

<!-- 引入 Simple-Jekyll-Search 库（内联，确保稳定） -->
<script>
/*! simple-jekyll-search 1.10.0 */
!function(){"use strict";var e={trim:function(e){return e.replace(/^\s+|\s+$/g,"")},isArray:function(e){return Array.isArray?Array.isArray(e):"[object Array]"===Object.prototype.toString.call(e)},isFunction:function(e){return"function"==typeof e},toArray:function(e){return Array.prototype.slice.call(e)},map:function(e,t){if(e.map)return e.map(t);for(var n=[],r=0;r<e.length;r++)n.push(t(e[r],r));return n},filter:function(e,t){if(e.filter)return e.filter(t);for(var n=[],r=0;r<e.length;r++)t(e[r],r)&&n.push(e[r]);return n},keys:function(e){if(Object.keys)return Object.keys(e);var t=[];for(var n in e)e.hasOwnProperty(n)&&t.push(n);return t}},t=function(e,t){t=t||{};var n=this;n.initialize=function(){n.options=t;var r=t.searchInput;if(n.$searchInput="string"==typeof r?document.querySelector(r):r,n.$searchInput)if(n.options.resultsContainer="string"==typeof t.resultsContainer?document.querySelector(t.resultsContainer):t.resultsContainer,n.$resultsContainer){var i=n.options.source;if(e.isFunction(i))n.searchFn=i;else if(e.isArray(i)){var o=n.options;n.searchFn=function(e){return function(t){return e.search(t,o)}}(i)}else{var s=n.options;n.searchFn=function(e){return function(t){return e.search(t,s)}}(i)}n.bindEvent()}else throw new Error("SimpleJekyllSearch: resultsContainer not found");else throw new Error("SimpleJekyllSearch: searchInput not found")},n.bindEvent=function(){n.$searchInput.addEventListener("input",function(e){var t=e.target.value;n.options.fuzzy&&(t=n.addFuzzy(t));var r=n.searchFn(t);if(e.isFunction(n.options.renderResults))n.options.renderResults(r);else{var i=n.options;n.renderResults(r,i)}})},n.addFuzzy=function(e){var t=n.options.fuzzy;return"."===t||","===t?e+"[^"+t+"]*"+t:e+t},n.renderResults=function(t,r){var i=r.resultsContainer;n.clearResults(i);if(t.length){var o=e.map(t,function(e){return r.resultTemplate.replace(/\{(\w*)\}/g,function(t,n){return e[n]?e[n]:""})}).join("");i.insertAdjacentHTML("beforeend",o)}else{var s=r.noResultsText||"No results found";i.insertAdjacentHTML("beforeend","<li>"+s+"</li>")}},n.clearResults=function(e){for(;e.firstChild;)e.removeChild(e.firstChild)},n.initialize()},window.SimpleJekyllSearch=function(e){return new t(window.simpleJekyllSearch||{},e)}}();
</script>

<!-- JavaScript 控制多级筛选、折叠和全局搜索（简化版，确保搜索稳定） -->
<script>
  document.addEventListener('DOMContentLoaded', function() {
    // ---------- 原有的筛选逻辑 ----------
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

    // ---------- 初始化全局搜索（简单模板，确保稳定） ----------
    var searchInput = document.getElementById('search-input');
    if (searchInput) {
      SimpleJekyllSearch({
        searchInput: searchInput,
        resultsContainer: document.getElementById('search-results'),
        json: '{{ site.baseurl }}/tags/search.json',
        searchResultTemplate: '<li><a href="{url}">{title}</a> <span class="result-date">({date})</span></li>',
        noResultsText: '<li>没有找到相关文章</li>',
        limit: 10,
        fuzzy: false
      });
    } else {
      console.error('搜索框未找到！');
    }
  });
</script>

<!-- 样式（保持不变） -->
<style>
  /* ===== 全局搜索框样式 ===== */
  .global-search {
    margin: 0 0 30px;
    padding: 15px;
    background-color: #ffffff;
    border: 1px solid #ddd;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.02);
  }

  .search-toggle {
    display: inline-block;
    padding: 8px 16px;
    background-color: #f0f0f0;
    border-radius: 30px;
    color: #333;
    font-size: 14px;
    cursor: pointer;
    user-select: none;
    transition: all 0.2s ease;
  }
  .search-toggle:hover {
    background-color: #e0e0e0;
  }

  #search-container {
    margin-top: 15px;
  }

  #search-input {
    width: 100%;
    padding: 12px 16px;
    font-size: 16px;
    border: 1px solid #ddd;
    border-radius: 30px;
    outline: none;
    transition: all 0.2s ease;
    box-sizing: border-box;
  }
  #search-input:focus {
    border-color: #999;
    box-shadow: 0 0 0 3px rgba(0,0,0,0.05);
  }

  #search-results {
    margin-top: 20px;
    max-height: 500px;
    overflow-y: auto;
    padding-right: 10px;
    list-style: none;
    padding-left: 0;
  }
  #search-results li {
    margin-bottom: 10px;
    padding-bottom: 5px;
    border-bottom: 1px dashed #ddd;
  }
  #search-results li a {
    color: #333;
    text-decoration: none;
    font-weight: 500;
  }
  #search-results li a:hover {
    text-decoration: underline;
  }
  .result-date {
    color: #999;
    font-size: 12px;
    margin-left: 8px;
  }

  /* ===== 原有样式 ===== */
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
