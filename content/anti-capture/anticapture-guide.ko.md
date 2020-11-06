---
linktitle: PallyCon 안티 캡쳐 가이드
weight: 2

# Page metadata.
title: PallyCon 안티 캡쳐 가이드
date: "2018-09-09T00:00:00Z"
lastmod: "2018-09-09T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: true  # Show table of contents? true/false
type: book  # Do not modify.

menu:
  anticapture:
    weight: 2
    parent: 안티캡쳐
---

## 개요

`PallyCon 안티 캡쳐` 제품은 윈도우즈 크롬 브라우저(이하 크롬)에서 Widevine DRM 동영상 재생 시, 녹화 프로그램을 모니터링 할 수 있는 제품입니다.

이 문서에서는 안티캡처 제품 다운로드와 설치 방법을 설명하고, 동작 과정 및 플레이어에 적용하는 방법을 설명합니다.

## 제품 구성

`PallyCon 안티 캡쳐` 제품은 크롬 브라우저 확장 프로그램과 자바스크립트 API, 그리고 윈도우즈 서비스 어플리케이션으로 구성됩니다.

- PallyConLib.js - 크롬 확장 프로그램과 메시지를 주고 받는 JavaScript API
- PallyCon AntiCapture 확장 프로그램 - 크롬 브라우저 확장 (크롬 웹 스토어에서 설치)
- PallyCon AntiCapture Service - 윈도우즈 서비스 기반 어플리케이션

![Anti Capture Modules](/docs/images/pallycon-anti-capture-modules.png)

웹 페이지에서 `PallyConLib.js` API는 확장 프로그램으로 메시지를 보내고 PC에 설치된 안티캡쳐 프로그램에 메시지를 보냅니다. 안티캡쳐 프로그램은 요청된 메시지에 결과를 확장 프로그램에 반환하고 확장 프로그램은 다시 결과를 웹 페이지에 반환됩니다.

예를 들어 안티캡쳐 설치 여부, 모니터링 시작/멈춤 등 메시지를 보내면 확장 프로그램을 통해 안티캡쳐 프로그램이 메시지를 받아 동작하고, 결과가 반환됩니다.

## 샘플 구성

`PallyCon 안티 캡쳐` 제품은 다음과 같은 테스트용 샘플을 제공합니다. 샘플을 통해 안티 캡쳐 기능을 이해하고, 실제 서비스 개발 시 참고할 수 있습니다.

- 크롬 확장 프로그램과 메시지를 주고 받기 위한 라이브러리
  - PallyConLib.js

- DRM DASH 컨텐츠 재생하기 위한 라이브러리
  - video.js, dash.all.min.js, videojs-dash.js

- 페이지에 적용된 기타 라이브러리
  - bootstrap.min.css, jquery.js, style.css, video-js.css 

## 실행 환경

`PallyCon 안티 캡쳐`를 적용하기 위해 필요한 클라이언트 환경은 다음과 같습니다.

- Windows 7, Windows 8.1, Windows 10
- 크롬 브라우저 79 버전 이후 (2019년 12월 기준 최신 버전)

### 프로그램 설치

클라이언트 시스템에 다음과 같이  관련 프로그램이 설치되어야 합니다.

- 크롬 확장 프로그램: 크롬 브라우저에서 `크롬 웹 스토어`에 접속해 `PallyCon AntiCapture` 확장 프로그램을 설치합니다.
- 서비스 어플리케이션: AntiCaptureService_Setup.exe 파일을 다운로드해 설치합니다.

## 안티 캡쳐 모니터링 과정

위와 같은 클라이언트 환경에서 안티 캡쳐가 적용된 HTML5 영상을 재생하면, 아래와 같은 과정이 지속적으로 반복되면서 화면 녹화를 모니터링 합니다.

![Anti Capture Process](/docs/images/pallycon-anti-capture-process.png)

1. 콘텐츠를 재생하는 플레이어가 PallyConLib.js API를 호출하여 확장 프로그램에게 메시지를 보냅니다.

2. 이 메시지는 네이티브 메시지 호스트(Native Message Host)가 받아 PC에 설치된 안티캡쳐 서비스에게 전달합니다. 

