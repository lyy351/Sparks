---
layout: page
title: 关于
permalink: /about/
---

这是一个关于页面，请编辑about.md以介绍你的博客。

## 📚 文章索引

### 按分类浏览
{% for category in site.categories %}
- [**{{ category[0] }}**]({{ site.baseurl }}/category#{{ category[0] | slugify }}) ({{ category[1] | size }}篇)
{% endfor %}

### 按标签浏览
<div class="tag-cloud">
  {% for tag in site.tags %}
  <a href="{{ site.baseurl }}/tags#{{ tag[0] | slugify }}" style="display:inline-block; margin-right:10px;">#{{ tag[0] }} ({{ tag[1] | size }})</a>
  {% endfor %}
</div>

### 最新文章
<ul>
{% for post in site.posts limit:5 %}
  <li>
    <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
    <span style="color:#999;">{{ post.date | date: "%Y-%m-%d" }}</span>
  </li>
{% endfor %}
</ul>
