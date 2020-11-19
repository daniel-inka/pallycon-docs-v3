---
# Course title, summary, and position.
linktitle: 3. 라이선스 발급 연동
weight: 3

# Page metadata.
title: DRM 라이선스 발급 연동
summary: DRM 콘텐츠 재생을 위해 필요한 라이선스 발급에 대한 가이드입니다.
date: "2018-09-09T00:00:00Z"
lastmod: "2020-09-09T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: false  # Show table of contents? true/false
type: book  # Do not modify.

# Add menu entry to sidebar.
# - name: Declare this menu item as a parent with ID `name`.
# - weight: Position of link in menu.
menu:
  multidrm:
    weight: 3
    parent: 멀티 DRM
    name: 라이선스 발급 연동
---

패키징된 콘텐츠를 클라이언트 기기에서 재생하려면 해당 기기에서 DRM 라이선스를 발급받아야 합니다. 라이선스 발급은 콜백 방식과 토큰 방식의 두 가지 방식으로 연동 가능하며, 두 방식의 차이점과 장단점은 아래 표를 참고하시기 바랍니다.

|<div style="width:70px">연동 방식</div>|설명|장점|단점|
|-----|-----|-----|-----|
| **콜백 방식** |라이선스 발급 연동이 PallyCon 라이선스 서버와 고객사의 백엔드 시스템 간 통신을 통해 구현됨.|클라이언트 어플리케이션에서의 연동 작업이 적음. 라이선스 발급에 대한 제어 및 보안성이 토큰 방식에 비해 높음.|콜백을 통한 추가 통신이 필요하므로 라이선스 발급 소요시간이 토큰 방식 보다 오래 걸릴 수 있음.|
| **토큰 방식** |라이선스 발급 연동이 고객사의 백엔드 시스템과 클라이언트 어플리케이션 간 통신을 통해 구현됨.|라이선스 발급 프로세스가 콜백 방식보다 간단하기 때문에 소요시간이 짧고, 대량의 라이선스 요청 발생 시 더 높은 성능을 제공.|토큰의 생성, 전달 및 관리 기능을 고객사에서 직접 구현 필요.|

> 콜백 방식 라이선스 연동은 고객사 시스템의 상황에 따라 재생 시점에 지연 현상이나 오류가 발생할 수 있어 권장되지 않습니다. 대신 라이선스 토큰을 이용하시기 바라며, 특별한 이유로 콜백 방식 연동을 원하는 고객사는 별도로 문의해 주시기 바랍니다.

아래는 각종 DRM 라이선스 발급 관련 문서입니다.

<div class="row">
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">라이선스 토큰 가이드</h3>
        <p class="card-text">토큰 방식은 서비스 사이트에서 미리 정의된 규격의 라이선스 토큰을 생성하여 클라이언트에 전달하고, 클라이언트는 해당 토큰을 이용해 라이선스를 발급받는 방식입니다. 본 문서는 토큰 방식의 라이선스 발급 방법에 대하여 설명합니다.</p>
        <a href="./license-token/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">라이선스 토큰 튜토리얼</h3>
        <p class="card-text">위 '라이선스 토큰 가이드'에서 설명한 토큰 생성 규격에 따라 단계 별로 토큰을 생성하는 과정을 샘플 코드와 함께 상세 설명합니다.</p>
        <a href="./license-token-tutorial/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">FPS 인증서 등록 자습서</h3>
        <p class="card-text">본 문서는 FairPlay DRM 연동에 필요한 인증서 등록 과정을 설명합니다.</p>
        <a href="./fps-cert-tutorial/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">라이선스 관련 에러코드</h3>
        <p class="card-text">본 문서는 DRM 라이선스 발급 과정에서 발생할 수 있는 각종 에러 코드에 대하여 설명합니다.</p>
        <a href="./license-errorcode/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>
</div>
