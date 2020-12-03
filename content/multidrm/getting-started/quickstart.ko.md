---
title: 멀티DRM 퀵스타트
linktitle: 멀티DRM 퀵스타트
summary: 본 문서는 PallyCon 멀티DRM 서비스의 빠른 체험을 위해 콘텐츠 패키징과 DRM 라이선스 연동 및 재생 과정을 안내합니다.
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
lastmod: "2020-09-16T00:00:00Z"
draft: false
featured: true

menu:
  multidrm:
    parent: 멀티DRM 시작하기
    weight: 50

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 50
---

## 개요

본 문서는 PallyCon 멀티DRM 서비스의 빠른 체험을 위해 **콘텐츠 패키징과 DRM 라이선스 연동 및 재생 과정을 안내**합니다.

퀵 스타트는 아래와 같은 세 가지 단계로 구성되어 있습니다. 가장 간단하고 빠르게 따라할 수 있도록 PallyCon CLI 패키져와 라이선스 토큰 연동으로 DRM 콘텐츠를 HTML5 플레이어에서 재생하는 과정을 설명합니다.

 - 1단계: PallyCon 트라이얼 계정 가입 및 테스트용 웹서버 준비
 - 2단계: PallyCon CLI 패키져를 이용한 DRM 콘텐츠 패키징
 - 3단계: DevConsole 사이트를 통한 라이선스 토큰 생성 및 DRM 콘텐츠 재생

> PallyCon CLI 패키져와 라이선스 토큰 연동에 대한 상세 설명은 다음 링크를 참고하시기 바랍니다.
>
> * PallyCon CLI 패키져 가이드: https://pallycon.com/docs/ko/multidrm/packaging/cli-packager/
> * 라이선스 토큰 연동 가이드: https://pallycon.com/docs/ko/multidrm/license/license-token/

## 1단계 - PallyCon 트라이얼 계정 가입 {#step1}

퀵 스타트 진행을 위해서는 PallyCon 서비스 사이트에 트라이얼 계정이 있어야 합니다. 아직 PallyCon 서비스에 가입하지 않은 경우, 다음과 같은 과정을 통해 계정을 생성합니다.

### 1-1. PallyCon 멀티 DRM 트라이얼 가입

