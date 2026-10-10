---
layout: default
title: 다음 20년
permalink: /next-20y/
memoir_scope: true
description: 대학병원 IT에서 26년. 그 경험을 근거로 지금과 다음 20년에 대해 던져야 할 질문들을 정리합니다.
---

<section class="page-shell container">
  <header class="memoir-header reveal">
    <p class="memoir-kicker">Series · 다음 20년</p>
    <h1 class="memoir-title">26년을 지나왔고,<br />다음 20년을 묻습니다</h1>
    <p class="memoir-meta">종이 청구서로 시작해 바이브코딩 앞에 서 있는 26년</p>
  </header>

  <div class="memoir-lede reveal delay-1">
    <p>
      대학병원 IT에서 개발자로 시작해 팀장과 본부장까지 26년을 보냈습니다.
      관리자가 되고 나서도 코딩을 놓지 않은 12년이 여기에 섞여 있습니다.
    </p>
    <p>
      이 시리즈는 그 시간을 순서대로 늘어놓는 기록이 아닙니다.
      <strong>26년을 근거로 삼아, 지금과 앞으로에 대해 던져야 할 질문을 하나씩 다룹니다.</strong>
      답을 내리기보다 질문을 제대로 세우는 쪽에 가깝습니다.
    </p>
  </div>
</section>

<section class="section container">
  {% assign episodes = site.posts | where: "series", "next-20y" | sort: "episode" %}
  {% assign published_count = episodes | size %}
  {% assign planned = "20년 가는 시스템을 만든다는 건 무슨 뜻인가|관리자가 되어도 코딩을 놓지 않는 게 맞나|규제가 강한 조직에 새 기술은 어떻게 들어오는가|기술 부채는 누가, 언제 갚아야 하는가|남은 20년을 어디에 쓸 것인가" | split: "|" %}

  <p class="section-label reveal">질문</p>
  <ol class="episode-list reveal delay-1">
    {% for post in episodes %}
      <li class="episode">
        <span class="episode-no">{% if post.episode %}Q{{ post.episode }}{% else %}—{% endif %}</span>
        <div>
          <h2 class="episode-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
          <p class="episode-date">
            <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%Y년 %-m월 %-d일" }}</time>
          </p>
          {% if post.excerpt %}
            <p class="episode-excerpt">{{ post.excerpt | strip_html | truncate: 150 }}</p>
          {% endif %}
        </div>
      </li>
    {% endfor %}

    {% for title in planned %}
      {% assign q_no = forloop.index | plus: published_count %}
      <li class="episode episode-planned">
        <span class="episode-no">Q{{ q_no }}</span>
        <div>
          <h2 class="episode-title">{{ title }}</h2>
          <p class="episode-date">예정</p>
        </div>
      </li>
    {% endfor %}
  </ol>

  <p class="episode-note">순서와 개수는 쓰면서 조정됩니다.</p>
</section>

<section class="section-tight container">
  <div class="memoir-lede">
    <p class="section-label">이 시리즈에서 다루지 않을 것</p>
    <ul class="plain-list">
      <li>환자와 관련된 어떤 사례도 — 익명화해도 남기지 않습니다.</li>
      <li>재직 병원의 이름과 특정 프로젝트명</li>
      <li>벤더명·계약·가격 등 상거래 정보</li>
      <li>특정 인물에 대한 저격 — 판단은 남기되 사람은 익명화합니다.</li>
    </ul>
  </div>

  <p class="more-link">
    <a href="{{ '/' | relative_url }}">← 학습 기록으로 돌아가기</a>
  </p>
</section>
