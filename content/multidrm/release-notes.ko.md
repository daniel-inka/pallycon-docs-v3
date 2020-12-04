---
title: 멀티 DRM 릴리즈 노트
linktitle: 릴리즈 노트
summary: PallyCon 멀티 DRM 서비스의 각 제품 별 릴리즈 노트입니다.
toc: true
type: book
date: "2020-05-05T00:00:00+01:00"
lastmod: "2020-11-11T00:00:00Z"
draft: false
menu:
  multidrm:
    parent: 멀티DRM 가이드
    weight: 100

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 100
---

PallyCon 멀티 DRM 서비스의 각 제품 별 릴리즈 노트입니다.

## 서버 제품군

### DRM 라이선스 서버

|<div style="width:90px">날짜</div> |<div style="width:50px">버전</div> |내용 |
|:---|:---|:---|
| 2020.10.22 | 4.8.0 |- 키 로테이션 기능 제어를 위한 Policy 추가 <br>- FPS iOS 10 오프라인 시나리오 적용 <br>- Widevine override revocation 제어 policy 추가 및 Widevine proxy 통신 에러코드 추가|
| 2020.09.07 | 4.7.0 |- Widevine 라이선스 발급: 구글 보안 기준 미달 기기에 라이선스 발급 가능하게 패치<br>- 콜백 게이트웨이 Timeout 5초 -> 3초로 변경 |
| 2020.08.12 | 4.6.0 |- 글로벌 라이선스 URL 트라이얼 체크 기능 추가<br>- Shaka Player 3.0 대상 라이선스 발급 지원 |
| 2020.06.03 | 4.5.0 |- 오프라인 렌탈 시나리오 적용 |
| 2020.05.12 | 4.4.1 |- Elastic Search 로그 누락 현상 수정 |
| 2020.04.27 | 4.4.0 |- 멀티키 라이선스 발급 기능, Response format, License policy V2 지원 추가 |
| 2020.03.17 | 4.3.1 |- NcgCloud 지원 제거, DB Connection 방식 수정 |
| 2020.03.05 | 4.3.0 |- Widevine allowed_track_type 기능 추가<br>- 라이선스 토큰 내 'token' 키에 'policy'도 추가로 지원<br>- FairPlay 오류 코드 추가<br>- 토큰 해시 값 체크 시 drm type 대소문자 무관하게 지원<br>- 라이선스 발급 시 7107, 7103 오류 중복 발생 수정 및 누락된 오류 코드 추가<br>- FairPlay 라이선스 발급 시 오류 수정 |
| 2019.10.22 | 4.2.0 |- Widevine 하드웨어 DRM 설정 시 오디오 트랙은 hdcp_none, security level 1로 적용 <br>- 트라이얼 계정에 대한 패키징 콜백 자동 처리 시 오류 수정 (파일 확장자가 없는 경우)<br>- 클라이언트 SDK 인증 관련 오류 수정|
| 2019.01.15 | 4.1.8 |- NCG 토큰 관련 오류 수정<br>- 클라이언트 SDK 인증 쿼리 오류 수정|
| 2018.09.19 | 4.1.3 |- Shaka Packager 연동을 위해 IV값을 hex 형식으로 응답하도록 수정 |
| 2018.08.09 | 4.1.1 |- 멀티키 라이선스 발급 기능 추가 (PlayReady 제외)<br>- FairPlay의 경우, 패키징 시 iv 값을 사이트 당 하나로 사용<br>- 실시간 HLS 키 발급 조건에 CID가 있을 경우, CID 기준 발급 기능 추가<br>- 클라이언트 SDK 인증 기능 지원|
| 2018.04.09 | 4.0.0 |- 토큰 기반 라이선스 발급 시 DB 저장 없이 처리<br>- 키 로테이션 기능 추가. pallycon-customdata-v2 규격에 content_type:live 일 경우에 활성화 (기본값: VOD) |

### KMS 서버

