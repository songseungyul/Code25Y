---
layout: page
title: 태그
permalink: /tags/
eyebrow: 주제별 탐색
---

주제별로 학습 기록을 모아 보는 페이지입니다.
회고 시리즈는 [별도 페이지]({{ '/hospital-it-26y/' | relative_url }})에서 이어집니다.

{% comment %} 회고 시리즈는 제외한다 — 학습 기록만 태그로 분류한다 {% endcomment %}
{% assign notes = site.posts | where_exp: "post", "post.series == nil" %}

{% assign rawtags = "" %}
{% for post in notes %}
  {% for tag in post.tags %}
    {% assign rawtags = rawtags | append: tag | append: ',' %}
  {% endfor %}
{% endfor %}
{% assign taglist = rawtags | split: ',' | uniq | sort %}

<nav class="tag-cloud" aria-label="태그 목록">
  {% for tag in taglist %}
    {% if tag == '' %}{% continue %}{% endif %}
    {% assign tagged = notes | where_exp: "post", "post.tags contains tag" %}
    <a class="tag-chip" href="#{{ tag }}">#{{ tag }} <span class="tag-count">{{ tagged.size }}</span></a>
  {% endfor %}
</nav>

{% for tag in taglist %}
  {% if tag == '' %}{% continue %}{% endif %}
  {% assign tagged = notes | where_exp: "post", "post.tags contains tag" %}
  <section class="tag-section" id="{{ tag }}">
    <h2 class="tag-section-title">#{{ tag }} <span class="tag-section-count">{{ tagged.size }}</span></h2>
    <ul class="archive-list">
      {% for post in tagged %}
        <li class="archive-item">
          <p class="archive-meta">
            <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%Y.%m.%d" }}</time>
            {% if post.tags and post.tags.size > 0 %} · {{ post.tags | join: " · " }}{% endif %}
          </p>
          <h3 class="archive-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
          {% if post.excerpt %}
            <p class="archive-excerpt">{{ post.excerpt | strip_html | truncate: 130 }}</p>
          {% endif %}
        </li>
      {% endfor %}
    </ul>
  </section>
{% endfor %}
