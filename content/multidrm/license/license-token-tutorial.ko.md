---
title: 라이선스 토큰 자습서
linktitle: 라이선스 토큰 자습서
summary: 토큰 방식의 DRM 라이선스 연동에 대한 자습서입니다.
toc: true
type: book
date: "2020-05-05T00:00:00+01:00"
lastmod: "2020-06-01T00:00:00Z"
draft: false
menu:
  multidrm:
    parent: 라이선스 발급 연동
    weight: 20
    name: 라이선스 토큰 자습서

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 20
---

## 개요 {#intro}

본 문서는 PallyCon 클라우드 서버에서 멀티 DRM(FPS, Widevine, PlayReady, NCG) 라이선스를 발급하는 방식인 토큰을 생성하는 방법에 대하여, 샘플 코드와 함께 단계 별로 상세 설명합니다. 토큰 방식 라이선스 연동 프로세스와 토큰 생성 규격에 대해서는 [라이선스 토큰 가이드](../license-token)를 참고하시기 바랍니다.

## 유의 사항 {#notice}

본 자습서 문서를 확인하기 전에 아래와 같은 사항을 유의해 주시기 바랍니다.

### 토큰 생성 주체

- 토큰의 생성은 PallyCon 멀티 DRM이 적용될 콘텐츠 서비스의 CMS(Content Management System) 백엔드 서버에서 수행되어야 합니다.
- 클라이언트 측 자바스크립트 또는 모바일 어플리케이션 내부에서 직접 토큰을 생성하는 것은 보안적으로 위험하므로 지양해야 합니다.

### 생성된 토큰의 사용

- 클라이언트 플레이어에서 DRM 콘텐츠를 재생하기 전에, CMS 백엔드는 해당 클라이언트 또는 사용자의 재생 권한 여부를 확인하고  DRM 라이선스 룰(기간 제한 및 보안 옵션)이 적용된 토큰을 생성해 클라이언트에 전달해야 합니다.
- 토큰 데이터를 클라이언트에 전달하는 기능은 고객사에서 직접 구현해야 하며, 주로 재생할 DRM 콘텐츠의 메타 데이터(재생 URL, DRM 연동 데이터, 포스터 이미지 등)에 추가로 전달합니다.

### 샘플 코드와 설정 값

- 본 문서의 샘플 코드는 Node.JS를 기준으로 작성되었으며, 그 외 서버 개발 언어를 사용하는 경우에는 샘플 코드를 참고해 해당 언어에 맞게 변환된 코드를 직접 구현해야 합니다.
- 샘플 코드에 표기된 각종 설정 값들은 참고용 더미 데이터이며, 생성된 토큰 데이터도 실제로는 동작하지 않습니다.
- 동작하는 토큰을 만들기 위해서는 PallyCon 서비스 가입 후에 해당 계정의 설정 값을 대신 입력해야 합니다.

## 1단계 - 각종 설정 값 입력 {#step1}

라이선스 토큰 생성에 필요한 각종 입력 값을 아래와 같이 준비합니다.

```js
const AES_IV = "0123456789abcdef";
const siteInfo = {
  siteId: "TUTO",
  siteKey: "lU5D8s3PWoLls3PWFWkClULlFWk5D8oC",
  accessKey: "LT2FVJDp2Xr018zf4Di6lzvNOv3DKP20"
};

let licenseInfo = {
  drmType: "Widevine",
  contentId: "bigbuckbunny",
  userId: "test-user"
};

let licenseRule = {
  playback_policy: {
    limit: true,
    persistent: false,
    duration: 3600
  }
};

console.log("license rule : " + JSON.stringify(licenseRule));
```

### 입력 값 설명

