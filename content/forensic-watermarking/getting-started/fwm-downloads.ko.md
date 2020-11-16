---
title: 샘플 다운로드
linktitle: 샘플 다운로드
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

PallyCon 포렌식 워터마킹 서비스를 이용하는데 필요한 샘플 코드를 다운로드 받을 수 있습니다.

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

 {{% button href="/docs/files/pallycon-watermark-lambda-v2.2.0.zip" icon="fas fa-download" %}}CloudFront Lambda 샘플 다운로드{{% /button %}}

> 아카마이 CDN은 Lambda@Edge와 같은 연동 모듈이 불필요합니다. CloudFront와 아카마이 이외의 CDN 연동이 필요한 경우, 별도로 문의하시기 바랍니다.

<a href="https://www.pallycon.com/contact/?lang=ko" target="_blank" class="btn btn-default">문의하기</a>
