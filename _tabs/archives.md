---
layout: page
title: 아카이브
icon: fas fa-box-archive
order: 2
---

<!-- ============================================================= -->
<!--  아카이브 탭                                                   -->
<!--  연도별 타임라인 + 태그 필터.                                  -->
<!--  홈의 태그 클라우드가 #tag-슬러그 로 이 페이지를 가리킵니다.   -->
<!-- ============================================================= -->

{% assign visible_posts = site.posts | where_exp: 'item', 'item.hidden != true' %}

<div id="arc-board">

  <!-- 태그 필터 -->
  {% assign sorted_tags = site.tags | sort %}
  {% if sorted_tags.size > 0 %}
    <div class="arc-filter">
      <button type="button" class="arc-chip arc-chip-on" data-tag="*">전체</button>
      {% for tag in sorted_tags %}
        {% assign tag_name = tag | first %}
        {% assign tag_posts = tag | last %}
        <button type="button" class="arc-chip" data-tag="{{ tag_name | slugify }}" id="tag-{{ tag_name | slugify }}">
          {{ tag_name }} <span class="arc-chip-n">{{ tag_posts | size }}</span>
        </button>
      {% endfor %}
    </div>
  {% endif %}

  <div class="arc-empty" id="arc-empty" hidden>해당 태그의 글이 없습니다.</div>

  <!-- 연도별 타임라인 -->
  <div class="arc-timeline">
    {% assign last_year = '' %}
    {% for post in visible_posts %}
      {% assign post_year = post.date | date: '%Y' %}

      {% if post_year != last_year %}
        {% unless forloop.first %}</div>{% endunless %}
        <div class="arc-year-group">
          <h2 class="arc-year">{{ post_year }}</h2>
        {% assign last_year = post_year %}
      {% endif %}

        {% assign post_tags = post.tags | join: ' ' | slugify %}
        <article class="arc-item" data-tags="{{ post.tags | join: '|' | downcase }}">
          <a href="{{ post.url | relative_url }}" class="arc-link">
            <time class="arc-date">{{ post.date | date: '%m.%d' }}</time>
            <div class="arc-body">
              <span class="arc-title">{{ post.title }}</span>
              <span class="arc-meta">
                {% if post.categories.size > 0 %}
                  <span class="arc-cat"><i class="far fa-folder"></i> {{ post.categories[0] }}</span>
                {% endif %}
                {% for t in post.tags %}
                  <span class="arc-tag">#{{ t }}</span>
                {% endfor %}
              </span>
            </div>
          </a>
        </article>

      {% if forloop.last %}</div>{% endif %}
    {% endfor %}
  </div>
</div>
<!-- #arc-board -->

<script>
(function () {
  var board = document.getElementById('arc-board');
  if (!board) return;

  var chips = Array.prototype.slice.call(board.querySelectorAll('.arc-chip'));
  var items = Array.prototype.slice.call(board.querySelectorAll('.arc-item'));
  var groups = Array.prototype.slice.call(board.querySelectorAll('.arc-year-group'));
  var empty = document.getElementById('arc-empty');

  function slugify(s) {
    return (s || '').toLowerCase().trim().replace(/[^a-z0-9가-힣]+/g, '-').replace(/^-+|-+$/g, '');
  }

  function applyFilter(tag) {
    var anyVisible = false;

    items.forEach(function (item) {
      var raw = (item.getAttribute('data-tags') || '').split('|');
      var slugs = raw.map(slugify);
      var show = tag === '*' || slugs.indexOf(tag) !== -1;
      item.style.display = show ? '' : 'none';
      if (show) anyVisible = true;
    });

    // 빈 연도 그룹 숨기기
    groups.forEach(function (group) {
      var visibleItems = group.querySelectorAll('.arc-item');
      var hasVisible = Array.prototype.some.call(visibleItems, function (it) {
        return it.style.display !== 'none';
      });
      group.style.display = hasVisible ? '' : 'none';
    });

    empty.hidden = anyVisible;
  }

  chips.forEach(function (chip) {
    chip.addEventListener('click', function () {
      chips.forEach(function (c) { c.classList.remove('arc-chip-on'); });
      chip.classList.add('arc-chip-on');
      applyFilter(chip.getAttribute('data-tag'));
    });
  });

  // 홈에서 #tag-슬러그 로 들어온 경우 자동 필터
  function fromHash() {
    var h = (location.hash || '').replace('#tag-', '');
    if (!h) return;
    var target = board.querySelector('.arc-chip[data-tag="' + h + '"]');
    if (target) target.click();
  }
  window.addEventListener('hashchange', fromHash);
  fromHash();
})();
</script>