PallyCon 콘솔 사이트 [로그인 페이지](https://login.pallycon.com)에 접속해 `회원 가입` 버튼을 클릭합니다. 아래와 같이 가입 양식을 입력하고 `회원가입` 버튼을 클릭합니다.

{{< figure library="true" numbered="true" src="drm-quickstart-pallycon-signup-ko.png" title="PallyCon 회원가입" >}}

> PallyCon 콘솔 : PallyCon 서비스 가입 및 연동 관련 각종 설정과 조회를 할 수 있는 사이트입니다. 사이트 가입 후 이메일 인증을 해야 하며, 신규 가입한 계정의 경우 자동으로 트라이얼 플랜이 적용됩니다.
> {{% button href="https://console.pallycon.com/" %}}PallyCon 콘솔 바로 가기{{% /button %}}

### 1-2. PallyCon 계정 이메일 인증 및 트라이얼 사이트 생성

가입 시 입력한 이메일로 가입 환영 메일이 발송됩니다. 해당 메일의 `인증하기` 링크를 클릭해 이메일 인증을 수행합니다. 이메일 인증 후에는 트라이얼을 위한 사이트 정보가 자동으로 생성됩니다.

### 1-3. 트라이얼 사이트 정보 확인

PallyCon 콘솔 사이트에 로그인 하여 `사이트 설정` 메뉴의 `연동 설정` 화면으로 이동합니다. 아래와 같은 화면에 표시되는 `사이트ID`, `사이트 키`와 `액세스 키` 값을 메모장과 같은 곳에 별도로 복사해 둡니다. 이 값들은 이후 단계에서 DRM 연동에 사용됩니다.

{{< figure library="true" numbered="true" src="drm-quickstart-console-siteinfo-ko.png" title="PallyCon 콘솔 사이트 정보" >}}

### 1-4. 테스트용 웹서버 준비

DRM 콘텐츠의 재생을 테스트하기 위해서는 해당 콘텐츠 파일들을 업로드할 웹서버가 필요합니다. 임의의 위치에서 HTTPS URL로 콘텐츠 파일에 접근할 수 있도록, 외부 인터넷에서의 접근이 허용되고 SSL 인증서가 설치된 웹서버를 준비합니다.

> 별도의 테스트용 웹서버 준비가 어려운 경우 `AWS`, `Azure` 등의 클라우드 인프라 서비스를 이용하거나 [Netlify](https://www.netlify.com/), [Heroku](https://www.heroku.com/) 등 클라우드 플랫폼에서 제공하는 무료 HTTPS 호스팅을 활용할 수 있습니다.

## 2단계 - PallyCon CLI 패키져를 이용한 DRM 콘텐츠 패키징

플레이어에서 DRM 콘텐츠 재생을 테스트하기 위해서는 우선 원본 영상에 DRM을 적용하는 패키징 과정이 필요합니다. 다음과 같은 과정을 통해 DRM 콘텐츠 패키징을 수행합니다.

### 2-1. CLI 패키져 다운로드 및 설치

본 사이트의 [패키져 / 샘플 다운로드 페이지](../downloads/)에서 `PallyCon Packager 다운로드` 버튼을 클릭해 CLI 패키저를 다운로드 합니다.

콘텐츠 패키징을 수행할 테스트 PC 또는 서버에 임의의 폴더를 생성하고, 다운받은 패키져 압축파일 내 `bin` 폴더의 OS별 실행 파일을 복사합니다. (예: ~/pallycon-test/PallyConPackager )

> PallyCon CLI 패키져는 64비트 기반의 윈도우즈와 리눅스(Ubuntu, CentOS) CLI(Command Line Interface) 환경을 지원합니다. 리눅스 환경의 경우, gcc와 g++ 라이브러리 6.0 버전 이상이 설치되어 있어야 합니다.

### 2-2. 원본 MP4 영상을 MPEG-DASH로 패키징

DRM 패키징과 재생 테스트에 사용할 MP4 (H264 AVC) 동영상 파일을 패키져 실행 파일이 저장된 폴더에 복사합니다. 아래 링크된 PallyCon 소개 영상을 다운로드 받아 사용하셔도 됩니다.

> PallyCon 소개 영상 : https://pallycon.com/docs/files/pallycon-intro.mp4
> 
> PallyCon CLI 패키져는 멀티 DRM용 `DASH-CENC`(Widevine, PlayReady), `HLS-AES`(FairPlay) 패키징과 NCG(Netsync Content Guard) DRM을 위한 NCG, NCG-HLS 패키징 기능을 지원합니다. 본 자습서에서는 간략한 테스트를 위해 DASH-CENC 패키징만 진행합니다.

아래와 같은 명령을 실행해 DASH 패키징을 수행합니다. 패키징 명령의 각종 입력값들에 대한 설명은 아래 표를 참고하시기 바랍니다.

```s
./PallyConPackager --site_id --access_key --content_id --dash -i -o
```

|<div style="width:90px">파라미터</div> | 입력 값 | 규격 | 예시 |
| :-------- | :------- | :---- | :---- |
| --site_id | 1단계에서 PallyCon 콘솔 사이트의 `연동 설정` 화면에 표시된 트라이얼 사이트 ID | 네자리 영대문자 | ABCD |
| --access_key | PallyCon 콘솔 사이트의 `연동 설정` 화면에 표시된 액세스 키 | 32자리 문자열 | H24FS8wdo3C7CWrSjcIhPYWpYaFu6Lh3 |
| --content_id | DRM 패키징과 라이선스 발급 시 각 콘텐츠를 구분할 콘텐츠의 고유 아이디. 해당 아이디를 기준으로 DRM 암호화 키와 라이선스가 관리되며, 일반적으로 CMS (Content Management System) 에서 관리하는 콘텐츠 아이디가 입력되도록 연동 구현함. | 최대 200 자리 영문/숫자 문자열 | pallycon-intro |
|-i | 패키징할 원본 MP4 파일명. 패키져 실행 파일과 다른 경로에 있는 경우, 해당 경로를 포함한 파일명 입력 | 파일 경로 규격 | ~/pallycon-test/input/pallycon-intro.mp4 |
| -o | 패키징된 결과물이 저장될 경로 | 폴더 경로 규격 | ~/pallycon-test/output/pallycon-intro/ |

> 위 예시된 입력값에 따라 명령을 구성하면 아래와 같습니다. 예시로 표기된 사이트ID와 액세스 키는 실제로는 동작하지 않으며 1단계에서 가입한 계정에 해당되는 값으로 교체하셔야 합니다.
> ```s 
> ./PallyConPackager --site_id ABCD --access_key H24FS8wdo3C7CWrSjcIhPYWpYaFu6Lh3 --content_id pallycon-intro --dash -i ~/pallycon-test/input/pallycon-intro.mp4 -o ~/pallycon-test/output/pallycon-intro/
> ```

패키징이 성공적으로 완료되면 PallyCon 콘솔 사이트의 `DRM 패키징 이력` 페이지에 해당 이력이 아래와 같이 표시됩니다.

{{< figure library="true" numbered="true" src="drm-quickstart-packaging-result-ko.png" title="패키징 결과" >}}

### 2-3. 테스트용 웹 서버에 패키징된 DASH 콘텐츠 업로드

패키징이 완료되면 아웃풋 폴더 내 `dash` 폴더에 mpd 매니페스트 파일과 오디오/비디오 트랙으로 구성된 `MPEG-DASH` 형태의 결과물이 생성됩니다. 3단계의 DRM 콘텐츠 재생 테스트를 위해서는 해당 폴더를 테스트용 웹 서버에 업로드해야 합니다. CDN과 연결된 스토리지 또는 일반적인 웹 서버에 DASH 콘텐츠를 업로드 합니다.

> 크롬과 파이어폭스 등의 브라우저는 보안을 위해 미디어 스트리밍과 관련된 모든 URL에 필수적으로 HTTPS를 적용하도록 강제하고 있습니다. 3단계 재생 테스트를 위해서는 테스트용 웹 서버에도 SSL 인증서를 적용해 HTTPS URL을 사용해야 합니다.

## 3단계 - 라이선스 토큰 생성 및 DRM 콘텐츠 재생

[PallyCon DevConsole 페이지](https://sample.pallycon.com/dev/devconsole/encDec.do?lang=ko)는 DRM 연동 개발에 도움이 되는 각종 테스트 기능을 제공합니다. 아래와 같은 과정을 통해 DRM 라이선스 발급에 필요한 토큰을 생성하고 2단계에서 패키징한 DRM 콘텐츠를 재생해봅니다.

### 3-1. DRM 라이선스 토큰 생성

패키징된 콘텐츠를 재생하기 위해서는 DRM 라이선스 연동을 통해 플레이어에 해당 콘텐츠의 라이선스가 발급되어야 합니다. PallyCon 멀티DRM은 콜백 방식과 토큰 방식의 라이선스 연동을 지원하며, 본 자습서에서는 토큰 방식의 라이선스 연동으로 진행합니다.

> 라이선스 발급 방식에 대한 상세 설명은 아래 링크된 온라인 가이드를 참고하시기 바랍니다.
> 
> * DRM 라이선스 발급 연동 가이드 : https://pallycon.com/docs/ko/multidrm/license/

라이선스 요청에 사용되는 토큰은 정해진 규격에 따라 PallyCon 멀티DRM이 적용될 동영상 서비스의 서버 사이드(백엔드)에서 생성되어 클라이언트의 플레이어 코드에 적용되어야 합니다.

본 자습서에서는 빠른 테스트 진행을 위해 PallyCon 사이트의 `개발자 콘솔` 페이지에서 제공되는 `토큰 생성` 기능을 이용합니다. 아래 링크의 개발자 콘솔 페이지로 접속하여 각 항목들을 입력합니다.

- 토큰 생성 페이지 : https://sample.pallycon.com/dev/devconsole/customData.do?lang=ko#create-token

{{< figure library="true" numbered="true" src="drm-quickstart-devconsole-token-ko.png" title="데브콘솔 토큰 생성 페이지" >}}

|<div style="width:90px">항목</div>| 입력 값 | 규격 | 예시 |
|-------|-------------------|----|----|
| SITE ID | 1단계에서 PallyCon 콘솔 사이트의 `연동 설정` 화면에 표시된 트라이얼 사이트 ID | 4자리 영대문자 | ABCD |
| SITE Key | PallyCon 콘솔 사이트의 `연동 설정` 화면에 표시된 사이트 키 | 32자리 문자열 | 3H24FS8wdo3C7CWrSjcIhPYWpYaFu6Lh |
| ACCESS Key | PallyCon 콘솔 사이트의 `연동 설정` 화면에 표시된 액세스 키 | 32자리 문자열 | gktt7xArRiSrWXEvrommuBdUD6Ktk3cP |
| DRM Type | 재생할 클라이언트(브라우저)에서 지원되는 DRM을 입력 (대소문자 구분) | Widevine / PlayReady / FairPlay | 크롬 / 파이어폭스: Widevine, IE11 / 엣지: PlayReady |
| CID | 2단계에서 CLI 패키져에 입력한 콘텐츠 ID | 최대 200자 영문/숫자 문자열 | pallycon-intro |
| USER ID | 콘텐츠 서비스 사용자의 권한 확인에 사용되는 고유한 사용자 ID. 토큰 방식의 연동에서는 콘텐츠 서비스 백엔드와 클라이언트 간에 직접 권한 확인이 완료된 후 토큰을 생성해야 함. | 최대 200자 영문/숫자 문자열 | test-user (임의로 입력) |
| Time stamp | 토큰의 유효성 발효 시간. 일반적으로 토큰 생성 시점으로 설정하며, 해당 시점 이후 기본 600초 동안 토큰이 유효함.(PallyCon 콘솔 사이트에서 설정 가능) | yyyy-mm-ddThh:mm:ssZ (GMT 시간대 기준) | 2019-04-03T08:30:00Z |
| Token Rule | 라이선스 발급 시 적용될 각종 DRM 관련 룰 데이터 (상세 내용은 토큰 가이드 참조) | JSON 문자열 | {} |

위와 같이 토큰 생성에 필요한 값들을 입력하고 `REQUEST TOKEN` 버튼을 클릭하면 페이지 하단에 토큰 문자열이 생성됩니다. 해당 문자열을 복사해 다음 단계를 위해 별도로 저장해 둡니다.

> `DRM Type` 항목은 대소문자를 구분해 정확히 입력해야 하며, `Time stamp` 항목을 입력하지 않으면 현재 시간을 기준으로 토큰이 생성됩니다.

### 3-2. HTML5 플레이어를 통한 DRM 콘텐츠 재생

재생 테스트를 위해 DevConsole의 [HTML5 재생 테스트 페이지](https://sample.pallycon.com/dev/devconsole/player.do?lang=ko#html5-player)로 이동합니다.

해당 페이지의 입력란에 아래와 같이 `DASH URL`과 `pallycon-customdata-v2` 항목을 입력합니다.

- DASH URL: 2단계에서 테스트용 웹 서버에 업로드한 DASH 콘텐츠의 매니페스트 파일(.mpd) URL을 입력
- pallycon-customdata-v2: 3-1 단계에서 생성된 라이선스 토큰 문자열을 입력

{{< figure library="true" numbered="true" src="drm-quickstart-playback-test-ko.png" title="데브콘솔 재생 테스트 페이지" >}}

> 위 캡쳐 화면의 입력값들은 예시를 위한 데모 콘텐츠에 해당하는 값으로, 실제 퀵 스타트 진행 시에는 이전 단계에서 직접 생성한 값들을 입력해야 합니다.

위 항목들이 정상적으로 입력된 경우, `PLAY` 버튼을 클릭하면 하단에 DRM 콘텐츠 영상이 재생됩니다.

## 재생 시 각종 오류 처리

3단계에서 DRM 콘텐츠가 재생되지 않고 오류가 발생한 경우, 아래와 같은 사항들을 확인합니다.

### 테스트용 웹 서버의 CORS 설정

DRM 콘텐츠와 웹 플레이어의 URL 도메인이 다를 경우, 콘텐츠가 업로드된 서버에서 크로스 도메인 접근이 허용되어야 합니다. 해당 설정이 되어있지 않을 때 발생하는 `CORS`(Cross Origin Request Sharing) 오류에 대한 자세한 설명과 처리 방법은 다음 링크를 참고하시기 바랍니다.

- HTTP 접근 제어 (CORS): https://developer.mozilla.org/ko/docs/Web/HTTP/Access_control_CORS

### 라이선스 토큰의 DRM Type 설정

재생 테스트를 수행하는 웹 브라우저가 3-1 단계에서 설정한 라이선스 토큰의 DRM Type과 일치하는지 확인합니다.

크롬/파이어폭스 브라우저를 이용하는 경우에는 `Widevine`, IE11/엣지 브라우저의 경우는 `PlayReady`로 DRM Type 값이 입력되어야 합니다.

### 라이선스 토큰의 만료 여부

유효 기간이 지난 토큰으로는 DRM 라이선스를 발급받을 수 없습니다. 토큰 유효기간은 기본적으로 생성 시점으로부터 10분(600초)이며, 이 값은 PallyCon 콘솔 사이트의 `사이트 설정 > 연동 설정` 화면에서 조절할 수 있습니다.

> 주의 사항 - 토큰의 유효 기간은 DRM 라이선스의 유효기간과는 다른 개념입니다.
> 
> - 토큰 유효 기간: 해당 토큰으로 DRM 라이선스를 발급 받을 수 있는 기간
> - 라이선스 유효 기간: 라이선스 발급 이후에 해당 라이선스로 DRM 콘텐츠를 재생할 수 있는 기간

## 다음 단계

축하합니다! 여기까지 문제없이 진행하셨다면 PallyCon 멀티DRM 서비스를 적용하는데 필요한 전 과정을 체험하신 것입니다.

다음 단계로의 진행을 위해서는 아래 가이드들을 참고하시기 바랍니다.

- [콘텐츠 패키징 가이드](../../packaging): 다양한 콘텐츠 패키징 방법들을 안내합니다.
- [라이선스 연동 가이드](../../license): DRM 라이선스 발급을 위한 연동 방식들을 설명합니다.
- [클라이언트 연동 가이드](../../clients): 다양한 클라이언트 기기들에서 DRM 콘텐츠를 재생하는 방법을 안내합니다.