|<div style="width:90px">날짜</div> |<div style="width:50px">버전</div> |내용 |
|:---|:---|:---|
| 2020.11.11 | 1.6.0 |- PallyCon KMS V2 싱글키 지원<br>- Widevine response에 PSSH DATA Element 추가|
| 2020.04.29 | 1.5.0 |- 멀티키 패키징 지원 (KMS V2), PallyCon CPIX API에 멀티키 기능 추가(V2)<br>- Bitmovin Encoder SDK, PallyCon CPIX V2 연동 샘플 제공 |
| 2019.01.29 | 1.4.0 |- PallyCon CPIX 연동 규격 추가 |

### CLI 패키져

|<div style="width:90px">날짜</div> |<div style="width:50px">버전</div> |내용 |
|:---|:---|:---|
| 2020.09.17 | 3.6.3 |- DASH mpd의 각 트랙에 평균 대역폭을 적용하는 기능 추가<br>- 서버에서 PSSH를 전달받는 대신 패키저에서 직접 생성하도록 변경 |
| 2020.07.10 | 3.6.2 |- CMAF(cbcs) 기능을 위해 PlayReady 헤더 4.3.0.0 버전 지원 |
| 2020.05.27 | 3.6.1 |- HLS 패키징에 HEVC 코덱 (fMP4 컨테이너) 지원 추가<br>- 입력 파일 경로 파라미터 인코딩 개선 <br>- C++14 적용을 위해 gcc 버전 변경 (v4.8.5 -> v7.3.1) |
| 2020.04.27 | 3.6.0 |- 트랙 별 다중 Manifest 생성 기능 추가<br>- 멀티 키 패키징 기능 정식 추가<br>- 비디오 비트레이트(대역폭) 설정 기능 추가 |
| 2020.03.06 | 3.5.1 |- 멀티 키 패키징을 위해 해상도 (SD, HD, UHD1) 별 최대 영상 높이 (max height) 설정 기능 추가 |
| 2020.02.26 | 3.5.0 |- DASH 패키징에 WebM 지원, HLS 트랙 (오디오, 자막) 이름 설정 기능 추가 |
| 2019.09.10 | 3.4.1 |- Widevine 멀티키 패키징 추가, 오디오 트랙 비암호화 (skip audio encryption) 옵션 추가 |
| 2019.08.30 | 3.4.0 |- 멀티 비트레이트 라이브 스트림 패키징 기능 추가<br>- PlayReady 관련 버그 수정 |
| 2019.05.08 | 3.3.1 |- Widevine PSSH 관련 버그 수정 |
| 2019.04.26 | 3.3.0 |- CMAF 패키징 기능 추가 |
| 2019.02.27 | 3.2.0 |- 소스코드 리팩토링 및 버그 수정|
| 2018.12.27 | 3.1.1 |- 버그 수정 |
| 2018.11.30 | 3.1.0 |- PSSH 생성 기능 추가, 패키징 진행 상황 표시, 다국어 자막 지원|
| 2018.10.19 | 3.0.0 |- Shaka Packager 기반으로 코어 모듈 변경|

### Wowza 연동 SDK

|<div style="width:90px">날짜</div> |<div style="width:50px">버전</div> |내용 |
|:---|:---|:---|
| 2018.12.06 | 1.2.2 |- HLS 라이브 스트림에 NCG 적용 시 버그 수정 |
| 2018.06.15 | 1.2.0 |- FairPlay Streaming 용 HLS 패키징 지원 |
| 2018.04.24 | 1.1.0 |- 키 매니저 에러 처리과 관련한 보안 개선|
| 2018.03.12 | 1.0.0 |- 최초 릴리즈 버전 |

## 클라이언트 제품군

### Widevine Android SDK

