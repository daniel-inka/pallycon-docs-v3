---
# Course title, summary, and position.
linktitle: 포렌식 워터마킹
weight: 20

# Page metadata.
title: PallyCon 포렌식 워터마킹
date: "2018-09-09T00:00:00Z"
lastmod: "2020-09-09T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: false  # Show table of contents? true/false
type: book  # Do not modify.

# Add menu entry to sidebar.
# - name: Declare this menu item as a parent with ID `name`.
# - weight: Position of link in menu.
menu:
  watermarking:
    name: 포렌식 워터마킹
    weight: 20
---

PallyCon 워터마킹 서비스는 클라우드 기반의 SaaS 서비스로, 복잡한 서버 환경을 구축하지 않고도 워터마킹을 쉽고 빠르게 적용할 수 있게 도와줍니다.

{{< figure library="true" numbered="false" src="pallycon-forensic-watermarking-workflow-ko.png" title="How it works" >}}

워터마킹 서비스를 적용하려면 아래 가이드를 참고하시기 바랍니다.

<div class="row">
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">포렌식 워터마킹 시작하기</h3>
        <p class="card-text">PallyCon 포렌식 워터마킹 서비스를 빠르게 이해하고 체험할 수 있도록 기본 설명과 가이드를 제공합니다.</p>
        <a href="./getting-started/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">워터마크 전처리</h3>
        <p class="card-text">콘텐츠에 워터마크를 적용하려면 하나의 동영상에 두 가지 버전의 워터마크 영상을 생성하는 전처리 과정이 필요합니다. 본 문서는 클라우드 방식의 패키징 서비스 또는 인코더 연동을 통한 워터마크 전처리 방법을 설명합니다.</p>
        <a href="./preprocessing/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">워터마크 삽입</h3>
        <p class="card-text">전처리 과정으로 생성된 두 가지 버전의 영상은 재생 시점에 사용자(세션) 정보에 따라 조합되어 클라이언트에 전달됩니다. 본 문서는 세션 매니저와 CDN 연동에 필요한 워터마크 삽입 모듈에 대해 설명합니다.</p>
        <a href="./embedding/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">워터마크 검출</h3>
        <p class="card-text">고객사에서 워터마크가 적용된 콘텐츠의 유출본을 입수한 경우, 워터마크 검출을 시도하여 유출자의 정보를 찾아낼 수 있습니다.</p>
        <a href="./detecting/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">릴리즈 노트</h3>
        <p class="card-text">PallyCon 워터마킹 관련 각종 제품의 릴리즈 이력입니다.</p>
        <a href="./watermarking-release-notes/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>
</div>
