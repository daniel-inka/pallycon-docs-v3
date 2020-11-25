---
title: FairPlay iOS SDK 가이드
linktitle: FairPlay iOS SDK
summary: 본 문서는 FairPlay iOS SDK에 포함된 라이브러리와 샘플 프로젝트의 사용법에 대해 설명합니다.
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
lastmod: "2020-09-16T00:00:00Z"
draft: false
featured: true

menu:
  multidrm:
    parent: 클라이언트 연동
    weight: 30

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 30
---

## 개요

PallyCon FairPlay iOS SDK는 iOS용 미디어 서비스 앱을 개발할 때 Apple의 FairPlay Streaming (FPS) DRM을 쉽게 적용할 수 있게 해주는 제품입니다. 본 문서는 SDK에 포함된 라이브러리와 샘플 프로젝트의 사용법에 대해 설명합니다.

FPS 클라이언트와 연동되는 PallyCon 멀티 DRM 서비스에 대한 설명은 [라이선스 토큰 가이드](../../license/license-token)를 참고하시기 바랍니다.
SDK 사용과 관련한 기술 문의는 [헬프데스크](https://pallycon.zendesk.com) 사이트를 이용해 주시기 바랍니다.

> SDK 제품은 PallyCon 클라우드 상용 서비스 가입 시 신청할 수 있으며, 신청한 SDK는 PallyCon 콘솔 사이트의 `다운로드` 페이지에서 다운로드 받을 수 있습니다.

## 지원 환경

- iOS 9.0 이상
- ARC(Automatic Reference Counting) 적용 필요
- 본 SDK는 `Xcode 11`에서 테스트 되었으며, 애플 정책에 따라 시뮬레이터에서는 동작하지 않습니다.

> FPS DRM 동작 이외의 앱 테스트를 위해 시뮬레이터용 빌드가 필요한 경우, 다음과 같은 매크로를 이용해 FPS SDK 관련 코드를 빌드에서 제외시키고 FPS 라이브러리도 빌드 대상에서 제외해야 합니다.
> 
> #if !targetEnvironment(simulator)
> ...
> #endif

## 선행 사항

FPS License 발급 연동을 구축 하기 위해서는 콘텐츠 서비스 측에서 Apple로부터 아래 정보를 발급 받아 PallyCon 멀티DRM 서비스에 설정해 주어야 합니다.

1. Apple 개발자 계정 생성 ( <https://developer.apple.com/support/enrollment/> )
1. Apple에 `FPS Deployment Package` 신청 ( <https://developer.apple.com/streaming/fps/> )
1. `Deployment Package`를 이용해 생성한 다음 4가지 정보를 PallyCon 콘솔 사이트의 `연동 설정`에 등록

```s
- FPS Certificate 파일 (.der or .cer)
- Private key 파일 (.pem)
- Private key 암호 문자열
- Application secret key (ASK) 문자열
```

> **NOTE:**
>
> 위 등록 과정에 대한 상세한 설명은 [FairPlay 인증서 등록 자습서]({{<ref "fps-cert-tutorial.ko.md">}})를 참고하시기 바랍니다.

## 동영상 튜토리얼

FPS SDK를 iOS 앱에 적용하여 DRM 콘텐츠를 재생하는 튜토리얼 영상입니다.

{{< youtube 8hGu_1HQ1NY >}}

> 최적의 재생을 위해 화면 품질을 '1080p'로 선택하고 자막(한글 또는 영문)을 선택하여 재생하시기 바랍니다.

## 프로젝트 설정

다음과 같은 과정으로 PallyCon FPS SDK를 개발 프로젝트에 추가할 수 있습니다.

1. [PallyCon 콘솔 사이트](https://console.pallycon.com)에서 다운로드 받은 PallyCon FPS iOS SDK 파일의 압축을 풉니다.
1. `lib` 폴더에 포함된 `PallyConFPSSDK.framework` 또는 `PallyConFPSSDK.xcframework` 라이브러리를 프로젝트에 추가합니다.
  - 프로젝트 TARGETS의 `General` 탭에 `Linked Frameworks and Libraries`에 프레임웍을 추가합니다. 
  - 프로젝트 TARGETS의 `Build Settings` 탭에 `Framework Search Paths` 프레임웍 경로를 넣습니다.
1. 프로젝트 TARGETS의 `Build Settings` 탭에 `Enable Bitcode`를 `No`로 설정합니다.

> - 최신 버전의 Xcode를 사용하는 경우, `PallyConFPSSDK.xcframework` 라이브러리를 이용하면 호화성 문제를 해결할 수 있습니다.
> - 'Advanced Sample'을 빌드하기 위해서는 [GoogleCast Framework](https://developers.google.com/cast/docs/developers#ios)가 필요합니다.
> - [COCOAPODS](https://cocoapods.org/)를 이용하여 GoogleCast.framework를 설치(pod install)하고, 생성된 `PallyConFPSAdvanced.xcworkspace`를 실행합니다.

## 퀵 스타트

PallyCon FPS SDK를 이용하여 FPS HLS 콘텐츠를 스트리밍 재생하거나 다운로드하는 방법은 아래 코드와 같습니다.

### 콘텐츠 재생

```swift
// 1. PallyConFPS SDK 객체 생성합니다.
do {
	let pallyconSdk: PallyConFPS = try PallyConFPSSDK(siteId:"Site ID", siteKey:"Site Key", fpsLicenseDelegate:"PallyConFPSLicenseDelegate")
} catch PallyConSDKException.DatabaseProcessError(let message) {
	print("PallyConFPSSDK initialize failed.\n\(message)")
}

// 2. FPS 콘텐츠가 재생 되기 전에 FPS 콘텐츠 정보를 설정합니다.
pallyconSdk.prepare(urlAsset:"Asset", userId:"User ID", contentId:"ContentID", optionalId:"optionalId")
// 콘텐츠 정보가 아닌 토큰을 사용하는 경우, 토큰 정보를 설정하여 아래 메소드를 호출합니다.
// pallyconSdk.prepare(urlAsset:"Asset", userId:"User ID", contentId:"ContentID", token:"Token String")
// CustomData 을 직접 사용하는 경우, CustomData 정보를 설정하여 아래 메소드를 호출합니다.
// pallyconSdk.prepare(urlAsset:"Asset", customData:"CustomData")

```

### 콘텐츠 다운로드

```swift
// 1. PallyConFPS SDK 객체 생성합니다.
do {
    let pallyconSdk: PallyConFPS = try PallyConFPSSDK(siteId:"Site ID", siteKey:"Site Key", fpsLicenseDelegate:"PallyConFPSLicenseDelegate")
} catch PallyConSDKException.DatabaseProcessError(let message) {
    print("PallyConFPSSDK initialize failed.\n\(message)")
}

// 2. DownloadTask 객체 생성합니다.
// 콘텐츠 다운로드를 위해 DownloadTask 객체를 생성해서 사용합니다.
// DownloadTask 는 반드시 PallyConFPS 객체를 사용해서 생성해야만 합니다.
let downloadTask = pallyconSdk.createDownloadTask(url:"Content URL", userId:"User ID", contentId:"Content ID", optionalId:"Order ID", downloadDelegate:"PallyConFPSDownloadDelegate")
/**
콘텐츠 정보가 아닌 토큰을 사용하는 경우, 토큰 정보를 설정하여 아래 메소드를 호출합니다.
let downloadTask = pallyconSdk.createDownloadTask(url:"Content URL", userId:"User ID", contentId:"Content ID", token:"Token String", downloadDelegate:"PallyConFPSDownloadDelegate")
*/

// 3. 콘텐츠 다운로드 시작
// 이전에 다운로드 중단된 콘텐츠일 경우, 해당 콘텐츠를 이어서 받습니다.
downloadTask.resume()

// 4. 콘텐츠 다운로드 취소 (필요 시)
downloadTask.cancel()
```

### CustomData 정보 보기

```swift
// 1. PallyConFPS SDK 객체를 생성합니다.
do {
    let pallyconSdk: PallyConFPS = try PallyConFPSSDK(siteId:"Site ID", siteKey:"Site Key", fpsLicenseDelegate:"PallyConFPSLicenseDelegate")
} catch PallyConSDKException.DatabaseProcessError(let message) {
    print("PallyConFPSSDK initilize failed.\n\(message)")
}

// 2. CustomData 정보 가져오기
do {
    let customDataInfo: PallyConTokenInfo = try pallyconSdk?.getCustomDataInfo(from:"CustomData")
} catch PallyConSDKException.InvalidParameter() {
    print("PallyConFPSSDK invalid customData")
}
```

### Token 정보 보기

```swift
// Token 정보 가져오기
let tokenInfo: PallyConTokenInfo = PallyConFPSSDK.getTokenInfo(from:"Token String")
```

### 크롬캐스트 재생을 위한 CustomData 만들기

```swift
// 크롬캐스트 SDK 를 사용할 때 'GCKMediaInformation' 객체를 만들기 위해 필요한 CustomData 가져오기
// CustomData 는 반드시 PallyConFPS 객체를 사용해서 생성해야만 합니다.
let customData = pallyconSdk.getCustomDataForChromcast(userId: "User ID", contentId: "Content ID", optionalId: "Optional ID")
/**
콘텐츠 정보가 아닌 토큰을 사용하는 경우, 토큰 정보를 설정하여 아래 메소드를 호출합니다.
let customData = pallyconSdk.getCustomDataForChromcast(token: "Token String")
*/
```

## API 레퍼런스 문서

> PallyCon FPS iOS SDK에서 제공하는 모든 API에 대한 설명은 SDK 파일에 포함된 API 레퍼런스 문서를 참고하시기 바랍니다.