|<div style="width:90px">날짜</div> |<div style="width:50px">버전</div> |내용 |
|:---|:---|:---|
| 2020.09.25 | 1.12.0 |- ExoPlayer 2.11.8 버전을 지원합니다.|
| 2020.08.12 | 1.11.0 |- 멀티 키 콘텐츠와 오프라인 렌탈 시나리오를 지원합니다.<br>- v1.10.1에 추가되었던 existDownloadLicense 함수가 제거되고, 새롭게 getOfflineLicenseExpireDate 함수가 추가되었습니다.|
| 2020.07.27 | 1.10.2 |- 구글 정책에 따라 Target 28 -> 29로 변경됩니다.<br>- 콘텐츠가 저장될 폴더명을 지정할 수 있는 PallyConDownloadTask API가 추가됩니다.<br>- 콘텐츠 URL 분석 처리가 수정됩니다. |
| 2020.05.12 | 1.10.1 |- SDK에 적용된 Exoplayer 버전이 2.10.4 -> 2.10.8 로 변경됩니다. |
| 2019.12.11 | 1.10.0 |- existDownloadedLicense API가 추가되어 DB에 라이선스 존재 여부를 알수 있습니다. |
| 2019.10.07 | 1.9.0 |- Exoplayer 2.10.x 버전 업데이트로 AndroidX, Android API 28, Gradle 5.4.1, Gradle Plugin 3.5.0 이 적용되었습니다.<br>- setEnableLog 메소드가 제거 되었습니다.<br>- SDK 업데이트로 Android/AndroidTV Sample도 함께 업데이트 되었습니다. 변경 사항은 각 샘플을 확인하시기 바랍니다. |
| 2019.09.09 | 1.8.0 |- removeLicense(), removeLicenseByCustomData(), removeLicenseByProxy() 메소드에서 네트워크 연결 없이 라이선스 삭제가 가능합니다. |
| 2019.08.16 | 1.7.0 |- PallyConWVSDK 크기를 최적화는 작업이 진행되었습니다.<br>- MpdTrackSelector 클래스에 setCookie() 함수가 추가했습니다.<br>- PallyconLoggingListener가 추가 되어 내부 로그를 확인할 수 있습니다.<br>- PallyconWVMSDK.setEnableLog 함수가 다음 버전에서 제외됩니다.  |
| 2019.07.12 | 1.6.8 |- SetCookie 함수의 오류를 수정했습니다. |
| 2019.06.20 | 1.6.7 |- Token 으로 라이선스를 다운로드 받을 수 있는 downloadLicenseByToken() 함수를 추가했습니다. |
| 2019.06.11 | 1.6.6 |- MediaDrmCallback 이 동작하는 SDK API에 설정된 Cookie가 적용되도록 변경되었습니다.<br>- setCookie함수에 예외처리가 추가되었습니다.<br>- enableLog함수명이 setEnableLog으로 변경하였으며, 예외처리가 추가되었습니다. |
| 2019.06.05 | 1.6.5 |- 64비트 지원 추가 |
| 2019.04.24 | 1.6.4 |- 안드로이드 4.4 ~ 5.0 버전 단말에서 발생하는 앱 설치 오류를 수정 |
| 2019.02.13 | 1.6.3 |- 콘텐츠 다운로드 완료 후 호출되는 콜백함수 추가<br>- ExoPlayer 2.9.3 적용<br>- Gradle version 4.6, Android Plugin version 3.2.1, compileSdkVersion 27 적용<br>- Android 9.0에서 실행 시 발생하는 내부 오류 수정 |
| 2018.12.27 | 1.6.1 |- 어댑티브 DASH 콘텐츠를 다운로드 할 때 해상도를 선택할 수 있는 기능이 추가 되었습니다. |
| 2018.09.19 | 1.6.0 |- Simple 버전 샘플 프로젝트 추가. 기존 샘플보다 쉽게 SDK 기본 기능을 체험할 수 있습니다.<br>- 대상 사이트가 SDK 사용 권한이 있는지 확인하기 위해 SDK 인증 기능을 추가했습니다. |

### FPS iOS SDK

