---
layout: default
title: 병원 IT 26년의 회고
permalink: /hospital-it-26y/
memoir_scope: true
description: 지방 대학병원 IT를 개발자·팀장·본부장으로 26년을 지나오며 실제로 만든 시스템들의 기록.
---

<section class="page-shell container">
  <header class="memoir-header">
    <p class="memoir-kicker">Series · 회고</p>
    <h1 class="memoir-title">병원 IT 26년의 회고</h1>
    <p class="memoir-meta">종이 청구서로 시작해 바이브코딩으로 끝나가는 26년</p>
  </header>

  <div class="memoir-lede">
    <p>
      지방 대학병원 IT를 개발자에서 팀장, 본부장까지 지나오며 실제로 만든 시스템들을
      지금의 시선으로 다시 정리하는 기록입니다.
      관리자가 되어도 손을 놓지 않고 계속 개발해 온 12년의 시간이 이 시리즈의 재료가 됩니다.
    </p>
    <p>
      각 편은 그 시기의 대표 시스템 하나를 축으로 삼습니다. 시대는 자연스럽게 따라옵니다.
    </p>
  </div>
</section>

<section class="section container">
  {% assign episodes = site.posts | where: "series", "hospital-it-26y" | sort: "episode" %}
  {% assign planned = "나이키 신발공장 POP — 병원 이전의 개발자|파워빌더와 Sybase ASE로 지은 첫 병원 업무 시스템|파워빌더 시대 후반 — 규모가 커진 뒤|병원의 첫 안드로이드 네이티브 앱 (2012)|플러터와 플러터플로로 다시 만든 병원 모바일|스프링과 Vaadin으로 지은 병원 웹|LLM이 도착하는 지금, 그리고 다음 10년" | split: "|" %}

  <p class="section-label">기록</p>
  <ol class="episode-list">
    {% for post in episodes %}
      <li class="episode">
        <span class="episode-no">{% if post.episode != nil %}{{ post.episode }}편{% else %}—{% endif %}</span>
        <div>
          <h3 class="episode-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
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
      {% assign ep_no = forloop.index %}
      <li class="episode episode-planned">
        <span class="episode-no">{{ ep_no }}편</span>
        <div>
          <h3 class="episode-title">{{ title }}</h3>
          <p class="episode-date">예정</p>
        </div>
      </li>
    {% endfor %}
  </ol>
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
