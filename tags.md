---
layout: page
title: Tags
permalink: /tags/
---

주제별로 글을 모아 보는 페이지입니다.

{% assign rawtags = "" %}
{% for post in site.posts %}
  {% for tag in post.tags %}
    {% assign rawtags = rawtags | append: tag | append: ',' %}
  {% endfor %}
{% endfor %}
{% assign taglist = rawtags | split: ',' | uniq | sort %}

<nav class="tag-cloud" aria-label="태그 목록">
  {% for tag in taglist %}
    {% if tag != '' %}
      {% assign count = site.tags[tag] | size %}
      <a class="tag-chip" href="#{{ tag }}">#{{ tag }} <span class="tag-count">{{ count }}</span></a>
    {% endif %}
  {% endfor %}
</nav>

{% for tag in taglist %}
  {% if tag == '' %}{% continue %}{% endif %}
  {% assign posts = site.tags[tag] %}
  <section class="tag-section" id="{{ tag }}">
    <h2 class="tag-section-title">#{{ tag }} <span class="tag-section-count">{{ posts.size }}</span></h2>
    <ul class="archive-list compact-archive-list">
      {% for post in posts %}
        <li class="archive-item">
          <p class="archive-meta">{{ post.date | date: "%Y.%m.%d" }} · {{ post.categories | join: " / " }}</p>
          <h3 class="archive-title compact-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
          {% if post.excerpt %}
            <p class="archive-excerpt">{{ post.excerpt | strip_html }}</p>
          {% endif %}
        </li>
      {% endfor %}
    </ul>
  </section>
{% endfor %}