|<div style="width:90px">날짜</div> |<div style="width:50px">버전</div> |내용 |
|:---|:---|:---|
| 2020.09.25 | 1.9.0 |- 1.8.5 버전에 추가되었던 existDownloadedLicense 함수가 제거되었습니다. 새로 추가된 getOfflineLicenseExpireDate 함수를 통해 라이선스 유무와 만료 날짜를 알 수 있습니다.<br>- getOfflineLicenseExpireDate 함수가 추가되었습니다.<br>- 멀티키로 패키징된 콘텐츠의 라이선스 획득과 재생을 지원합니다.|
| 2020.07.06 | 1.8.8 |- SDK가 Xcode 11.5 와 swift 5.2.4가 반영 됩니다.<br>- XCframework SDK 가 추가됩니다. |
| 2020.02.24 | 1.8.7 |- 컨텐츠 다운로드와 라이선스 다운로드가 함께 동작하도록 수정되었습니다. |
| 2020.01.22 | 1.8.6 |- 컨텐츠 다운로드 시 Cellular 사용을 제한 할 수 있습니다.<br>- 시뮬레이터 라이브러리(x86_64, i386)가 제거된 SDK가 배포됩니다. |
| 2020.12.11 | 1.8.5 |- 라이선스 존재 여부를 알수 있는 existDownloadedLicense 가 추가됩니다. |
| 2019.11.07 | 1.8.4 |- Xcode 11.2, Swift 5.2 에서 빌드된 iOS/tvOS SDK를 배포합니다. |
| 2019.10.18 | 1.8.3 |- Xcode 11, Swift 5.1 에서 빌드된 iOS/tvOS SDK를 배포합니다. |
| 2019.07.29 | 1.8.2 |- iOS 9 타겟에서 Symbol not found 발생하는 오류 수정하였습니다. |
| 2019.07.19 | 1.8.1 |- 저장된 라이선스가 삭제되지 않는 버그를 수정하였습니다. |
| 2019.06.01 | 1.8.0 |- SDK 인증 과정을 제거했습니다. |
| 2019.04.05 | 1.7.0 |- Swift 5.0 버전을 지원합니다.<br>- 다운로드 기능의 버그를 수정하였습니다. |
| 2019.01.28 | 1.6.5 |- KeyChain에 접근 권한을 '항상 접근'으로 변경하였습니다. |
| 2019.01.05 | 1.6.4 |- SDK 인증 관련 일부 기능이 수정되었습니다.|
| 2018.12.20 | 1.6.3 |- 어댑티브 스트림 콘텐츠의 특정 Bandwidth 파일만 다운로드 받을 수 있도록 파라미터가 추가되었습니다. |
| 2018.11.29 | 1.6.2 |- 초기 SDK 정보 획득 시 저장된 KeyChain 정보를 초기화 하도록 수정했습니다.<br>- 인증 실패 시 KeyChain 에 저장된 정보를 초기화 하도록 수정했습니다. |
| 2018.11.21 | 1.6.1 |- SDK 인증 시 키체인 저장과 관련된 오류를 수정하였습니다.<br>- Download API의 오류를 수정하였습니다. |
| 2018.09.19 | 1.6.0 |- Simple 버전 샘플 프로젝트 추가. 기존 샘플보다 쉽게 SDK 기본 기능을 체험할 수 있습니다.<br>- Swift 4.2, iOS 12 지원. Xcode 10, Swift 4.2 로 빌드 되었습니다.<br>- 대상 사이트가 SDK 사용 권한이 있는지 확인하기 위해 SDK 인증 기능을 추가했습니다. |

### NCG Android SDK

