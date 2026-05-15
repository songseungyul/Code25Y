---
layout: page
title: Search
permalink: /search/
---

게시된 글을 제목, 요약, 카테고리 기준으로 검색할 수 있습니다.

<div class="search-shell">
  <label class="search-label" for="search-input">검색어</label>
  <input id="search-input" class="search-input" type="search" placeholder="예: 마크다운, 자동화, 공부 노트" autocomplete="off" />

  <div class="search-filter-row" aria-label="카테고리 필터">
    <button type="button" class="filter-chip is-active" data-filter="all">전체</button>
    <button type="button" class="filter-chip" data-filter="study">Study</button>
    <button type="button" class="filter-chip" data-filter="todo-study">To Study</button>
    <button type="button" class="filter-chip" data-filter="notes">Notes</button>
  </div>

  <p class="search-help">두 글자 이상 입력하면 제목, 요약, 카테고리에서 바로 찾습니다.</p>
  <p id="search-status" class="search-status">전체 글을 불러왔습니다.</p>

  <ul id="search-results" class="archive-list">
    {% for post in site.posts %}
      <li
        class="archive-item search-item"
        data-title="{{ post.title | downcase | escape }}"
        data-excerpt="{{ post.excerpt | strip_html | downcase | escape }}"
        data-categories="{{ post.categories | join: ' ' | downcase | escape }}"
        data-url="{{ post.url | relative_url }}"
        data-date="{{ post.date | date: '%Y.%m.%d' }}"
        data-title-raw="{{ post.title | escape }}"
        data-excerpt-raw="{{ post.excerpt | strip_html | escape }}"
        data-categories-raw="{{ post.categories | join: ' / ' | escape }}"
      >
        <p class="archive-meta">{{ post.date | date: "%Y.%m.%d" }} · {{ post.categories | join: " / " }}</p>
        <h2 class="archive-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
        {% if post.excerpt %}
          <p class="archive-excerpt">{{ post.excerpt | strip_html }}</p>
        {% endif %}
      </li>
    {% endfor %}
  </ul>
</div>

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
        titleRaw: item.dataset.titleRaw || '',
        excerptRaw: item.dataset.excerptRaw || '',
        categoriesRaw: item.dataset.categoriesRaw || '',
        url: item.dataset.url || '#',
        date: item.dataset.date || '',
        element: item
      };
    });

    var activeFilter = 'all';
    var filterLabels = {
      all: '전체',
      study: 'Study',
      'todo-study': 'To Study',
      notes: 'Notes'
    };

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

    function render() {
      var query = input.value.trim().toLowerCase();
      var visibleCount = 0;
      var html = '';

      items.forEach(function (item) {
        var haystack = [item.title, item.excerpt, item.categories].join(' ');
        var categoryMatched = activeFilter === 'all' || item.categories.split(' ').indexOf(activeFilter) !== -1;
        var queryMatched = query.length < 2 || haystack.indexOf(query) !== -1;
        var matched = categoryMatched && queryMatched;

        if (!matched) return;
        visibleCount += 1;

        html += '<li class="archive-item">';
        html += '<p class="archive-meta">' + escapeHtml(item.date + ' · ' + item.categoriesRaw) + '</p>';
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
    }

    buttons.forEach(function (button) {
      button.addEventListener('click', function () {
        activeFilter = button.dataset.filter;
        buttons.forEach(function (btn) {
          btn.classList.toggle('is-active', btn === button);
        });
        render();
      });
    });

    input.addEventListener('input', render);
    render();
  });
</script>
