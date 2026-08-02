---
layout: page
title: Notes
permalink: /notes/
---

정식 학습 노트로 정리하기 전 단계의 짧은 메모, 작업 중 흘려두기 아까운 생각을 모아두는 페이지입니다.

<ul class="archive-list">
  {% assign note_posts = site.posts | where_exp: "post", "post.categories contains 'notes'" %}
  {% if note_posts.size > 0 %}
    {% for post in note_posts %}
      <li class="archive-item">
        <p class="archive-meta">{{ post.date | date: "%Y.%m.%d" }} · {{ post.categories | join: " / " }}</p>
        <h2 class="archive-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
        {% if post.excerpt %}
          <p class="archive-excerpt">{{ post.excerpt | strip_html }}</p>
        {% endif %}
      </li>
    {% endfor %}
  {% else %}
    <li class="archive-item archive-empty">
      아직 짧은 메모로 저장한 글이 없습니다. 정식 학습 노트는 <a href="{{ '/study/' | relative_url }}">Study</a>에서 볼 수 있습니다.
    </li>
  {% endif %}
</ul>