3. 메시지를 받은 안티캡쳐 서비스는 모니터링한 결과를 반환합니다.

4. 결과를 받은 네이티브 메시지 호스트는 확장 프로그램에 전달하고, 이슈가 있을 경우 이벤트를 발생시킵니다.

## 테스트 환경 구축

Node.js를 활용하여 `PallyCon 안티 캡쳐` 제품을 로컬에서 테스트 할 수 있습니다.

1. Node.js를 다운로드하여 설치합니다.

2. 터미널을 열고 배포된 `sample` 폴더로 이동 후 `npm install` 명령을 실행합니다.
 - 샘플을 동작시키기 위한 모듈들이 다운로드되어 설치됩니다.

3. 다운로드가 완료되면 `node app` 명령어를 실행합니다. (아래 그림 참고)
  - 모듈이 없다고 표시되면 `npm install 모듈명` 을 실행하여 설치합니다.
  - 설치가 완료되면 `node app` 명령어를 실행합니다.

4. 브라우저에서 `http://localhost:3000/index.htm` URL을 호출합니다.

![Anti Capture Setup](/docs/images/pallycon-anti-capture-setup.png)

### 상용 서비스 적용

PallyCon 데모 페이지 또는 로컬 환경 이외에 고객사의 웹 페이지에 안티캡쳐를 적용하려면, 우선 PallyCon 안티 캡쳐 크롬 확장에 해당 도메인이 등록되어야 합니다.

PallyCon 안티 캡쳐 제품을 이용하려는 고객은 별도 컨택을 통해 등록할 도메인을 알려주시기 바랍니다.

> 로컬 환경이 아닌 실제 서비스에 적용하려면 플레이어 웹페이지에 http 대신 https URL을 사용해야 합니다. (SSL/TSL 적용 필요)

## PallyConLib.js 모듈

`PallyConLib.js` API는 크롬 확장 프로그램을 통해 PC에 설치된 안티캡쳐 프로그램에 메시지를 전달합니다. 결과가 반환되면 다시 확장 프로그램을 통해 `PallyConLib.js`가 결과를 받아 처리합니다.

아래 표시된 진행 단계별 API를 콘텐츠 재생 준비에서 재생 종료에 걸쳐 해당 웹페이지에 적용해야 합니다.

|진행 단계 | PallyConLib.js API |설명 |
|---|---|---|
| 1. 페이지 로드 | CheckInstall | 페이지가 로드될 때 안티캡쳐 설치 여부를 확인합니다. |
| 2. 재생 준비 | ActivateAntiCapture | 영상 재생 시작 전에 설치된 안티 캡쳐 프로그램을 활성화 합니다. |
| 3. 재생 시작 | StartMonitoring | 영상이 재생되면 모니터링을 시작합니다. `PallyConLib.js`는 내부적으로 5초마다 `CheckCaptureStatus`를 호출하여 화면 녹화 여부를 체크합니다. 화면 녹화가 감지되면 이벤트가 발생합니다. |
| 4. 재생 종료 후| StopMonitoring | 영상 재생이 완료/중지되면 모니터링을 멈춥니다. |
| 5. 비활성화 | DeactivateAntiCapture | 더 이상 콘텐츠 재생이 없을 경우, DeactivateAntiCapture API로 모니터링을 비활성화합니다. |

### API 설명

|API 이름 | 설명 |
|---|---|
|CheckInstall | PC에 안티캡쳐가 설치되어 있는지를 확장 프로그램을 통해 확인합니다. NOK일 경우 영상 재생을 비활성화하고 안티캡쳐 프로그램을 설치하도록 안내해야 합니다. |
|ActivateAntiCapture |확장 프로그램을 통해 PC에 설치된 안티캡쳐 서비스의 모니터링을 활성화합니다. |
|DeactivateAntiCapture |확장 프로그램을 통해 PC에 설치된 안티캡쳐 서비스의 모니터링을 비활성화합니다. |
|CheckCaptureStatus | 안티캡쳐 프로그램이 모니터링한 정보를 확인하여 결과를 반환합니다. |
|SetMonitoringInterval | 안티캡쳐 서비스의 모니터링 주기를 변경합니다. (기본 5초) 5초 이하의 주기는 시스템 리소스의 과중한 사용으로 추천하지 않습니다. |
|StartMonitoring |CheckCaptureStatus API를 5초마다 호출하여 지속적으로 모니터링 정보를 확인합니다. ActivateAntiCapture을 호출하지 않은 상태에서 StartMonitoring을 호출하면 에러가 발생합니다. |
|StopMonitoring |5초마다 호출되는 CheckCaptureStatus API가 중지됩니다. StopMonitoring 호출 전에 DeactivateAntiCapture을 호출하면 에러가 발생합니다. |