|<div style="width:90px">날짜</div> |<div style="width:50px">버전</div> |내용 |
|:---|:---|:---|
| 2020.09.03 | 2.6.3 |- SDK Sample 프로젝트에 scoped storage 권한이 추가됩니다.|
| 2020.08.12 | 2.6.2 |- Token으로 라이선스 획득이 가능한 API가 추가됩니다.|
| 2020.07.01 | 2.6.1 |- 샘플 프로젝트 오류를 수정하여 반영합니다.|
| 2020.05.28 | 2.6.0 |- Android 루팅 확인 시 강도를 지정할 수 있는 setRootingCheckLevel() API가 추가됩니다. |
| 2020.04.29 | 2.5.5 |- 구글 정책에 따라 타겟 버전이 29로 변경됩니다. <br>- Android Q(API 29)부터 Widevine UUID를 Device ID로 사용합니다.| 
| 2020.04.02 | 2.5.4 |- NCG Core 에 m3u8 파일에서 EXT-X-MAP에 있는 URI 추출하는 함수가 추가되어 NCG SDK에 반영됩니다.<br>- NCG Advanced 샘플이 HLS 콘텐츠 여부에 따라 각각 다른 플레이어가 호출되도록 변경됩니다. |
| 2020.03.13 | 2.5.3 |- 안드로이드 루팅 체크가 변경됩니다. | 
| 2020.02.05 | 2.5.2 |- Secure Time 관련 로그가 NcgExceptionalEventListener로 출력됩니다.<br>- NCG Core에 모든 로그가 출력되지 않습니다. |
| 2019.11.11 | 2.5.1 |- Android Target Sdk Version이 27에서 28로 변경됩니다.<br>- AndroidX 가 적용되었습니다.<br>- 일부 디바이스에서 외부 출력 및 루팅 확인이 안되는 문제를 수정했습니다. |
| 2019.10.07 | 2.5.0 |- 로컬 웹서버에서 크래시 발생을 제거했습니다. |
| 2019.06.05 | 2.4.0 |- Android 64bit(arm64-v8a, x86_64) 지원<br>2019년 8월부터 Android 64bit 지원이 필수가 되어 NCG SDK에 Android ABI arm64-v8a, x86_64 가 포함되었습니다. 8월부터 구글 플레이에 앱을 등록하려면 NCG SDK v2.4.0 이상을 반드시 적용해야 합니다.<br>- Android API Level 변경(API 15 -> 16)<br>NCG SDK 64bit를 지원하면서 NDK에서 최소 지원이 Android API Lebel 16 으로 변경되었습니다. 64bit 에서 Android 최소 지원은 Android API Lebel 21 입니다. |
| 2019.02.27 | 2.3.2 |- 안드로이드 9.0 버전 지원이 추가되었습니다. |
| 2018.12.27 | 2.3.1 |- Android 에뮬레이터에서 실행되는 것을 차단하는 보안 패치가 추가되었습니다. |
| 2018.09.19 | 2.3.0 |- Simple 버전 샘플 프로젝트 추가. 기존 샘플보다 쉽게 SDK 기본 기능을 체험할 수 있습니다.<br>- 시큐어타임 로직 개선. 일부 단말에서 오프라인 재생 시 보안이 강화되었습니다. |

### NCG iOS SDK

|<div style="width:90px">날짜</div> |<div style="width:50px">버전</div> |내용 |
|:---|:---|:---|
| 2019.10.08 | 2.5.0 |- 로컬 웹서버에서 크래시 발생을 제거했습니다. |
| 2019.01.22 | 2.4.2 |- 토큰 방식의 라이선스 획득을 위한 새로운 함수가 추가 되었습니다. (acquireLicenseByToken) |
| 2018.11.27 | 2.4.1 |- RODB 파일이 손상되거나 수정 된 경우 복구 프로세스가 업데이트되었습니다. |
| 2018.09.19 | 2.4.0 |- Simple 버전 샘플 프로젝트 추가. 기존 샘플보다 쉽게 SDK 기본 기능을 체험할 수 있습니다.<br>- 시큐어타임 로직 개선. 일부 단말에서 오프라인 재생 시 보안이 강화되었습니다.<br>- Swift 4.2, iOS 12 지원. Xcode 10, Swift 4.2 로 빌드 되었습니다.<br>- Xcode 10 버전 C++ 라이브러리 이름 변경. Xcode 10버전부터 기존의 libstdc++에서 libc++로 변경되었습니다. |
