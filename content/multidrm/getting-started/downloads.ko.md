---
title: 패키져 / 샘플 다운로드
linktitle: 패키져 / 샘플 다운로드
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

PallyCon 서비스를 이용하는데 필요한 `콘텐츠 패키저`와 `연동 샘플 소스`를 다운로드 받을 수 있습니다.

## PallyCon CLI 패키져

- PallyCon CLI 패키져는 원본 콘텐츠에 멀티 DRM을 적용하여 암호화된 스트리밍 콘텐츠로 패키징하는 CLI(Command Line Interface) 기반 툴입니다.
- PallyCon CLI 패키져에 대한 상세 내용은 [해당 가이드]({{ %ref "cli-packager.ko.md"%}})를 참고하시기 바랍니다.

{{% button href="/docs/files/PallyCon-Packager-Cloud-v3.6.2.zip" icon="fas fa-download" %}}PallyCon CLI 패키져 다운로드{{% /button %}}

## 토큰 방식 DRM 연동 샘플

- 고객의 서비스 사이트와 PallyCon 멀티DRM 서비스 간의 라이선스 연동을 위한 **토큰 생성 방법 예제 소스**가 제공됩니다.
- 토큰 방식 라이선스 연동에 대한 상세 규격은 [라이선스 토큰 가이드]({{ %ref "license-token.ko.md"%}})를 참고하시기 바랍니다.
 
 {{% button href="/docs/files/drm-token-sample-java-20200710.zip" icon="fas fa-download" %}}토큰 생성 Java 샘플 다운로드{{% /button %}}

 {{% button href="/docs/files/drm-token-sample-python-20200713.zip" icon="fas fa-download" %}}토큰 생성 Python 샘플 다운로드{{% /button %}}

 {{% button href="/docs/files/drm-token-sample-php-20200526.zip" icon="fas fa-download" %}}토큰 생성 PHP 샘플 다운로드{{% /button %}}

 {{% button href="/docs/files/drm-token-sample-asp-20200526.zip" icon="fas fa-download" %}}토큰 생성 ASP 샘플 다운로드{{% /button %}}

## 토큰 Proxy 방식 DRM 연동 샘플

- 토큰 방식의 라이선스 연동을 고객사에서 구축한 프록시 서버를 통해 처리하고자 하는 경우, 아래의 토큰 Proxy 샘플 코드를 참고해 구현할 수 있습니다.

 {{% button href="/docs/files/TokenProxySample-Java-v2.0.zip" icon="fas fa-download" %}}토큰 Proxy Java 버전 샘플 다운로드{{% /button %}}

## 콜백 방식 DRM 연동 샘플

- 고객의 서비스 사이트와 PallyCon 멀티DRM 서비스 간의 **콜백 방식 연동 방법 예제가 포함된 웹페이지 소스**로, 주요 서버 언어인 **Python, JSP, ASP, PHP, Node.js** 예제가 제공됩니다.
- 콜백 방식 라이선스 연동에 대한 상세 내용은 [해당 가이드]({{ %ref "license-callback.ko.md"%}})를 참고하시기 바랍니다.
 
|샘플 타입 |GitHub 링크 |
|---|---|
|Python| <https://github.com/inka-pallycon/sdk-quickstart-python> |
|JSP| <https://github.com/inka-pallycon/sdk-quickstart-jsp> |
|ASP| <https://github.com/inka-pallycon/sdk-quickstart-asp> |
|PHP| <https://github.com/inka-pallycon/sdk-quickstart-php> |

## 포렌식 워터마킹 API 요청 샘플

- PallyCon 포렌식 워터마킹 서비스의 [패키징 작업 관련 API]({{ %ref "packaging-api.ko.md"%}})와 [Akamai CDN]({{ %ref "akamai-session-manager.ko.md"%}}), [Amazon CloudFront CDN]({{ %ref "cloudfront-session-manager.ko.md"%}}) 연동을 위한 세션 매니저 API에 공통적으로 사용되는 요청 규격을 구현한 샘플 코드입니다.
- API 별 요청 데이터 JSON을 생성하는 규격은 위에 링크된 각각의 API 가이드 문서를 참고하시기 바랍니다.

|샘플 타입 |GitHub 링크 |
|---|---|
|Python| <https://github.com/inka-pallycon/pallycon-wm-api-request-sample-python> |
|Java| <https://github.com/inka-pallycon/pallycon-wm-api-request-sample-java> |

## 포렌식 워터마킹 CDN 연동 샘플

- PallyCon 포렌식 워터마킹 서비스를 이용하기 위해서는 실시간 워터마크 조합을 위한 CDN 연동이 필요합니다.

- 아래 링크에서 Amazon CloudFront CDN 연동을 위한 Lambda@Edge 샘플 소스를 다운받을 수 있습니다.

 {{% button href="/docs/files/pallycon-watermark-lambda-v2.0.1.zip" icon="fas fa-download" %}}CloudFront Lambda 샘플 다운로드{{% /button %}}

> CloudFront 이외의 CDN 연동이 필요한 경우, 별도로 문의하시기 바랍니다.

<a href="https://www.pallycon.com/contact/?lang=ko" target="_blank" class="btn btn-default">문의하기</a>

***
