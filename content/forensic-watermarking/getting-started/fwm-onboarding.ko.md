---
title: 포렌식 워터마킹 온보딩 가이드
linktitle: 워터마킹 온보딩
summary: 고객사의 다양한 상황과 요구사항에 맞는 가이드 문서를 쉽게 찾을 수 있도록 포렌식 워터마킹 제품에 대한 온보딩 가이드를 제공합니다.
toc: true
type: book
date: "2020-11-25T00:00:00+01:00"
lastmod: "2020-12-05T00:00:00Z"
draft: false
menu:
  watermarking:
    parent: 시작하기
    weight: 10

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 10
---

고객사의 다양한 상황과 요구사항에 맞는 가이드 문서를 쉽게 찾을 수 있도록 포렌식 워터마킹 제품에 대한 온보딩 가이드를 제공합니다. 아래 순서도의 각 단계별로 링크된 가이드 중에서 원하는 항목을 클릭해 해당 페이지로 이동할 수 있습니다.

## 1단계 - 워터마크 전처리

```mermaid
graph TD;
  start_onboarding(포렌식 워터마킹 온보딩 시작 &nbsp&nbsp) --> preprocessing["1단계 - 워터마크 전처리 &nbsp&nbsp"]

  preprocessing --> preprocessing_type{"전처리 방식 선택 &nbsp"}
  preprocessing_type -->|SaaS 방식 &nbsp|saas_packaging(워터마킹<br>패키징 서비스 &nbsp)
  preprocessing_type -->|On-prem 방식 &nbsp|onprem_preprocessing{인코더 연동 방식 선택  &nbsp}
  onprem_preprocessing -->|인코더 외부 연동  &nbsp|cli_type[CLI 전처리기 방식  &nbsp]
  onprem_preprocessing -->|인코더 내부 연동  &nbsp|library_type[전처리 라이브러리 방식  &nbsp]

  cli_type --> source_encoding(원본 영상 인코딩 &nbsp<br>: mp4 생성 &nbsp)
  source_encoding --> cli_preprocessing(CLI 전처리기 구동 &nbsp<br>: A/B 버전 mp4 생성 &nbsp)
  cli_preprocessing --> packaging_type{DRM 패키징 방식 선택 &nbsp}

  library_type --> library_integration(전처리 라이브러리 &nbsp<br>인코더에 적용 &nbsp)
  library_integration --> ab_encoding(원본 영상 인코딩 &nbsp<br>: A/B 버전 mp4 생성 &nbsp)
  ab_encoding --> packaging_type

  packaging_type -->|PallyCon CLI 패키저 &nbsp|cli_packaging(CLI DRM 패키징 &nbsp<br>: A/B 버전 DASH/HLS 생성 &nbsp)
  cli_packaging --> cdn_upload(패키징 결과물 &nbsp<br>CDN 스토리지에 업로드 &nbsp)

  packaging_type -->|타사 패키저 &nbsp|3rdparty_packaging(DRM 패키징 &nbsp<br>: A/B 버전 DASH/HLS 생성 &nbsp)
  3rdparty_packaging -->packaging_rule(CDN 연동용 &nbsp<br>패키징 규칙 적용 &nbsp)
  packaging_rule --> cdn_upload

  saas_packaging --> storage_registration(S3 스토리지 등록 &nbsp)
  storage_registration --> source_upload(원본 인코딩 후 &nbsp<br>S3 업로드 &nbsp)
  source_upload --> packaging_job(패키징 작업 생성 &nbsp)
  packaging_job --> packaging_done(전처리 및 패키징 완료 &nbsp<br>: A/B 버전 DASH/HLS 생성 &nbsp)
  packaging_done --> cdn_upload

  cdn_upload --> preprocessing_done[1단계 완료 &nbsp]

  click preprocessing "../../preprocessing/"
  click saas_packaging "../../preprocessing/packaging-service"
  click cli_type "../../preprocessing/cli-preprocessor"
  click library_type "../../preprocessing/preprocessor-library"
  click packaging_type "../../../multidrm/packaging"
  click cli_packaging "../../../multidrm/packaging/cli-packager"
  click packaging_rule "../fwm-workflow/#option2-c"
```

## 2단계 - 워터마크 삽입

```mermaid
graph TD;
  watermark_embedding[2단계 - 워터마크 삽입을 위한 CDN 연동 &nbsp] --> cdn_type{연동 CDN 선택 &nbsp}
  cdn_type -->|아카마이 CDN|akamai_account(아카마이 계정 담당자 연락 &nbsp &nbsp<br>- 워터마킹 연동 계약 &nbsp)
  cdn_type --> |아마존 CloudFront|lambda_download(Lambda 모듈 다운로드 &nbsp)

  akamai_account --> pallycon_account(PallyCon 담당자 연락 &nbsp<br>- 연동 인증키 요청 &nbsp)
  pallycon_account --> akamai_config(아카마이 CDN에 연동 구성 &nbsp)
  akamai_config --> session_manager(세션 매니저 API 연동 &nbsp)

  lambda_download --> lambda_config(Lambda 모듈 설정 &nbsp)
  lambda_config --> session_manager
  session_manager --> player_config(플레이어에 세션 URL 적용 &nbsp)
  player_config --> embedding_done[2단계 완료 &nbsp]

  click akamai_config "https://learn.akamai.com/en-us/webhelp/adaptive-media-delivery/adaptive-media-delivery-implementation-guide/GUID-0BA201AE-8CB5-4A0C-AB11-39155F7CD96F.html"
  click lambda_download "../fwm-downloads"
  click lambda_config "../../embedding/cloudfront-embedder"
  click session_manager "../../embedding/session_manager"
```

## 3단계 - 워터마크 검출 테스트

```mermaid
graph TD;
  detecting[3단계 - 검출 테스트 &nbsp] --> playback(세션 URL을 통한 영상 재생 &nbsp<br>여러 재생 세션 중 한 세션을 녹화&nbsp &nbsp)
  playback --> request_detection(녹화된 영상을 PallyCon에 전달 &nbsp<br>워터마크 검출 요청 &nbsp)
  request_detection --> detection_done[검출 결과 확인 &nbsp<br>3단계 완료 &nbsp]
  detection_done --> onboarding_complete(포렌식 워터마킹 온보딩 완료&nbsp &nbsp)

```
