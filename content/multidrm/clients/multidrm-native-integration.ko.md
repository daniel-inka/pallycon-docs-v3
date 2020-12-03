---
title: 멀티DRM 네이티브 클라이언트 연동 가이드
linktitle: 네이티브 클라이언트 연동
summary: 본 문서는 PallyCon 클라이언트 SDK를 사용하지 않고 직접 각 기기의 네이티브 DRM을 사용해 개발할 경우에 PallyCon 멀티DRM 클라우드 서버로부터 라이선스를 발급받기 위한 연동 가이드 문서입니다.
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
lastmod: "2020-09-16T00:00:00Z"
draft: false

menu:
  multidrm:
    parent: 클라이언트 연동
    weight: 40

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 40
---

## 개요

PallyCon 멀티DRM 클라우드 서버는 Google Widevine Modular, Apple FairPlay Streaming, Microsoft PlayReady DRM 라이선스를 발급하는 기능을 제공합니다.
클라이언트에서 멀티DRM 라이선스를 발급받기 위해서는 해당 서비스 사이트와의 연동을 통해 사용자 인증을 해야 하며, 연동 방식은 다음과 같이 두 가지 방식이 있습니다.

1. 콜백 방식
   - 클라이언트에서 라이선스 요청 시, PallyCon 클라우드 서버에서 각 서비스 사이트 별로 등록된 콜백 페이지를 호출해 인증하는 방식입니다.

2. 토큰 방식
   - 서비스 사이트에서 미리 정의된 규격에 따라 라이선스 토큰을 생성해 클라이언트에 전달하고, 클라이언트는 해당 토큰을 이용해 라이선스를 발급받는 방식입니다.
   - [라이선스 토큰 가이드](../../license/license-token) 참조

본 문서는 멀티DRM 클라이언트(HTML5 플레이어, iOS/Android 앱, 스마트TV, 셋탑박스 등)에서 PallyCon 클라이언트 SDK를 사용하지 않고 직접 각 기기의 네이티브 DRM을 사용해 개발할 경우에 PallyCon 멀티DRM 클라우드 서버로부터 라이선스를 발급받기 위한 연동 가이드 문서입니다.

## 멀티DRM 라이선스 발급 과정 {#workflow}

{{< figure library="true" src="Integration_Multi_DRM_Overview_ko.png" >}}

1) 콘텐츠 재생 준비

- 클라이언트(Player)에서 DRM 콘텐츠 재생을 위해 서비스 사이트로부터 DRM 연동을 위한 정보(PallyCon Custom Data 또는 토큰)를 전달받아 DRM 콘텐츠 재생을 시도합니다.

2) 라이선스 요청

- 클라이언트에서 설정한 PallyCon Custom Data 또는 토큰 정보는 네이티브 DRM 모듈이 생성한 License Challenge 정보와 함께 PallyCon 멀티DRM 클라우드 서버로 전송됩니다.

3) 콜백 사용자 인증

- 콜백 방식으로 연동된 경우, PallyCon 멀티DRM 클라우드 서버는 해당 서비스 사이트에 사용자 인증 요청을 합니다. 서비스 사이트의 콜백 페이지에서는 사용자 인증 확인 후 콘텐츠 사용 권한 정보를 회신합니다.
- 토큰 방식 연동에서는 토큰 발급 전에 서비스 사이트에서 사용자 인증이 처리되므로, 본 과정은 생략됩니다.

4) 라이선스 발급

- PallyCon 멀티DRM 클라우드 서버는 콜백 페이지 응답이나 토큰 요청 정보에 설정된 권한 정보를 이용해 클라이언트의 DRM 종류에 따라 라이선스를 발급합니다.

## Widevine Modular / PlayReady DRM 라이선스 연동 {#widevine-playready}

### Request

- Request URI : <https://license.pallycon.com/ri/licenseManager.do>
- Request Method : POST
- Custom HTTP Header, URL Parameter 두 가지 방식으로 custom data 전송이 가능하며, PlayReady의 경우에는 PlayReady Challenge의 customdata 필드도 이용 가능합니다 ( only license token ).

