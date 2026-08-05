---
layout: page
title: 공부한 것
permalink: /study/
eyebrow: 학습 아카이브
---

공부하면서 이해한 내용과 정리한 개념을 모아두는 페이지입니다.

<ul class="archive-list">
  {% assign study_posts = site.posts | where_exp: "post", "post.categories contains 'study'" %}
  {% if study_posts.size > 0 %}
    {% for post in study_posts %}
      <li class="archive-item">
        <p class="archive-meta">{{ post.date | date: "%Y.%m.%d" }} · {{ post.categories | join: " / " }}</p>
        <h2 class="archive-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
        {% if post.excerpt %}
          <p class="archive-excerpt">{{ post.excerpt | strip_html }}</p>
        {% endif %}
      </li>
    {% endfor %}
  {% else %}
    <li class="archive-item archive-empty">아직 공부한 것 카테고리 글이 없습니다.</li>
  {% endif %}
</ul>
