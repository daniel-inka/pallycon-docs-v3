---
title: 지원 환경
linktitle: 지원 환경
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  multidrm:
    parent: 멀티DRM 시작하기
    weight: 3

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 3
---

PallyCon 멀티 DRM 제품은 다음과 같은 클라이언트 플랫폼과 OS를 지원합니다.

## PC 플랫폼

|OS |웹브라우저 또는 SDK |스트리밍 방식 |DRM |다운로드 시나리오 |기타 |
|:---|:---|:---|:---|:---|:---|
|Windows 8.1 이상 |IE11, 엣지 |MPEG-DASH |PlayReady |지원 안함 | |
|Windows 7.0 이상, Mac OS |크롬 v35 이상, 파이어폭스 v47 이상 |MPEG-DASH |Widevine Modular |지원 안함 | |
|Windows 7.0 이상 |NCG Windows SDK |HLS 또는 프로그래시브 다운로드 |Netsync Content Guard |지원 |영상 재생 기능은 별도 구현 필요 |
|Windows 10 | PallyCon PlayReady UWP SDK | MPEG-DASH | PlayReady | 지원 | 오프라인 시나리오는 지원 안함|
|Mac OS 10.10 이상 |Safari v8 이상 |HLS |FairPlay Streaming |지원 안함 |. |

## 모바일 플랫폼

|OS |웹브라우저 또는 SDK |스트리밍 방식 |DRM |다운로드 시나리오 |기타 |
|:---|:---|:---|:---|:---|:---|
|Android 4.4 이상 |PallyCon Widevine SDK |MPEG-DASH |Widevine Modular |Android 5.0부터 지원 |구글 미인증 기기는 지원 안됨 |
|Android 4.0 이상 |NCG Android SDK |HLS 또는 프로그래시브 다운로드 |Netsync Content Guard |지원 |구글 미인증 기기 지원 가능 |
|Android 4.4 이상 |크롬 v57 이상 |MPEG-DASH | Widevine Modular | PWA로 지원 가능 |Widevine L1은 크롬 v62부터 지원 |
|iOS 9.0 이상 |PallyCon FPS SDK for iOS |HLS |FairPlay Streaming |iOS 10.0부터 지원 | |
|iOS 9.0 이상 |NCG iOS SDK |HLS 또는 프로그래시브 다운로드 |Netsync Content Guard |지원 | |
|iOS 11.2 이상 | Safari v11.2(iOS 버전과 동일) 이상 |HLS | FairPlay Streaming | 지원 안함 |. |

## OTT 플랫폼

|OS / 플랫폼 |클라이언트 SDK |스트리밍 방식 |DRM |다운로드 시나리오 |기타 |
|:---|:---|:---|:---|:---|:---|
|Android TV 4.4 이상 |PallyCon Widevine SDK |MPEG-DASH |Widevine Modular |필요 시 지원 가능 (5.0 이상) |구글 미인증 기기는 지원 안됨 |
|Android 4.0 이상 |NCG Android SDK |HLS 또는 프로그래시브 다운로드 |Netsync Content Guard |필요 시 지원 가능 |구글 미인증 기기 지원 |
|Amazon Fire TV |PallyCon Widevine SDK |MPEG-DASH |Widevine Modular | 지원 안함 | |
|tvOS 10.0 이상(애플TV 4세대) |PallyCon FPS SDK for tvOS |HLS |FairPlay Streaming |지원 안함 | |
|삼성 스마트TV (타이젠OS 2.3 이상) | 네이티브 직접 연동 | MPEG-DASH | PlayReady | 지원 안함 | |
|삼성 스마트TV (타이젠OS 3.0 이상) | 네이티브 직접 연동 | MPEG-DASH | Widevine Modular | 지원 안함 | |
|LG 스마트TV (webOS 3.0 이상) | 네이티브 직접 연동 | MPEG-DASH | PlayReady | 지원 안함 | |
|Roku OS 8.0 이상 | PallyCon PlayReady Roku SDK | MPEG-DASH | PlayReady | 지원 안함 | |
|Xbox One | PallyCon PlayReady UWP SDK | MPEG-DASH | PlayReady | 지원 안함 | |
|ChromeCast | 커스텀 리시버 및 센더 샘플 | MPEG-DASH | PlayReady 또는 Widevine Modular | 지원 안함 | . |

## CMAF (Common Media Application Format) 단일 콘텐츠 지원 플랫폼

|OS |웹브라우저 또는 SDK |스트리밍 방식 |DRM |
|:---|:---|:---|:---|
|Android 7.1 이상 |PallyCon Widevine Android SDK |DASH 또는 HLS |Widevine Modular |
|Android TV 7.1 이상 |PallyCon Widevine AndroidTV SDK |DASH 또는 HLS |Widevine Modular |
|iOS 10.0 이상 |사파리 V10 이상, PallyCon FPS iOS SDK |HLS |FairPlay Streaming |
|tvOS 10.0 이상 |사파리 V10 이상, PallyCon FPS tvOS SDK |HLS |FairPlay Streaming |
|Windows 7.0, macOS 10.10 이상 | 크롬 68 버전 이상 |DASH 또는 HLS | Widevine Modular |
|macOS 10.12 이상 | 사파리 V10 이상 |HLS | FairPlay Streaming |

## HTTPS 필수 적용

구글 크롬 등 웹 브라우저의 보안 강화 방침에 따라, 스트리밍 URL 등 콘텐츠 서비스와 관련된 **모든 URL에는 기본적으로 HTTPS가 적용**되어야 합니다. (예: DASH 스트리밍용 mpd URL, HLS 스트리밍용 m3u8 URL, NCG 다운로드/PD용 ncg 파일 URL)

***