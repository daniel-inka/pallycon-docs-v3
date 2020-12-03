---
linktitle: Wowza 연동 가이드
weight: 50

# Page metadata.
title: Wowza 연동 가이드
summary: PallyCon Wowza 연동 SDK는 Wowza Streaming Engine의 확장 모듈로, DASH(CENC)와 HLS(FPS) 콘텐츠로 스트리밍 서비스를 할 수 있도록 지원하는 제품입니다.
date: "2018-09-09T00:00:00Z"
lastmod: "2020-10-23T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: true  # Show table of contents? true/false
type: book  # Do not modify.

menu:
  multidrm:
    weight: 50
    parent: 콘텐츠 패키징
---

 {{< figure library="true" numbered="false" src="wowza-streaming-engine-logo.png">}}

## 개요

PallyCon Wowza 연동 SDK는 Wowza Streaming Engine의 확장 모듈로, 원본 MP4 영상 또는 라이브 스트림을 실시간으로 패키징해 DASH(CENC)와 HLS(FPS) 콘텐츠로 스트리밍 서비스를 할 수 있도록 지원하는 제품입니다.

### 스트리밍 프로토콜 및 DRM 지원

* DASH-CENC : Widevine Modular, PlayReady DRM
* HLS-AES : FairPlay Streaming DRM

> 콜백 방식 패키징을 사용하는 이전 버전과 달리, CPIX 방식의 최신 버전 Wowza 연동 SDK는 NCG DRM 패키징을 지원하지 않습니다. 해당 기능이 필요한 경우 별도로 연락해 주시기 바랍니다.

PallyCon Wowza 연동 SDK는 Wowza Streaming Engine 4.8 버전 이상을 지원하며, 스트리밍 성능 향상을 위해 Wowza Media Cache를 통한 스토리지 연동 및 CDN 서비스 이용을 권장합니다.

Wowza Streaming Engine에 대한 자세한 소개와 PallyCon Wowza 연동 SDK에 대한 데모는 아래 링크들을 참고하시기 바랍니다.

* Wowza Streaming Engine 웹사이트 : <https://www.wowza.com/products/streaming-engine>
* Wowza 실시간 패키징 데모 페이지 : <https://www.pallycon.com/multi-drm-demo/?lang=ko>

본 문서는 Wowza Streaming Engine 설치 후에 PallyCon Wowza 연동 SDK를 적용하는 방법을 설명합니다.

## 연동 워크플로우 {#workflow}

1. 원본 콘텐츠 업로드
 - Wowza 스트리밍 서버와 연동된 스토리지에 원본 MP4 콘텐츠를 업로드합니다. Adaptive Streaming을 위해서는 해상도 별 MP4 파일들과 SMIL 파일을 업로드합니다.
 - 라이브 스트림을 서비스하는 경우에는 라이브 방식으로 생성된 Wowza 어플리케이션에 해당 라이브 소스를 설정합니다.

2. 동영상 재생 요청
 - 클라이언트(HTML5 플레이어 또는 모바일 어플리케이션)에서 DASH(mpd) 또는 HLS(m3u8) 방식의 스트리밍 URL을 통해 스트리밍 재생을 요청합니다.

3. 패키징 키 정보 요청
 - 실시간 패키징에 필요한 키 정보를 PallyCon 클라우드 서버에 요청합니다. 키 정보 요청 시, MP4 파일 명 또는 라이브 스트림 명이 CID 생성 기준으로 전달됩니다.
 - Adaptive Streaming의 경우, SMIL 파일명을 기준으로 키 정보가 요청됩니다.

4. 실시간 패키징
 - PallyCon 클라우드에서 받은 키 정보를 이용하여 원본 콘텐츠를 DASH 또는 HLS 포맷으로 패키징합니다.

5. DRM 라이선스 발급
 - 클라이언트는 암호화된 스트리밍 콘텐츠의 재생에 필요한 키를 PallyCon 클라우드 서버에 요청하며, PallyCon 클라우드 서버는 라이선스 연동을 통해 확인된 클라이언트에게 키 정보를 전달합니다.

## Wowza 연동 모듈 설정 {#settings}

### 요구 사항

- Java 버전: jdk 9 이상

### Wowza Application 생성

고객사에서 관리하는 실시간 스트리밍용 서버에 Wowza Streaming Engine을 설치합니다. http://Wowza-server:8088/ 접속 후 Applcations 탭에서 Wowza 스트리밍 어플리케이션을 생성합니다. VoD 또는 라이브 방식의 어플리케이션을 선택할 수 있으며, 두 가지 방식을 모두 이용하는 경우 각각의 어플리케이션을 별도로 생성해 설정해야 합니다.

