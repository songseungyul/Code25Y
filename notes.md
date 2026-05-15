---
layout: page
title: Notes
permalink: /notes/
---

짧은 작업 메모, 생각의 흐름, 기록해 두고 싶은 노트를 모아두는 페이지입니다.

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
    <li class="archive-item archive-empty">아직 Notes 카테고리 글이 없습니다.</li>
  {% endif %}
</ul>
