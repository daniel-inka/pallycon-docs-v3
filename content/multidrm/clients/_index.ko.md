---
# Course title, summary, and position.
linktitle: 4. 클라이언트 연동
weight: 4

# Page metadata.
title: DRM 클라이언트 연동
date: "2018-09-09T00:00:00Z"
lastmod: "2018-09-09T00:00:00Z"
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
    name: 클라이언트 연동
---

PallyCon 멀티DRM 서비스는 PC, 모바일, OTT 플랫폼 등 다양한 클라이언트 환경을 지원하기 위한 연동 API와 SDK를 제공합니다. 아래 문서들은 각 클라이언트 별 SDK의 사용 방법을 설명합니다.

<div class="row">
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">HTML5 플레이어 연동</h3>
        <p class="card-text">고객사는 웹 브라우저 지원을 위해 다양한 HTML5 플레이어 제품들 중에서 원하는 플레이어를 사용할 수 있습니다. 본 문서는 서비스 사이트의 웹 페이지에서 HTML5 플레이어를 이용해 멀티DRM(PlayReady, Widevine, FairPlay Streaming)으로 보호된 스트리밍 콘텐츠(DASH 또는 HLS)를 재생하는 방법을 설명합니다.</p>
        <a href="./html5-player/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">    
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">Widevine Android SDK 가이드</h3>
        <p class="card-text">PallyCon Widevine Android SDK는 안드로이드 모바일 환경에서 구글의 Widevine Modular DRM을 쉽게 적용할 수 있게 해주는 제품입니다. 본 문서는 SDK에 포함된 라이브러리와 샘플 프로젝트에 대하여 설명합니다.</p>
        <a href="./widevine-android/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">  
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">FairPlay iOS SDK 가이드</h3>
        <p class="card-text">PallyCon FairPlay iOS SDK는 iOS용 미디어 서비스 앱을 개발할 때 Apple의 FairPlay Streaming DRM을 쉽게 적용할 수 있게 해주는 제품입니다. 본 문서는 SDK에 포함된 라이브러리와 샘플 프로젝트의 사용법을 설명합니다.</p>
        <a href="./fairplay-ios/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">  
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">PlayReady UWP SDK 가이드</h3>
        <p class="card-text">PallyCon PlayReady UWP SDK는 Universal Windows Platform (UWP) 환경에서 미디어 서비스 앱을 개발할 때 Microsoft사의 PlayReady DRM을 쉽게 적용할 수 있게 해주는 제품입니다. 본 문서는 PallyCon PlayReady UWP SDK에 포함된 라이브러리와 샘플 프로젝트의 사용법을 설명합니다.</p>
        <a href="./playready-uwp/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">  
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">NCG Android SDK 가이드</h3>
        <p class="card-text">PallyCon NCG Android SDK는 안드로이드용 미디어 서비스 앱을 개발할 때 잉카엔트웍스의 NCG(Netsync Content Guard) DRM을 쉽게 적용할 수 있게 해주는 제품입니다. 본 문서는 SDK에 포함된 라이브러리와 샘플 프로젝트의 사용법에 대해 설명합니다.</p>
        <a href="./ncg-android/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">  
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">NCG iOS SDK 가이드</h3>
        <p class="card-text">PallyCon NCG iOS SDK는 iOS용 미디어 서비스 앱을 개발할 때 잉카엔트웍스의 NCG(Netsync Content Guard) DRM을 쉽게 적용할 수 있게 해주는 제품입니다. 본 문서는 SDK에 포함된 라이브러리와 샘플 프로젝트의 사용법에 대해 설명합니다.</p>
        <a href="./ncg-ios/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">  
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">Widevine AndroidTV SDK 가이드</h3>
        <p class="card-text">PallyCon Widevine Android TV SDK는 안드로이드 TV 단말용 미디어 서비스 앱을 개발할 때 구글의 Widevine Modular DRM을 쉽게 적용할 수 있게 해주는 제품입니다. 본 문서는 SDK에 포함된 라이브러리와 샘플 프로젝트의 사용법에 대해 설명합니다.</p>
        <a href="./widevine-androidtv/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">  
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">크롬캐스트 연동 가이드</h3>
        <p class="card-text">본 문서는 FairPlay iOS SDK와 Widevine Android SDK에서 크롬캐스트를 연동하는 방법을 설명합니다.</p>
        <a href="./chromecast-integration/" class="btn btn-primary">바로가기</a>
      </div>
    </div>
  </div>        
</div>