### 라이브러리 셋팅

압축 해제한 PallyCon Wowza 연동 SDK의 /lib 폴더 내 파일 들을 /[WOWZA_HOME]/lib/ 폴더에 복사합니다.

> PallyCon Wowza 연동 SDK 압축파일은 PallyCon 콘솔 사이트에서 상용 서비스 신청 시 요청할 수 있으며, 요청 승인이 완료된 후 서비스 정보 페이지에서 다운로드 가능합니다.

```
- pallycon-cpix-common-2.0.0-jar-with-dependencies.jar
- pallycon-wowza-cpix-1.0.0.jar
- protobuf-java-2.6.1.jar
```

### 환경설정

/[WOWZA_HOME]/conf/[APPLICATION]/Application.xml에 PallyCon module 관련 설정을 추가합니다. 

#### FairPlay용 Application LiveStreamPacketizer 추가

https://www.wowza.com/docs/how-to-configure-apple-hls-packetization-cupertinostreaming 가이드를 참고해 아래와 같이 설정합니다.

```xml
...
<LiveStreamPacketizer>
        <Property>
	           <Name>cupertinoChunkDurationTarget</Name>
	            <Value>10000</Value>
	            <Type>Integer</Type>
        </Property>
        <Property>
	            <Name>cupertinoMaxChunkCount</Name>
	            <Value>10</Value>
	            <Type>Integer</Type>
        </Property>
        <Property>
	            <Name>cupertinoPlaylistChunkCount</Name>
	            <Value>3</Value>
	            <Type>Integer</Type>
        </Property>
        <Property>
	            <Name>cupertinoRepeaterChunkCount</Name>
	            <Value>-1</Value>
	            <Type>Integer</Type>
        </Property>
        <Property>
	            <Name>cupertinoCalculateChunkIDBasedOnTimecode</Name>
	            <Value>false</Value>
	            <Type>Boolean</Type>
        </Property>
</LiveStreamPacketizer>
```

#### FairPlay Application HTTPStreamer 추가

```xml
 ...
 <HTTPStreamer>
    <Properties>
        <Property>
            <Name>cupertinoExtXVersion</Name>
            <Value>5</Value>
            <Type>Integer</Type>
        </Property>
    </Properties>
</HTTPStreamer>
```

#### FairPlay 관련 Session ID 옵션 설정

Wowza Streaming Engine은 기본적으로 HLS 청크리스트의 암호화 관련 URI 값에 아래와 같이 스트리밍 세션 ID를 추가합니다.

```
예: sdk://content-id?wowzasessionid=30273096
```

PallyCon FairPlay 연동을 위해서는 해당 값에 세션 ID가 추가되지 않도록 Wowza 설정을 변경해야 합니다. 다음의 가이드를 참고해 `cupertinoAppendQueryParamsToEncUrl` 속성을 `false`로 설정합니다.

- https://www.wowza.com/docs/how-to-control-streaming-session-id-appended-to-encryption-urls-in-chunklist-responses-cupertinoappendqueryparamstoencurl

#### 멀티DRM용 Application module 추가

```xml
<Modules>
    ...
    <Module>
        <!-- add property -->
        <!-- Settings for MPEG-DASH(Widevine, PlayReady), HLS(FairPlayStream) -->
        <Name>DrmModule</Name>
            <Descript>Multi DRM CPIX Module</Descript>
            <Class>com.pallycon.wowza.DrmModule</Class>
        <Description></Description>
	</Module>
</Modules>
```

#### 멀티DRM용 Application property 추가

```xml
<Properties>
    ...
    <!-- add property -->
    <Property>
        <Name>cupertinoEncryptionAPIBased</Name>
        <Value>true</Value>
        <Type>Boolean</Type>
    </Property>
    <Property>
        <Name>KmsUrl</Name>
        <!-- pallycon kms v1 url-->
        <Value>https://kms.pallycon.com/cpix/pallycon/getKey</Value>
    </Property>
     <Property>
     <!-- true : live key rotation / false : single key -->
        <Name>KeyRotation</Name>
        <Value>false</Value>
    </Property>
    <Property>
        <Name>PallyConEncToken</Name>
        <Value>{pallycon kms token}</Value>
    </Property>
</Properties>
```

### Application 재시작 및 HTTPS URL 적용

세팅 완료 후 Wowza Application 을 재시작하면 세팅한 정보로 동작합니다.

> 참고 : DASH(.mpd) 또는 HLS(.m3u8) URL 등의 모든 스트리밍 관련 URL에는 HTTPS가 필수적으로 적용되어야 합니다. (브라우저 강제 사항)