## 예제

`Video.js` 플레이어에 `PallyConLib.js`를 적용한 예제 코드입니다.

```js
<script type="text/javascript" src="PallyConCE.js"></script>
<script src="video.js"></script>
<script src="dash.all.min.js"></script>
<script src="videojs-dash.js"></script>

<script type="text/javascript">
    // PallyConLib.js 이벤트 리스너
    window.addEventListener("message", function (event) {
        if (event.source != window)
            return;

        if (event.data.type && (event.data.type == "PallyCon")) {
            if (event.data.isError) {
                if (typeof player != 'undefined') {
                    player.pause();
                }
                window.alert('[PallyCon] Extension Error : ' + event.data.msg);
            } else {
                console.log('[PallyCon] Extension Message : ' + event.data.msg);
            }
        }
    }, false);

    // 1. 페이지가 로드되면 Anti capture 프로그램 설치 여부를 확인합니다.
    function CheckInstall() {
        pal.CheckInstall();
    }

    var player;
    function playPlayer() {

        // 2. 플레이어가 준비하는 동안 또는 재생 전에 Anti capture를 활성화 합니다.
        pal.ActivateAntiCapture();

        player = videojs('videoPlayer');
        player.ready(function () {
            player.src('DASH URL Source');
        }

        player.on('play', function () {
            // 3. 재생이 시작되면 지속적인 모니터링을 시작합니다.
            pal.StartMonitoring();
        });

        player.on('pause', function () {
            // 4. 재생이 멈추면 지속적인 모니터링을 종료합니다.
            pal.StopMonitoring();
        });
        
        player.on('error', function () {
              // 5. 재생 중 플레이어에 문제가 있다면 지속적인 모니터링을 멈추고
              // 안티캡쳐 비활성화합니다.
             pal.StopMonitoring();
             pal.DeactivateAntiCapture();
         });

        player.play().then(function (value) {
            console.log("[PallyCon] Successfully created player instance");
        });
    }
</script>
```

## 각종 이슈 대응

### 다양한 녹화 프로그램

#### PC 녹화 프로그램

녹화 프로그램은 다양하게 있고 새로운 프로그램이 계속해서 출시되고 있습니다. 또한 클라이언트 게임이나 관련 프로그램에서 녹화를 지원하는 경우가 있습니다.

예를 들어 윈도우 기본 XBox 클라이언트, Steam 프로그램, NVIDIA / AMD 드리이버 등을 설치하면 함께 녹화 프로그램이 설치됩니다. 이러한 경우 녹화 프로그램을 종료시키거나 관련된 서비스를 종료해야 합니다.

#### 크롬 확장 프로그램

크롬 확장 프로그램에 녹화를 지원하는 프로그램이 있습니다. 이러한 확장 프로그램은 크롬에 설치되어 있다면 안티캡쳐 프로그램은 이벤트를 발생시킵니다.

사용자가 녹화를 하지 않더라도 스크린 캡쳐 확장 프로그램이 설치되어 있다면 안티캡쳐 프로그램에서 이벤트시키므로 확장 프로그램을 삭제하거나 disable하면 됩니다.

> 새로운 녹화 프로그램을 발견하거나 테스트 중 안티캡쳐 프로그램에 등록되지 않은 녹화 프로그램이 있다면 잉카 팰리컨팀에 제보바랍니다.

### 보안 이슈

#### 난독화

웹 어플리케이션은 코드 노출과 디버깅으로 보안 취약요소가 많습니다. 따라서 개발이 완료되면 코드 난독화로 기본적인 보호가 필수이며, PallyConLib.js API를 호출하는 파일도 난독화를 권장합니다.

> 난독화 사이트 추천: https://obfuscator.io/

***
