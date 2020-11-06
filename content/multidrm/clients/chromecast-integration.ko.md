---
title: "크롬캐스트 연동 가이드"
date: 2018-08-28T16:12:37+09:00
draft: false
weight: 80
---

## 개요

본 문서는 FairPlay iOS SDK와 Widevine Android SDK에서 크롬캐스트를 연동하는 방법을 설명합니다.

## 지원 환경

- PallyCon FPS SDK for iOS (iOS 9.0 이상)
- PallyCon Widevine SDK (Android 4.4 이상)

## 확인 사항

- Android 기기의 경우, '[Google Play 서비스](https://play.google.com/store/apps/details?id=com.google.android.gms)' 최신 버전이 설치되어 있어야 합니다.

- Chromecast는 Apple FairPlay DRM을 지원하지 않습니다. iOS 기기에서 FairPlay DRM 콘텐츠를 Chromecast로 전송하기 위해서는, 동일한 콘텐츠가 Widevine으로 패키징되어 있어야 합니다.

- Chromecast와 모바일 기기는 동일한 Wi-Fi에 연결되어 있어야 합니다. 기기 설정을 위하여 'Google Home'을 모바일 기기에 설치해야 할 수 있습니다. 설치 후 앱에서 제공하는 가이드를 따르십시요.

- 'Google Home'은 [Play Store](https://play.google.com/store/apps/details?id=com.google.android.apps.chromecast.app), [App Store](https://itunes.apple.com/us/app/google-home/id680819774)에서 받으실 수 있습니다.

- Chromecast를 지원하는 구글 기기([Chromecast Ultra](https://store.google.com/us/product/chromecast_ultra) 또는 [Chromecast 2세대](https://store.google.com/us/product/chromecast_2015)), 스마트 TV, OTT 장비(Nexus Player, CJ Viewing 등)에 대한 정보는 해당 제조사의 가이드 문서를 참고하십시요.

## Chromecast 연동

- Google이 기본 제공하는 리시버 앱은 DRM을 지원하지 않습니다. PallyCon SDK와 연동하기 위해서는, Media Player Library를 사용하여 Custom Receiver 앱을 제작하여야 합니다.

- 빠른 연동 테스트를 위해 PallyCon SDK와 연동되는 Custom Receiver 앱을 기본 제공합니다. 앱 ID는 '1CDBBCB6'이며, PallyCon SDK의 샘플 앱에 적용되어 있습니다.

- PallyCon Multi-DRM이 적용된 콘텐츠를 Custom Receiver 앱에서 재생하기 위해서는, 라이선스 발급에 사용되는 Custom data를 Custom Receiver 앱으로 전송해야 합니다.

  - Custom data에 대한 자세한 정보는 [여기]({{%ref "multidrm-license.ko.md#pallycon-custom-data-v2"%}})를 방문하여 확인하십시요.
  - Sender 앱에서 Receiver 앱으로 Custom data를 전송하는 방법은 PallyCon 샘플 앱을 참고하십시요.
  - Receiver 앱에서 Custom data를 처리하는 방법은 [Google Cast Guides - DRM](https://developers.google.com/cast/docs/player#drm-playreadywidevine)과 PallyCon Custom Receiver 앱을 참고하십시요.
  
- PallyCon Custom Receiver 앱을 수정하신 후, [Google Cast SDK Developer Console](https://cast.google.com/publish/)에 등록하여 사용하실 수 있습니다.

- Sender 및 Receiver 앱 개발에 관한 자세한 내용은 [Google Cast Guides](https://developers.google.com/cast/docs/developers) 및 PallyCon SDK 샘플 앱을 참고하십시요.

- Custom Receiver 앱 등록에 관한 자세한 내용은 [Google Cast Guides - Registration](https://developers.google.com/cast/docs/registration)을 참고하십시요.

***