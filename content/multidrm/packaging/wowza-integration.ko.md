---
linktitle: Wowza 연동 가이드
weight: 5

# Page metadata.
title: Wowza 연동 가이드
date: "2018-09-09T00:00:00Z"
lastmod: "2018-09-09T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: true  # Show table of contents? true/false
type: book  # Do not modify.

menu:
  multidrm:
    weight: 5
    parent: 콘텐츠 패키징
---

![wowza-logo](/docs/images/wowza-streaming-engine-logo.png)

## 개요

PallyCon Wowza 연동 SDK는 Wowza Streaming Engine의 확장 모듈로, 원본 MP4 영상 또는 라이브 스트림을 실시간으로 패키징해 DASH(CENC)와 HLS(FPS 또는 NCG) 콘텐츠로 스트리밍 서비스를 할 수 있도록 지원하는 제품입니다.

### 스트리밍 프로토콜 및 DRM 지원

* DASH-CENC : Widevine Modular, PlayReady DRM
* HLS-AES : FairPlay Streaming DRM
* HLS-AES : NCG DRM

PallyCon Wowza 연동 SDK는 Wowza Streaming Engine 4.5 버전 이상을 지원하며, 스트리밍 성능 향상을 위해 Wowza Media Cache를 통한 스토리지 연동 및 CDN 서비스 이용을 권장합니다.

Wowza Streaming Engine에 대한 자세한 소개와 PallyCon Wowza 연동 SDK에 대한 데모는 아래 링크들을 참고하시기 바랍니다.

* Wowza Streaming Engine 웹사이트 : <https://www.wowza.com/products/streaming-engine>
* Wowza 실시간 패키징 데모 페이지 : <https://www.pallycon.com/multi-drm-demo/?lang=ko>

본 문서는 Wowza Streaming Engine 설치 후에 PallyCon Wowza 연동 SDK를 적용하는 방법을 설명합니다.

> Wowza를 이용한 스트리밍 서비스 시, 콘텐츠에 대한 CID 발급은 콜백 페이지 연동을 통해서 이루어집니다. 이와 관련된 규격은 별도로 문의 바랍니다.

## 연동 워크플로우 {#workflow}

![wowza-integration](/docs/images/wowza-integration-workflow.png)

1. 원본 콘텐츠 업로드
 - Wowza 스트리밍 서버와 연동된 스토리지에 원본 MP4 콘텐츠를 업로드합니다. Adaptive Streaming을 위해서는 해상도 별 MP4 파일들과 SMIL 파일을 업로드합니다.
 - 라이브 스트림을 서비스하는 경우에는 라이브 방식으로 생성된 Wowza 어플리케이션에 해당 라이브 소스를 설정합니다.

2. 동영상 재생 요청
 - 클라이언트(HTML5 플레이어 또는 모바일 어플리케이션)에서 DASH(mpd) 또는 HLS(m3u8) 방식의 스트리밍 URL을 통해 스트리밍 재생을 요청합니다.

3. 패키징 키 정보 요청
 - 실시간 패키징에 필요한 키 정보를 PallyCon 클라우드 서버에 요청합니다. 키 정보 요청 시, MP4 파일 명 또는 라이브 스트림 명이 CID 생성 기준으로 전달됩니다.
 - Adaptive Streaming의 경우, SMIL 파일명을 기준으로 키 정보가 요청됩니다.

4. CID 발급 처리
 - PallyCon Key DB에 해당 콘텐츠의 키 정보가 없는 경우, 패키징 콜백 페이지로 해당 콘텐츠의 CID를 발급 요청합니다.

5. 실시간 패키징
 - PallyCon 클라우드에서 받은 키 정보를 이용하여 원본 콘텐츠를 DASH 또는 HLS 포맷으로 패키징합니다.

6. DRM 라이선스 발급
 - 클라이언트는 암호화된 스트리밍 콘텐츠의 재생에 필요한 키를 PallyCon 클라우드 서버에 요청하며, PallyCon 클라우드 서버는 라이선스 연동을 통해 확인된 클라이언트에게 키 정보를 전달합니다.

## Wowza 연동 모듈 설정 {#settings}

### Wowza Application 생성

고객사에서 관리하는 실시간 스트리밍용 서버에 Wowza Streaming Engine을 설치합니다. http://Wowza-server:8088/ 접속 후 Applcations 탭에서 Wowza 스트리밍 어플리케이션을 생성합니다. VoD 또는 라이브 방식의 어플리케이션을 선택할 수 있으며, 두 가지 방식을 모두 이용하는 경우 각각의 어플리케이션을 별도로 생성해 설정해야 합니다.

> 참고: 멀티DRM(PlayReady, Widevine, FPS)과 NCG DRM을 모두 적용하는 경우, 각각의 Wowza 어플리케이션을 별도로 생성해 설정해야 합니다.

### 라이브러리 셋팅