|Name |Value    |
|---|---|
|pallycon-customdata-v2| 사용자 인증 방식에 따라 custom data 또는 token 값을 사용합니다.<br> 1) base64 encoding ([PallyCon Custom Data v2 Format](#pallycon-custom-data-v2) 참조) <br> 2) 라이선스 토큰 문자열 ([License Token Guide](../../license/license-token) 참조) |

> POST body : Native DRM Client 모듈에서 생성한 License Challenge Data

### Response

- 라이선스 정상 발급

|Name |Value    |
|---|---|
|status code |200 OK |
|response body |native DRM license data<br> - Widevine Modular: binary data<br> - PlayReady DRM : base64 encoding string |

- 라이선스 발급 오류

|Name |Value    |
|---|---|
|status code |200 OK |
|response body |JSON Data <br> {"eccorCode": "error code","message": "error message"} |

## FairPlay Streaming DRM 라이선스 연동 {#fairplay}

FPS License 발급을 연동하기 위해서는 콘텐츠 서비스 측에서 Apple로부터 아래 정보를 발급 받아 PallyCon 멀티DRM 서버에 설정해 주어야 합니다.

1. Apple 개발자 계정 생성 ( <https://developer.apple.com/support/enrollment/> )
1. Apple에 `FPS Deployment Package` 신청 ( <https://developer.apple.com/streaming/fps/> )
1. `Deployment Package`를 이용해 생성한 다음 4가지 정보를 PallyCon 콘솔 사이트의 `연동 설정`에 등록

```s
- FPS Certificate 파일 (.der or .cer)
- Private key 파일 (.pem)
- Private key 암호 문자열
- Application secret key (ASK) 문자열
```

> 위 등록 과정에 대한 상세한 설명은 [FairPlay 인증서 등록 자습서]({{<ref "fps-cert-tutorial.ko.md">}})를 참고하시기 바랍니다.

### 1. FPS Certification File 다운로드 연동 규격

FPS 클라이언트에서는 FPS 연동을 하기 위해 사전에 등록된 FPS Certificate(.cer) 파일을 PallyCon Server로부터 아래 연동 규격을 통해 다운 받아야 합니다.

#### Request

- Request URI : <https://license.pallycon.com/ri/fpsKeyManager.do?siteId='site-id'>
- Request Method : GET

|Parameter |Value |
|---|---|
|siteid |PallyCon Service에서 발급 받은 서비스 사이트 ID (4 byte) |

#### Response

- 정상

| Name          | Value       |
|---|---|
| status code   | 200 OK      |
| response body | base64 encoding (fps certificate data) |

- 오류

| Name        | Value  |
|---|---|
| status code | 200 OK |
| response body |JSON Data {"errorCode": "error code", "message": "error message"}        |

> FPS Cert 다운로드 URL은 고객사의 편의를 위해 제공되는 것으로, 필요에 따라 위 규격 대신 Cert 파일(.cer 또는 .der)을 고객사에서 자체 서버에 호스팅하고 해당 URL을 사용할 수도 있습니다.

### 2. FPS 라이선스 연동 규격

#### Request

- Request URI : <https://license.pallycon.com/ri/licenseManager.do>
- Request Method : POST
- Custom HTTP Header, URL Parameter 두 가지 방식으로 custom data 전송 

|Name |Value |
|---|---|
|pallycon-customdata-v2 | 사용자 인증 방식에 따라 custom data 또는 token 값을 사용합니다.<br> 1) base64 encoding ([PallyCon Custom Data v2 Format](#pallycon-custom-data-v2) 참조) <br> 2) 라이선스 토큰 문자열 ([License Token API](../../license/license-token) 참조) |

- POST body : spc='base64 encoding(spc data)'

|Parameter |Value |
|---|---|
|spc |base64 encoding (native DRM client에서 생성한 SPC Data) |

#### Response

- 라이선스 정상 발급

|Name |Value |
|---|---|
|status code | 200 OK |
|response body |base64 encoding (CKC data from FPS KSM) |

- 라이선스 발급 오류

|Name |Value |
|---|---|
|response body | JSON Data {"errorCode": "error code", "message": "error message"}

## PallyCon Custom Data v2 Format {#pallycon-custom-data-v2}

### JSON Format

```json
{
    "drm_type": "<multi-drm type>",
    "site_id": "<site id>",
    "data": "<base64 encoding(aes256 encryption(custom data json string))>"
}
```

| Name     | Value                        |
| ---      | ---                          |
| drm_type | DRM 종류 <br> ("NCG", "Widevine", "PlayReady", "FairPlay") |
|SiteID | PallyCon 클라우드 서비스에서 발급받은 서비스 사이트 ID (4byte) |
|JSON Data | 사용자 인증 관련 정보 (custom data json string) <br>AES256 암호화 후 base64 encoding |

### Custom Data JSON Format

```json
{
    "user_id": "user id",
    "cid": "content id",
    "oid": "optional id"
}
```

|Name |Value |
|---|---|
|user_id |서비스 사이트의 사용자 ID |
|cid | 콘텐츠 고유 ID |
|oid | 부가 연동 정보. 주문 정보 등 추가적으로 서비스 사이트에 전달되기를 원하는 정보를 입력합니다. (선택 사항) |

### 예제

```json
PallyCon Customdata v2 string
{
     "drm_type":"Widevine",
     "site_id":"DEMO",
     "data":"4wvWFelCQ4ynPUaCSWeb1fcNuFUFqFhEH0jivn11OStvVPP/05wUkNhdKCGchNz1"
}

custom data json string
{"user_id":"test", "cid":"test cid"}

"pallycon-customdata-v2" : "eyJkcm1fdHlwZSI6IldpZGV2aW5lIiwgInNpdGVfaWQiOiJERU1PIiwgImRhdGEiOiI0d3ZXRmVs
Q1E0eW5QVWFDU1dlYjFmY051RlVGcUZoRUgwaml2bjExT1N0dlZQUC8wNXdVa05oZEtDR2NoTnox
In0NCg=="
```

## AES256 암호화 {#aes256}

aes256 Encryption/Decryption 처리는 PallyCon Cloud 서비스 사이트 생성 시 발급 되는 사이트 키 값을 이용하여 다음과 같이 처리합니다. ( PallyCon 콘솔 사이트에서 확인 )

```s
AES256 Encryption
- mode : CBC
- key : 32 byte (PallyCon 콘솔 사이트에서 발급되는 사이트 키)
- iv : 16 byte (0123456789abcdef)
- padding : pkcs7
```

## 예제 {#examples}

```s
PallyCon Customdata v2 string
{
     "drm_type":"Widevine",
     "site_id":"DEMO",
     "data":"4wvWFelCQ4ynPUaCSWeb1fcNuFUFqFhEH0jivn11OStvVPP/05wUkNhdKCGchNz1"
}
custom data json string
{"user_id":"test", "cid":"test cid"}

1) Using Custom HTTP Header

setRequestHeader("pallycon-customdata-v2", "eyJkcm1fdHlwZSI6IldpZGV2aW5lIiwgInNpdGVfaWQiOiJERU1PIiwgImRhdGEiOiI0d3ZXRmVs
Q1E0eW5QVWFDU1dlYjFmY051RlVGcUZoRUgwaml2bjExT1N0dlZQUC8wNXdVa05oZEtDR2NoTnox
In0NCg==");

2) Using URL parameter

license url = "https://license.pallycon.com/ri/licenseManager.do?pallycon-customdata-v2=eyJkcm1fdHlwZSI6IldpZGV2aW5lIiwgInNpdGVfaWQiOiJERU1PIiwgImRhdGEiOiI0d3ZXRmVs
Q1E0eW5QVWFDU1dlYjFmY051RlVGcUZoRUgwaml2bjExT1N0dlZQUC8wNXdVa05oZEtDR2NoTnox
In0NCg=="

3 Using PlayReady customdata ( only license token string )

<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns:soap="https://schemas.xmlsoap.org/soap/envelope/">
<soap:Body>AcquireLicense
    xmlns="http://schemas.microsoft.com/DRM/2007/03/protocols">
<challenge>
...

<ClientInfo> ... </ClientInfo>
<CustomData>eyJ0b2tlbiI6IlRhXC9SdkJBZ1U4ZDdwSExqeXVyK2MxTU05N1pXUm1pSmdXYzJcL1U5MnBPbWJhOXNtXC9ybHdmc0lNM2ZValhzSTUiLCJzaXRlX2lkIjoiREVNTyIsImRybV90eXBlIjoiUGxheVJlYWR5IiwiY2lkIjoidHJheSJ9</CustomData>
```

## 구버전 PallyCon Custom Data Format {#custom-data-v1}

- 2가지 방식 지원: Custom HTTP Header, URL Parameter

```s
DRMType(1) + SiteID(4) + base64 E(aes256 E(custom data json string))
```

| Name     | Value                        |
| ---      | ---                          |
| DRMType | Native DRM 종류 <br> ("W": Widevine, "P": PlayReady, "F": FairPlay) |
|SiteID | PallyCon 클라우드 서비스에서 발급받은 서비스 사이트 ID (4byte) |
|JSON Data | 사용자 인증 관련 정보 (custom data json string) <br>AES256 암호화 후 base64 인코딩 |

### custom data json format

```json
{
    "user_id": "<user id>",
    "cid": "<content id>",
    "oid": "<optional id>"
}
```

|Name |Value |
|---|---|
|user_id |서비스 사이트의 사용자 ID |
|cid | 콘텐츠 고유 ID |
|oid | 부가 연동 정보. 주문 정보 등 추가적으로 서비스 사이트에 전달되기를 원하는 정보를 입력합니다. (선택 사항) |

### 예제

```s
DRMType : Widevine Modular,
SiteID : DEMO,
custom data json string : {"user_id":"test","cid":"test_cid"}

"pallycon-customdata" :
"WDEMO4wvWFelCQ4ynPUaCSWeb1fcNuFUFqFhEH0jivn11OStvVPP/05wUkNhdKCGchNz1"

1) Using Custom HTTP Header

setRequestHeader("pallycon-customdata", "WDEMO4wvWFelCQ4ynPUaCSWeb1fcNuFUFqFhEH0jivn11OStvVPP/05wUkNhdKCGchNz1");

2) Using URL parameter

license url = "https://license.pallycon.com/ri/licenseManager.do?pallycon-customdata=WDEMO4wvWFelCQ4ynPUaCSWeb1fcNuFUFqFhEH0jivn11OStvVPP/05wUkNhdKCGchNz1"

```
