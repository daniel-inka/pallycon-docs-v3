---
# Course title, summary, and position.
linktitle: 전처리 라이브러리 가이드
summary: PallyCon 워터마킹 전처리 라이브러리는 인코딩/트랜스코딩 솔루션에 포팅될 수 있도록 C++ 라이브러리 형태로 구현된 전처리기입니다.
weight: 20

# Page metadata.
title: 전처리 라이브러리 가이드
date: "2018-09-09T00:00:00Z"
lastmod: "2020-10-18T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: true  # Show table of contents? true/false
type: book  # Do not modify.

# Add menu entry to sidebar.
# - name: Declare this menu item as a parent with ID `name`.
# - weight: Position of link in menu.
menu:
  watermarking:
    weight: 20
    parent: 워터마크 전처리
---

PallyCon 워터마킹 전처리 라이브러리는 인코딩/트랜스코딩 솔루션에 포팅될 수 있도록 C++ 라이브러리 형태로 구현된 전처리기입니다. 영상 인코딩 과정에서 직접 워터마크 전처리를 수행하는 방식으로, CLI 전처리기를 이용할 때 필요한 재인코딩 과정을 생략할 수 있습니다.

```mermaid
graph LR;
    subgraph 인코더/트랜스코더
    A[원본 영상] -->|인코딩 대상 프레임| B(전처리 라이브러리)
    B -->|0, 1 마크된<br>두 벌 프레임| C["인코딩 결과물<br>(0, 1 두 벌 MP4)"]
    end
    C --> D(DASH 또는<br>HLS 패키징)
    subgraph 패키저
    D --> E["패키징 결과물<br>(0, 1 두 벌)"]
    end
```

기본적으로 FFMPEG 기반의 인코더/트랜스코더에 포팅을 지원하며 기타 상용 솔루션에 적용을 위해서는 해당 솔루션 업체의 작업이 필요합니다. 더 많은 정보가 필요하거나 전처리 라이브러리의 적용을 원하는 업체는 잉카엔트웍스로 문의하시기 바랍니다.

<a href="https://www.pallycon.com/contact/?lang=ko" target="_blank" class="btn btn-primary">문의하기</a>

## 지원 환경 및 설정

- 우분투 16.04 또는 18.04 버전
- FFMPEG 3.4.2 ~ 4.1.1 버전
- Makefile의 'YOUR_FFMPEG_PATH' 항목에 FFMPEG 경로 설정
- 의존 라이브러리 설치 (아래 스크립트 이용)

```s
sudo apt-get update
sudo apt-get install -y \
		build-essential \
		libva-dev \
		libvdpau-dev \
		libssl-dev
```

## 상세 플로우

1. 인코더/트랜스코더 초기화

2. 전처리 라이브러리 초기화
  - 라이브러리 초기화를 위해 '0'과 '1' 버전을 대상으로 각각 `CreateEmbedderImplement()`와 `init()` 함수를 실행합니다.

3. 원본 영상에서 작업할 프레임을 로딩
  - 첫 프레임부터 순차적으로 인코딩 및 전처리를 진행합니다.

4. 원본 프레임을 이미지로 디코딩

5. 디코딩된 이미지에 워터마크 전처리 수행
  - '0'과 '1' 두 벌의 결과물을 생성하기 위해 8번까지의 과정을 두 차례 수행합니다.

6. 워터마크 삽입
  - 전처리 라이브러리의 `embed()` 함수를 호출하여 '0'과 '1' 마크를 삽입합니다.
   
7. 워터마크 전처리된 이미지를 출력 영상의 프레임으로 인코딩
   
8. 인코딩된 프레임을 출력 영상에 기록

9. 전처리 라이브러리 종료
  - '0'과 '1' 버전을 대상으로 전처리 라이브러리의 `DestroyEmbedderImplement()` 함수를 호출합니다.

10. 인코더/트랜스코더 종료