압축 해제한 PallyCon Wowza 연동 SDK의 /lib 폴더 내 파일 들을 /[WOWZA_HOME]/lib/ 폴더에 복사합니다.

> PallyCon Wowza 연동 SDK 압축파일은 PallyCon 콘솔 사이트에서 상용 서비스 신청 시 요청할 수 있으며, 요청 승인이 완료된 후 서비스 정보 페이지에서 다운로드 가능합니다.

```s
- PallyConMultiDrmModule.jar
- NetsyncModule.jar
- not-yet-commons-ssl-0.3.9.jar
- commons-logging-1.1.1.jar
- httpclient-4.2.5.jar
- httpcore-4.2.4.jar
```

### Packager Key 셋팅

압축 해제한 PallyCon Wowza 연동 SDK의 /conf 폴더 내 pallycon_pack.key 파일을 /[WOWZA_HOME]/conf/[APPLICATION] 폴더에 복사합니다.

### 환경설정

/[WOWZA_HOME]/conf/[APPLICATION]/Application.xml에 PallyCon module 관련 설정을 추가합니다. 멀티DRM(PlayReady, Widevine, FPS)용 Wowza 어플리케이션과 NCG DRM용 어플리케이션은 별도로 생성해 설정해야 하며 각각의 환경 설정은 아래와 같습니다.

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
    <!-- MPEG-DASH(Widevine, PlayReady), HLS(FairPlayStream) 사용 시 setting -->
    <Module>
        <Name>MultiDrmModule</Name>
        <Description>Wowza MultiDrmModule</Description>
        <Class>com.inka.wms.multidrmmodule.MultiDrmModule</Class>
    </Module>
</Modules>
```

#### 멀티DRM용 Application property 추가

```xml
<Properties>
    ...
    <!-- property 추가 -->
    <Property>
        <Name>cupertinoEncryptionAPIBased</Name>
        <Value>true</Value>
        <Type>Boolean</Type>
    </Property>
    <Property>
        <Name>SiteID</Name>
        <Value>[SITEID]</Value>
    </Property>
    <Property>
        <Name>AccessKey</Name>
        <Value>[ACCESSKEY]</Value>
    </Property>
    <Property>
        <Name>PrivateKeyPath</Name>
        <Value>/conf/[APPLICATION]/pallycon_pack.key</Value>
    </Property>
    <!-- MPEG-DASH(Widevine, PlayReady), HLS(FairPlayStream) 사용 시 setting -->
    <!-- playready license manager-->
    <Property>
        <Name>LicenseManagerUrl</Name>
        <Value>https://license.pallycon.com/ri/playready/licenseManager.do</Value>
    </Property>
    <!-- multidrm pack manager-->
    <Property>
    <Name>PackManagerUrl</Name>
        <Value>https://license.pallycon.com/ri/packageManager.do</Value>
    </Property>
</Properties>
```

#### NCG용 Application module 추가

```xml
<Modules>
    ...
    <!-- NCG(HLS) 사용 시 setting -->
    <Module>
        <Name>NetsyncModule</Name>
        <Description>inka Module</Description>
        <Class>com.inka.wms.module.NetsyncModule</Class>
    </Module>
</Modules>
```

#### NCG용 Application property 추가

```xml
<Properties>
    ...
    <!-- property 추가 -->
    <Property>
        <Name>cupertinoEncryptionAPIBased</Name>
        <Value>true</Value>
        <Type>Boolean</Type>
    </Property>
    <Property>
        <Name>SiteID</Name>
        <Value>[SITEID]</Value>
    </Property>
    <Property>
        <Name>AccessKey</Name>
        <Value>[ACCESSKEY]</Value>
    </Property>
    <Property>
        <Name>PrivateKeyPath</Name>
        <Value>/conf/[APPLICATION]/pallycon_pack.key</Value>
    </Property>
    <!-- NCG(HLS) 사용 시 setting -->
    <!-- hls pack manager-->
    <Property>
        <Name>HLSKeyManagerUrl</Name>
        <Value>https://license.pallycon.com/ri/packageManager.do</Value>
    </Property>
    <!-- packager-->
    <Property>
        <Name>HLSKeyPackUrl</Name>
        <Value>https://license.pallycon.com/ri/hlskeyPackager.do</Value>
    </Property>
</Properties>
```

##### 입력 값 설명

|Name|Value|
|---|---|
|SITEID|PallyCon에서 발급된 서비스 사이트 ID(4byte)를 입력|
|ACCESSKEY|PallyCon Cloud 서비스 사이트 생성 시 발급되는 Access Key 값|
|APPLICATION|wowza application 명|

### Application 재시작 및 HTTPS URL 적용

세팅 완료 후 Wowza Application 을 재시작하면 세팅한 정보로 동작합니다.

> 참고 : DASH(.mpd) 또는 HLS(.m3u8) URL 등의 모든 스트리밍 관련 URL에는 HTTPS가 필수적으로 적용되어야 합니다. (브라우저 강제 사항)

***
