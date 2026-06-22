---
layout: page
title: 카테고리
icon: fas fa-folder-open
order: 1
---

<!-- ============================================================= -->
<!--  카테고리 탭                                                   -->
<!--  글을 올릴 때마다 카테고리별로 자동 그룹핑됩니다.              -->
<!-- ============================================================= -->

<div id="cat-board">

  <!-- 컨트롤 바 -->
  <div class="cb-controls">
    <input type="text" id="cb-search" class="cb-search"
           placeholder="카테고리 또는 글 제목 검색..." aria-label="카테고리 검색">
    <div class="cb-actions">
      <button type="button" class="cb-btn" id="cb-expand">모두 펼치기</button>
      <button type="button" class="cb-btn" id="cb-collapse">모두 접기</button>
    </div>
  </div>

  <!-- 검색 결과 없음 -->
  <div class="cb-empty" id="cb-empty" hidden>검색 결과가 없습니다.</div>

  <!-- 카테고리 목록: 1차 카테고리 기준 그룹핑 -->
  <div class="cb-list">
    {% assign sorted_categories = site.categories | sort %}
    {% for category in sorted_categories %}
      {% assign cat_name = category | first %}
      {% assign cat_posts = category | last %}
      {% assign first_post = cat_posts | first %}

      {% if cat_name == first_post.categories[0] %}
        {% assign post_count = cat_posts | size %}

        <section class="cb-card" data-cat="{{ cat_name | downcase }}">
          <button type="button" class="cb-head" aria-expanded="true">
            <span class="cb-head-left">
              <i class="fas fa-folder-open cb-folder"></i>
              <span class="cb-name">{{ cat_name }}</span>
              <span class="cb-count">{{ post_count }}</span>
            </span>
            <i class="fas fa-chevron-down cb-chevron"></i>
          </button>

          <ul class="cb-posts">
            {% for post in cat_posts %}
              <li class="cb-post" data-title="{{ post.title | downcase }}">
                <a href="{{ post.url | relative_url }}" class="cb-post-link">
                  <span class="cb-post-title">{{ post.title }}</span>
                  <span class="cb-post-meta">
                    {% if post.categories.size > 1 %}
                      <span class="cb-sub">{{ post.categories[1] }}</span>
                    {% endif %}
                    <time class="cb-post-date">{{ post.date | date: '%Y.%m.%d' }}</time>
                  </span>
                </a>
              </li>
            {% endfor %}
          </ul>
        </section>
      {% endif %}
    {% endfor %}
  </div>
</div>
<!-- #cat-board -->

<script>
(function () {
  var board = document.getElementById('cat-board');
  if (!board) return;

  var search   = document.getElementById('cb-search');
  var empty    = document.getElementById('cb-empty');
  var cards    = Array.prototype.slice.call(board.querySelectorAll('.cb-card'));
  var expandBtn   = document.getElementById('cb-expand');
  var collapseBtn = document.getElementById('cb-collapse');

  function setOpen(card, open) {
    var head = card.querySelector('.cb-head');
    var list = card.querySelector('.cb-posts');
    head.setAttribute('aria-expanded', open ? 'true' : 'false');
    list.style.display = open ? '' : 'none';
    card.classList.toggle('cb-closed', !open);
  }

  cards.forEach(function (card) {
    var head = card.querySelector('.cb-head');
    head.addEventListener('click', function () {
      var open = head.getAttribute('aria-expanded') === 'true';
      setOpen(card, !open);
    });
  });

  if (expandBtn)   expandBtn.addEventListener('click',   function () { cards.forEach(function (c) { setOpen(c, true);  }); });
  if (collapseBtn) collapseBtn.addEventListener('click', function () { cards.forEach(function (c) { setOpen(c, false); }); });

  function normalize(s) { return (s || '').toLowerCase().trim(); }

  search.addEventListener('input', function () {
    var q = normalize(search.value);
    var anyVisible = false;

    cards.forEach(function (card) {
      var catName = card.getAttribute('data-cat') || '';
      var posts = Array.prototype.slice.call(card.querySelectorAll('.cb-post'));
      var catMatch = catName.indexOf(q) !== -1;
      var postMatchCount = 0;

      posts.forEach(function (li) {
        var title = li.getAttribute('data-title') || '';
        var show = q === '' || catMatch || title.indexOf(q) !== -1;
        li.style.display = show ? '' : 'none';
        if (show) postMatchCount++;
      });

      var visible = q === '' || catMatch || postMatchCount > 0;
      card.style.display = visible ? '' : 'none';
      if (visible) {
        anyVisible = true;
        if (q !== '') setOpen(card, true);
      }
    });

    empty.hidden = anyVisible;
  });
})();
</script>