> 원본 영상의 마지막 프레임까지 위 과정 중 3 ~ 8번을 반복 수행해 각각 '0'과 '1' 마크가 삽입된 두 벌의 출력 영상을 생성합니다. (예: output_0.mp4 와 output_1.mp4)

## API 목록

```s
- IEmbedder* CreateEmbedderImplement(EmbedMarkSymbol sym)
  : @return embedder instance
  
- void DestroyEmbedderImplement(IEmbedder* embedder)
  : destroy embedder instance
    
- IEmbedder::init(const char* fmkeyfile, const char* accesskey)
  : initialize embedder with Keyfile and Access-Key [1]
  : @return FMError
    
- IEmbedder::embed(EmbedFrame* frame)
  : embed watermark
  : @return bool
```

### API 사용 설명

```s
   embed_0 = CreateEmbedderImplement(EmbedMarkSymbol_0) [1]
   embed_1 = CreateEmbedderImplement(EmbedMarkSymbol_1)
    |
    v
   embed_0->init("./fmkeyDemo.dat", "FOR_INKA_FORENSIC_WATERMARK_TEST") [2]
   embed_1->init("./fmkeyDemo.dat", "FOR_INKA_FORENSIC_WATERMARK_TEST")
    |
    v
 +------------------------------------+
 | EmbedFrame ef0 [3]                 |
 | EmbedFrame ef1                     |
 |  |                                 |
 |  v                                 |
 | embed_0->embed(&ef0) [4]           |loop
 | embed_1->embed(&ef1)               |
 |  |                                 |
 |  v                                 |
 | adjust_packet_size(...) [5]        |
 +------------------------------------+
    |
    v
   DestroyEmbedderImplement(embed_0)
   DestroyEmbedderImplement(embed_1)
    |
    v
   XXXX_0.mp4, XXXX_1.mp4
   
   
   [1] Server Side 0/1 Variant Watermark 방식은 0/1 파일을 조합하여 사용자 정보를 표현합니다. 0/1 파일을 만들기 위해서 인스턴스를 2개 생성합니다.
       - embedsample.cpp::fm_init_embedder()

   [2] Keyfile and Access-Key
       - 라이브러리를 사용하기 위해서는 Keyfile 경로와 Access-Key를 입력해야 합니다.
       - 샘플프로젝트에는 데모용 keyfile이 포함되어 있습니다. 데모용 Key를 사용하면 결과 영상 좌측 상단에 'Pallycon Demo' 문자가 나타나며 다른 기능상의 제약은 없습니다.
       - 상용버전 Keyfile 및 Access-Key를 발급 받으려면 별도 문의해주시기 바랍니다.
       - embedsample.cpp::fm_init_embedder()

   [3] Sample 코드와 같이 EmbedFrame 필드를 세팅합니다.
       - embedsample.cpp::fm_embed(), IEmbedder.h

   [4] IEmbedder::embed()를 호출하면 워터마크가 삽입됩니다.
       - embedsample.cpp::fm_embed()

   [5] embed 된 0,1 AVPacket의 크기를 일치시킵니다.
       - embedsample.cpp::fm_adjust_packet_size(), fm_check_filler_data_rbsp()
```

### 제약사항

Pallycon Watermark 규격상 다음과 같은 제약사항을 따라야 합니다.
인코더별로 다음 3가지를 세팅하는 방법은 상이합니다. embedsample.cpp에서는 libx264를 다루는 경우에 대해서만 설명되어 있습니다.

1. IEmbedder::embed() 후 AVPacket 크기가 같아야 함
   embedsample.cpp::fm_adjust_packet_size(), fm_check_filler_data_rbsp()

2. B-Frame을 사용하지 않음
   embedsample.h::DONT_USE_BFRAME

3. 인코더 GOP 60
   - embedsample.cpp::openOutputFile()::enc_ctx->gop_size
   - embedsample.cpp::checkAndUpdateParam()::pCtx->x264_param
