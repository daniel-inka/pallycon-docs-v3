---
# Course title, summary, and position.
linktitle: 멀티DRM 가이드
weight: 1

# Page metadata.
title: PallyCon 멀티DRM 가이드
summary: PallyCon 멀티 DRM 서비스는 클라우드 기반의 SaaS (Solution as a Service)로, 콘텐츠에 DRM을 적용하는 패키징 솔루션과 DRM 라이선스 발급 서버, 그리고 DRM 콘텐츠의 재생에 필요한 클라이언트 연동 솔루션으로 이루어져 있습니다.
date: "2018-09-09T00:00:00Z"
lastmod: "2020-09-16T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: false  # Show table of contents? true/false
type: book  # Do not modify.

# Add menu entry to sidebar.
# - name: Declare this menu item as a parent with ID `name`.
# - weight: Position of link in menu.
menu:
  multidrm:
    name: 멀티DRM 가이드
    weight: 1
---

PallyCon 멀티 DRM 서비스는 클라우드 기반의 SaaS (Solution as a Service)로, 콘텐츠에 DRM을 적용하는 패키징 솔루션과 DRM 라이선스 발급 서버, 그리고 DRM 콘텐츠의 재생에 필요한 클라이언트 연동 솔루션으로 이루어져 있습니다.

![howitworks](/docs/images/pallycon-howitworks.png)

아래와 같은 가이드 문서들을 참조하여 고객님의 콘텐츠 서비스와 PallyCon 멀티DRM 서비스를 연동하실 수 있습니다.

<div class="cards">
<article class="card">
    <div class="text">
        <h3>DRM 개념 및 용어</h3>
        PallyCon 멀티DRM 서비스와 관련된 각종 개념과 용어에 대하여 설명합니다.<p>
        <a href="{{<ref "drm-concepts.ko.md">}}" target="_self" class="btn btn-default">바로 가기</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>콘텐츠 패키징</h3>
        PallyCon 서비스를 적용하기 위해서는 가장 먼저 콘텐츠를 암호화하는 패키징 과정을 수행해야 합니다. 본 문서는 PallyCon 패키져, AWS Elemental 미디어 서비스, Wowza Streaming Engine, PallyCon 패키징 서비스 등 다양한 콘텐츠 패키징 방식을 설명합니다.<p>
        <a href="{{<ref "./packaging/">}}" target="_self" class="btn btn-default">바로 가기</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>라이선스 발급 연동</h3>
        패키징된 콘텐츠를 클라이언트 기기에서 재생하려면 해당 기기에서 DRM 라이선스를 발급받아야 합니다. 본 문서는 각종 DRM 라이선스 발급 연동 방식에 대하여 설명합니다.<p>
        <a href="{{<ref "./license/">}}" target="_self" class="btn btn-default">바로 가기</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>클라이언트 연동</h3>
        PallyCon 멀티DRM 서비스는 PC, 모바일, OTT 플랫폼 등 다양한 클라이언트 환경을 지원하기 위한 연동 API와 SDK를 제공합니다. 본 문서는 각 클라이언트 별 SDK의 사용 방법을 설명합니다.<p>
        <a href="{{<ref "./clients/">}}" target="_self" class="btn btn-default">바로 가기</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>릴리즈 노트</h3>
        PallyCon 멀티DRM 관련 각종 제품의 릴리즈 이력입니다.<p>
        <a href="{{<ref "release-notes.ko.md">}}" target="_self" class="btn btn-default">바로 가기</a>
    </div>
</article>
</div>