| **변수 명** | **입력 형식** | **설명** |
| --- | --- | --- |
| AES_IV | 16자리 문자열 | "0123456789abcdef" 고정 값 입력 |
| siteId | 4자리 영문 대문자 | PallyCon 콘솔 사이트에 등록한 서비스 사이트의 ID (콘솔 '사이트 설정' 페이지에서 확인) |
| siteKey | 32자리 Base64 문자열 | 서비스 사이트 연동 과정에 필요한 암호화 키 (콘솔 '사이트 설정' 페이지에서 확인) |
| accessKey | 32자리 Base64 문자열 | 패키져, 라이선스 토큰 등 추가적 연동 보안에 사용되는 키 (콘솔 '사이트 설정' 페이지에서 확인) |
| drmType | "NCG", "Widevine", "PlayReady", "FairPlay" 중 하나 입력 (대소문자 구분) | 클라이언트 환경에 따라 해당되는 DRM의 유형을 입력.  [DRM 플랫폼 지원 페이지](https://pallycon.com/ko/drm-platform) 참고 |
| contentId | 최대 200 자리 영숫자 | 재생할 DRM 콘텐츠의 고유 ID. 해당 콘텐츠의 패키징에 사용된 CID와 동일해야 함. |
| userId | 최대 32자리 영숫자 | 콘텐츠를 재생할 사용자의 고유 ID (서비스 사이트에서 관리하는 유저 ID). 입력할 사용자 ID가 없을 경우, 기본값 ("LICENSETOKEN") 입력 |

> `drmType` 항목의 입력 값은 정해진 규격에 따라 대소문자를 정확히 입력해야 합니다.

LicenseRule JSON 데이터는 해당 콘텐츠 재생 권한에 대한 [상세 규칙](../license-token)을 설정하기 위해 사용됩니다. 본 자습서에서는 위 샘플 코드에 입력된 기본값을 사용합니다.

1단계 코드를 실행하면 아래와 같은 결과가 브라우저 콘솔에 출력됩니다.

```s
license rule : {"playback_policy":{"limit":true,"persistent":false,"duration":3600}}
```

## 2단계 - 라이선스 룰 암호화 {#step2}

1단계에서 준비한 라이선스 룰 JSON 문자열을 AES256 알고리즘으로 암호화하고, 결과를 Base64 문자열로 변환합니다.

```js
// 1단계 코드 이후에 다음을 추가

const crypto = require("crypto");

const cipher = crypto.createCipheriv("aes-256-cbc", siteInfo.siteKey, AES_IV);
let encryptedRule = cipher.update(
  JSON.stringify(licenseRule),
  "utf-8",
  "base64"
);
encryptedRule += cipher.final("base64");

console.log("encrypted rule : " + encryptedRule);
```

AES 암호화에는 다음과 같은 설정이 사용됩니다.

  - 암호화 알고리즘 : AES256 CBC 모드
  - 암호화 키 : 1단계에서 입력한 32바이트 PallyCon 사이트키 값
  - IV (Initial Vector) : 1단계에서 입력한 16바이트 AES_IV 값
  - Padding : pkcs7 (위 코드에서는 기본 값으로 적용되어 입력하지 않음)

> AES 암호화 처리는 각 서버 개발 언어에 따라 해당되는 라이브러리와 API를 사용해야 합니다.

2단계 코드의 실행 결과는 다음과 같습니다.

```s
encrypted rule : bO9DzyQgtgfbSDWmqGgZXWZdWm7M4oZ4X0hC1hS6QNraiwLI0LwmNY+OfOh1L0KmtuH7NF1blUxep9YLWrNfSy4H/6swQW7pbjZnRqRguDQ=
```

## 3단계 - 해시 값 생성 {#step3}

토큰 데이터의 무결성 확인에 사용되는 해시 값을 다음과 같이 생성합니다.

```js
// 2단계 코드 이후에 다음을 추가

const UTCTime = new Date().toISOString().replace(/\.\d{3}/gi, '');

let hashData = {
  siteId: siteInfo.siteId,
  accessKey: siteInfo.accessKey,
  drmType: licenseInfo.drmType,
  userId: licenseInfo.userId,
  cid: licenseInfo.contentId,
  token: encryptedRule,
  timestamp: UTCTime
};

const hashInput =
  hashData.accessKey +
  hashData.drmType +
  hashData.siteId +
  hashData.userId +
  hashData.cid +
  hashData.token +
  hashData.timestamp;

console.log("hash input : " + hashInput);

let hashString = crypto
  .createHash("sha256")
  .update(hashInput)
  .digest("base64");

console.log("hash string : " + hashString);
```

해시를 생성하기 위한 입력 값은 [토큰 규격](../license-token.ko)에 따라 각종 설정 값과 암호화된 라이선스 룰, 그리고 현재 시간 정보를 하나의 문자열로 조합해 만들어집니다.

> 해시 값 생성에는 SHA256 알고리즘이 사용되며, 해시 함수의 결과 값은 문자열로 변환하지 않고 바이너리 데이터 형태 그대로 base64 함수에 입력되어야 합니다.

3단계 코드의 실행 결과는 다음과 같습니다.

```s
hash input : LT2FVJDp2Xr018zf4Di6lzvNOv3DKP20WidevineTUTOtest-userbigbuckbunnybO9DzyQgtgfbSDWmqGgZXWZdWm7M4oZ4X0hC1hS6QNraiwLI0LwmNY+OfOh1L0KmtuH7NF1blUxep9YLWrNfSy4H/6swQW7pbjZnRqRguDQ=2019-07-17T08:57:04Z 

hash string : 0Zef3WHBbzQAR0oGf5KXn25G1TmvB2Bkvzv+7OqIWvQ=
```

## 4단계 - 라이선스 토큰 생성 {#step4}

지금까지 준비한 데이터들을 이용해 최종적으로 라이선스 토큰을 다음과 같이 생성합니다.

```js
// 3단계 코드 이후에 다음을 추가

let tokenData = {
  drm_type: licenseInfo.drmType,
  site_id: siteInfo.siteId,
  user_id: licenseInfo.userId,
  cid: licenseInfo.contentId,
  token: encryptedRule,
  timestamp: UTCTime,
  hash: hashString
};

console.log("token json : " + JSON.stringify(tokenData));

let base64Token = Buffer.from(JSON.stringify(tokenData)).toString("base64");

console.log("base64 encoded token : " + base64Token);
```

우선 토큰 생성 규격에 따라 각종 입력 값과 암호화된 라이선스 룰, 타임스탬프, 그리고 해시 값을 조합해 JSON 데이터를 만듭니다. 해당 JSON 데이터를 Base64 문자열로 변환하면 클라이언트에서 DRM 라이선스 요청에 사용할 토큰이 완성됩니다.

4단계 코드의 실행 결과는 다음과 같습니다.

```s
token json : {"drm_type":"Widevine","site_id":"TUTO","user_id":"test-user","cid":"bigbuckbunny","token":"bO9DzyQgtgfbSDWmqGgZXWZdWm7M4oZ4X0hC1hS6QNraiwLI0LwmNY+OfOh1L0KmtuH7NF1blUxep9YLWrNfSy4H/6swQW7pbjZnRqRguDQ=","timestamp":"2019-07-17T08:57:04Z","hash":"0Zef3WHBbzQAR0oGf5KXn25G1TmvB2Bkvzv+7OqIWvQ="} 

base64 encoded token : eyJkcm1fdHlwZSI6IldpZGV2aW5lIiwic2l0ZV9pZCI6IlRVVE8iLCJ1c2VyX2lkIjoidGVzdC11c2VyIiwiY2lkIjoiYmlnYnVja2J1bm55IiwidG9rZW4iOiJiTzlEenlRZ3RnZmJTRFdtcUdnWlhXWmRXbTdNNG9aNFgwaEMxaFM2UU5yYWl3TEkwTHdtTlkrT2ZPaDFMMEttdHVIN05GMWJsVXhlcDlZTFdyTmZTeTRILzZzd1FXN3BialpuUnFSZ3VEUT0iLCJ0aW1lc3RhbXAiOiIyMDE5LTA3LTE3VDA4OjU3OjA0WiIsImhhc2giOiIwWmVmM1dIQmJ6UUFSMG9HZjVLWG4yNUcxVG12QjJCa3Z6dis3T3FJV3ZRPSJ9
```

***

이상으로 라이선스 토큰 자습서를 마칩니다.

본 자습서에서 생성한 토큰을 이용해 실제 DRM 콘텐츠를 재생해보려면 PallyCon 블로그 사이트에 포스팅된 [Bitmovin HTML5 플레이어 자습서](https://pallycon.com/ko/blog/pallycon-%eb%a9%80%ed%8b%b0-drm%ea%b3%bc-bitmovin-%ed%94%8c%eb%a0%88%ec%9d%b4%ec%96%b4-%ec%97%b0%eb%8f%99-%ed%95%98%ea%b8%b0)을 참고하시기 바랍니다.
