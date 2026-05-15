---
layout: page
title: To Study
permalink: /to-study/
---

앞으로 읽고 싶거나 더 깊게 공부해 보고 싶은 주제를 모아두는 페이지입니다.

<ul class="archive-list">
  {% assign todo_posts = site.posts | where_exp: "post", "post.categories contains 'todo-study'" %}
  {% if todo_posts.size > 0 %}
    {% for post in todo_posts %}
      <li class="archive-item">
        <p class="archive-meta">{{ post.date | date: "%Y.%m.%d" }} · {{ post.categories | join: " / " }}</p>
        <h2 class="archive-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
        {% if post.excerpt %}
          <p class="archive-excerpt">{{ post.excerpt | strip_html }}</p>
        {% endif %}
      </li>
    {% endfor %}
  {% else %}
    <li class="archive-item archive-empty">아직 To Study 카테고리 글이 없습니다.</li>
  {% endif %}
</ul>
