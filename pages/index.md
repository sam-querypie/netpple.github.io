---
layout: page
title: Netpple Engineering Archive
permalink: /
description: 클라우드 네이티브와 분산 시스템 운영 경험을 정리한 기술 아카이브
---
{% assign posts = site.posts | where_exp: "post", "post.excluded_in_search != true" %}
{% assign sorted_docs = site.docs | sort: "date" | reverse %}
{% assign istio_docs = site.docs | where: "label", "istio in action" %}
{% assign docker_docs = site.docs | where: "label", "도커 없이 컨테이너 만들기" %}
{% assign kube_docs = site.docs | where: "label", "쿠버네티스 딥다이브" %}
{% assign sorted_announcements = site.announcements | where_exp: "announcement", "announcement.published != false" | sort: "date" | reverse %}
{% assign primary_announcement = nil %}
{% for announcement in sorted_announcements %}
  {% if announcement.expires_at == nil or announcement.expires_at > site.time %}
    {% if announcement.pinned %}
      {% assign primary_announcement = announcement %}
      {% break %}
    {% endif %}
  {% endif %}
{% endfor %}
{% if primary_announcement == nil %}
  {% for announcement in sorted_announcements %}
    {% if announcement.expires_at == nil or announcement.expires_at > site.time %}
      {% assign primary_announcement = announcement %}
      {% break %}
    {% endif %}
  {% endfor %}
{% endif %}
{% assign has_secondary_announcements = false %}
{% if primary_announcement %}
  {% for announcement in sorted_announcements %}
    {% if announcement.expires_at == nil or announcement.expires_at > site.time %}
      {% if announcement.url != primary_announcement.url %}
        {% assign has_secondary_announcements = true %}
        {% break %}
      {% endif %}
    {% endif %}
  {% endfor %}
{% endif %}

<section class="home-hero">
  <p class="home-hero__eyebrow">Tech Journal</p>
  <h1 class="home-hero__title">클라우드 네이티브와 분산 시스템 운영 지식을 구조적으로 기록합니다.</h1>
  <p class="home-hero__description">
    실무에서 검증한 Kubernetes, Container, Istio, 플랫폼 운영 경험을 문서와 뉴스 형식으로 정리한 아카이브입니다.
    빠른 탐색과 높은 가독성을 중심으로 전체 정보 구조를 재구성했습니다.
  </p>
  <div class="home-hero__actions button-row">
    <a class="button button--primary" href="{{ site.baseurl }}/docs/">문서 허브 보기</a>
    <a class="button button--ghost" href="{{ site.baseurl }}/news/">최신 글 보기</a>
  </div>
  <div class="home-stats">
    <div class="home-stats__item">
      <p class="home-stats__label">Posts</p>
      <p class="home-stats__value">{{ posts | size }}</p>
    </div>
    <div class="home-stats__item">
      <p class="home-stats__label">Docs</p>
      <p class="home-stats__value">{{ sorted_docs | size }}</p>
    </div>
    <div class="home-stats__item">
      <p class="home-stats__label">Since</p>
      <p class="home-stats__value">2021</p>
    </div>
  </div>
</section>

{% if primary_announcement %}
  {% assign primary_cta_url = primary_announcement.cta_url | default: primary_announcement.url %}
  {% if primary_cta_url contains "://" %}
    {% assign primary_cta_href = primary_cta_url %}
  {% else %}
    {% assign primary_cta_href = primary_cta_url | prepend: site.baseurl %}
  {% endif %}
  <section class="home-section home-announcement-section" aria-labelledby="home-announcements-title">
    <div class="home-announcement">
      <div class="home-announcement__main">
        <div class="home-announcement__meta">
          <p class="home-announcement__eyebrow">Updates</p>
          <time datetime="{{ primary_announcement.date | date_to_xmlschema }}">{{ primary_announcement.date | date: "%Y.%m.%d" }}</time>
          {% if primary_announcement.pinned %}<span class="badge">Pinned</span>{% endif %}
        </div>
        <h2 class="home-announcement__title" id="home-announcements-title">
          <a href="{{ primary_announcement.url | prepend: site.baseurl }}">{{ primary_announcement.title }}</a>
        </h2>
        <p class="home-announcement__summary">{{ primary_announcement.summary }}</p>
      </div>
      <div class="home-announcement__actions">
        <a class="button button--ghost" href="{{ primary_cta_href }}"{% if primary_cta_url contains "://" %} target="_blank" rel="noreferrer noopener"{% endif %}>
          {{ primary_announcement.cta_label | default: "공지 보기" }}
        </a>
        <a class="home-announcement__archive-link" href="{{ site.baseurl }}/announcements/">모든 공지 보기</a>
      </div>
    </div>
    {% if has_secondary_announcements %}
      <div class="home-announcement-list" aria-label="최근 공지">
        {% assign secondary_count = 0 %}
        {% for announcement in sorted_announcements %}
          {% if announcement.expires_at == nil or announcement.expires_at > site.time %}
            {% if announcement.url == primary_announcement.url %}
              {% continue %}
            {% endif %}
            {% assign secondary_cta_url = announcement.cta_url | default: announcement.url %}
            {% if secondary_cta_url contains "://" %}
              {% assign secondary_cta_href = secondary_cta_url %}
            {% else %}
              {% assign secondary_cta_href = secondary_cta_url | prepend: site.baseurl %}
            {% endif %}
            {% assign secondary_count = secondary_count | plus: 1 %}
            <article class="home-announcement-list__item">
              <div>
                <p class="home-announcement-list__date">{{ announcement.date | date: "%Y.%m.%d" }}</p>
                <h3 class="home-announcement-list__title"><a href="{{ announcement.url | prepend: site.baseurl }}">{{ announcement.title }}</a></h3>
                <p class="home-announcement-list__summary">{{ announcement.summary }}</p>
              </div>
              <a class="home-announcement-list__link" href="{{ secondary_cta_href }}"{% if secondary_cta_url contains "://" %} target="_blank" rel="noreferrer noopener"{% endif %}>
                {{ announcement.cta_label | default: "바로가기" }}
              </a>
            </article>
            {% if secondary_count >= 2 %}
              {% break %}
            {% endif %}
          {% endif %}
        {% endfor %}
      </div>
    {% endif %}
  </section>
{% endif %}

