---
layout: page
title: Search
permalink: /search/
---

학습 기록을 제목, 요약, 카테고리, 태그 기준으로 검색할 수 있습니다.
회고 시리즈는 [별도 페이지]({{ '/hospital-it-26y/' | relative_url }})에서 순서대로 이어집니다.

<form class="search-shell" role="search" onsubmit="return false">
  <label class="search-label" for="search-input">검색어</label>
  <input id="search-input" name="q" class="search-input" type="search" placeholder="예: codex, 하네스, 오케스트레이션" autocomplete="off" />

  <div class="search-filter-row" role="group" aria-label="카테고리 필터">
    <button type="button" class="filter-chip is-active" data-filter="all" aria-pressed="true">전체</button>
    <button type="button" class="filter-chip" data-filter="study" aria-pressed="false">Study</button>
    <button type="button" class="filter-chip" data-filter="todo-study" aria-pressed="false">To Study</button>
  </div>

  <p class="search-help">두 글자 이상 입력하면 제목·요약·카테고리·태그에서 바로 찾습니다.</p>
  <p id="search-status" class="search-status" aria-live="polite">전체 글을 불러왔습니다.</p>

  <ul id="search-results" class="archive-list">
    {% comment %} 회고 시리즈는 제외한다 — 별도 공간으로 분리했다 {% endcomment %}
    {% assign searchable = site.posts | where_exp: "post", "post.series == nil" %}
    {% for post in searchable %}
      <li
        class="archive-item search-item"
        data-title="{{ post.title | downcase | escape }}"
        data-excerpt="{{ post.excerpt | strip_html | downcase | escape }}"
        data-categories="{{ post.categories | join: ' ' | downcase | escape }}"
        data-tags="{{ post.tags | join: ' ' | downcase | escape }}"
        data-url="{{ post.url | relative_url }}"
        data-date="{{ post.date | date: '%Y.%m.%d' }}"
        data-title-raw="{{ post.title | escape }}"
        data-excerpt-raw="{{ post.excerpt | strip_html | escape }}"
        data-categories-raw="{{ post.categories | join: ' / ' | escape }}"
        data-tags-raw="{{ post.tags | join: ', ' | escape }}"
      >
        <p class="archive-meta">{{ post.date | date: "%Y.%m.%d" }} · {{ post.categories | join: " / " }}{% if post.tags and post.tags.size > 0 %} · {{ post.tags | join: ", " }}{% endif %}</p>
        <h2 class="archive-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
        {% if post.excerpt %}
          <p class="archive-excerpt">{{ post.excerpt | strip_html }}</p>
        {% endif %}
      </li>
    {% endfor %}
  </ul>
</form>

<script>
  document.addEventListener('DOMContentLoaded', function () {
    var input = document.getElementById('search-input');
    var status = document.getElementById('search-status');
    var results = document.getElementById('search-results');
    var buttons = Array.prototype.slice.call(document.querySelectorAll('.filter-chip'));
    var items = Array.prototype.slice.call(document.querySelectorAll('.search-item')).map(function (item) {
      return {
        title: item.dataset.title || '',
        excerpt: item.dataset.excerpt || '',
        categories: item.dataset.categories || '',
        tags: item.dataset.tags || '',
        titleRaw: item.dataset.titleRaw || '',
        excerptRaw: item.dataset.excerptRaw || '',
        categoriesRaw: item.dataset.categoriesRaw || '',
        tagsRaw: item.dataset.tagsRaw || '',
        url: item.dataset.url || '#',
        date: item.dataset.date || '',
        element: item
      };
    });

    var filterLabels = {
      all: '전체',
      study: 'Study',
      'todo-study': 'To Study'
    };

    var params = new URLSearchParams(window.location.search);
    var initialQuery = params.get('q') || '';
    var initialFilter = params.get('category') || 'all';
    if (!filterLabels[initialFilter]) initialFilter = 'all';

    var activeFilter = initialFilter;
    input.value = initialQuery;
    setActiveButton(activeFilter);

    function setActiveButton(filter) {
      buttons.forEach(function (btn) {
        var active = btn.dataset.filter === filter;
        btn.classList.toggle('is-active', active);
        btn.setAttribute('aria-pressed', String(active));
      });
    }

    function escapeHtml(text) {
      return text
        .replace(/&/g, '&amp;')
        .replace(/</g, '&lt;')
        .replace(/>/g, '&gt;')
        .replace(/"/g, '&quot;')
        .replace(/'/g, '&#39;');
    }

    function escapeRegExp(text) {
      return text.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
    }

    function highlight(text, query) {
      var safeText = escapeHtml(text || '');
      if (!query || query.length < 2) return safeText;
      var regex = new RegExp('(' + escapeRegExp(query) + ')', 'ig');
      return safeText.replace(regex, '<mark>$1</mark>');
    }

    function syncUrl(query) {
      var next = new URLSearchParams();
      if (query && query.length >= 2) next.set('q', query);
      if (activeFilter !== 'all') next.set('category', activeFilter);
      var qs = next.toString();
      var newUrl = window.location.pathname + (qs ? '?' + qs : '');
      window.history.replaceState(null, '', newUrl);
    }

    function render() {
      var query = input.value.trim().toLowerCase();
      var visibleCount = 0;
      var html = '';

      items.forEach(function (item) {
        var haystack = [item.title, item.excerpt, item.categories, item.tags].join(' ');
        var categoryMatched = activeFilter === 'all' || item.categories.split(' ').indexOf(activeFilter) !== -1;
        var queryMatched = query.length < 2 || haystack.indexOf(query) !== -1;
        var matched = categoryMatched && queryMatched;

        if (!matched) return;
        visibleCount += 1;

        var meta = item.date + ' · ' + item.categoriesRaw;
        if (item.tagsRaw) meta += ' · ' + item.tagsRaw;

        html += '<li class="archive-item">';
        html += '<p class="archive-meta">' + escapeHtml(meta) + '</p>';
        html += '<h2 class="archive-title"><a href="' + item.url + '">' + highlight(item.titleRaw, query) + '</a></h2>';
        if (item.excerptRaw) {
          html += '<p class="archive-excerpt">' + highlight(item.excerptRaw, query) + '</p>';
        }
        html += '</li>';
      });

      if (visibleCount === 0) {
        html = '<li class="archive-item archive-empty">검색 조건에 맞는 글이 없습니다.</li>';
      }

      results.innerHTML = html;

      if (query.length < 2 && activeFilter === 'all') {
        status.textContent = '전체 글 ' + visibleCount + '개를 표시하고 있습니다. 두 글자 이상 입력하거나 카테고리를 고르면 바로 필터링됩니다.';
      } else if (query.length < 2) {
        status.textContent = filterLabels[activeFilter] + ' 카테고리 글 ' + visibleCount + '개를 표시하고 있습니다.';
      } else {
        status.textContent = '"' + query + '" 검색 결과 ' + visibleCount + '개 · 필터: ' + filterLabels[activeFilter];
      }

      syncUrl(query);
    }

    buttons.forEach(function (button) {
      button.addEventListener('click', function () {
        activeFilter = button.dataset.filter;
        setActiveButton(activeFilter);
        render();
      });
    });

    input.addEventListener('input', render);
    render();
  });
</script>