<section class="home-section">
  <div class="section-heading">
    <p class="section-heading__kicker">Start Here</p>
    <h2 class="section-heading__title">최근 게시글</h2>
    <p class="section-heading__description">운영 이슈 해결 사례와 기술 실험 기록을 최신 순으로 확인할 수 있습니다.</p>
  </div>
  <div class="entry-grid home-news-grid">
    {% for post in posts limit:3 %}
      <article class="entry-card">
        <div class="entry-card__meta">
          <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%Y.%m.%d" }}</time>
          {% if post.categories %}<span class="badge badge-secondary">{{ post.categories | join: ", " }}</span>{% endif %}
          <span class="badge">{{ post.version | default: "v1.0" }}</span>
        </div>
        <h3 class="entry-card__title"><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></h3>
        <p class="entry-card__excerpt">{{ post.content | split: "<!--more-->" | first | strip_html | strip_newlines | truncate: 140 }}</p>
        <a class="entry-card__cta" href="{{ post.url | prepend: site.baseurl }}">자세히 보기 →</a>
      </article>
    {% endfor %}
  </div>
</section>

<section class="home-section">
  <div class="section-heading">
    <p class="section-heading__kicker">Documentation Tracks</p>
    <h2 class="section-heading__title">주요 문서 시리즈</h2>
    <p class="section-heading__description">학습 경로 중심으로 문서를 묶어 접근성을 높였습니다.</p>
  </div>
  <div class="track-grid home-track-grid">
    <article class="track-card">
      <h3 class="track-card__title"><a href="{{ site.baseurl }}/docs/istio-in-action/">Istio IN ACTION</a></h3>
      <p class="track-card__description">서비스 메시 핵심 개념부터 보안, 트래픽 제어, 문제 해결까지 단계적으로 정리한 시리즈입니다.</p>
      <div class="track-card__foot">
        <span>{{ istio_docs | size }} docs</span>
        {% assign latest = istio_docs | sort: "date" | reverse | first %}
        {% if latest %}<span>Latest: {{ latest.date | date: "%Y.%m.%d" }}</span>{% endif %}
      </div>
    </article>
    <article class="track-card">
      <h3 class="track-card__title"><a href="{{ site.baseurl }}/docs/make-container-without-docker/">도커 없이 컨테이너 만들기</a></h3>
      <p class="track-card__description">컨테이너 인터널과 리눅스 네임스페이스를 실습 중심으로 깊게 다룬 콘텐츠입니다.</p>
      <div class="track-card__foot">
        <span>{{ docker_docs | size }} docs</span>
        {% assign latest = docker_docs | sort: "date" | reverse | first %}
        {% if latest %}<span>Latest: {{ latest.date | date: "%Y.%m.%d" }}</span>{% endif %}
      </div>
    </article>
    <article class="track-card">
      <h3 class="track-card__title"><a href="{{ site.baseurl }}/docs/deepdive-into-kubernetes/">쿠버네티스 딥다이브</a></h3>
      <p class="track-card__description">쿠버네티스 구성 요소를 운영 관점에서 분석하고 디버깅 포인트를 정리했습니다.</p>
      <div class="track-card__foot">
        <span>{{ kube_docs | size }} docs</span>
        {% assign latest = kube_docs | sort: "date" | reverse | first %}
        {% if latest %}<span>Latest: {{ latest.date | date: "%Y.%m.%d" }}</span>{% endif %}
      </div>
    </article>
  </div>
</section>

<section class="home-section">
  <div class="home-about-panel">
    <div>
      <h2 class="home-about-panel__title">About Sam</h2>
      <p class="home-about-panel__text">
        대용량 분산 시스템과 클라우드 플랫폼 구축/운영 경험을 바탕으로, 기술 선택의 배경과 실무 적용 과정을
        재사용 가능한 형태로 기록합니다.
      </p>
      <div class="button-row button-row--offset">
        <a class="button button--primary" href="{{ site.baseurl }}/about/">프로필 보기</a>
      </div>
    </div>
    <div class="home-about-panel__meta">
      <div class="home-about-panel__meta-item">
        <p class="home-about-panel__meta-label">Core Domains</p>
        <p class="home-about-panel__meta-value">Security · Search · Cloud · SRE</p>
      </div>
      <div class="home-about-panel__meta-item">
        <p class="home-about-panel__meta-label">Current Focus</p>
        <p class="home-about-panel__meta-value">Cloud Native Architecture & Platform Engineering</p>
      </div>
      <div class="home-about-panel__meta-item">
        <p class="home-about-panel__meta-label">Contact</p>
        <p class="home-about-panel__meta-value">{{ site.email }}</p>
      </div>
    </div>
  </div>
</section>
