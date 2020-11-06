---
title: "워터마크 검출"
date: 2018-08-28T16:52:20+09:00
weight: 30
chapter: true
pre: "<b>3. </b>"
---

# 워터마크 검출

워터마킹된 영상이 유출되어 불법 배포되는 경우, 유출이 의심되는 영상을 PallyCon 서비스에 검출 의뢰할 수 있습니다. 워터마크 검출은 영상의 각 프레임을 분석하여 원본 워터마크 패턴을 감지하고, 삽입 시 사용한 비밀 키로 데이터를 해독합니다.

검출 과정을 통해 워터마크 페이로드를 찾아내면, 세션 데이터베이스에서 해당 페이로드를 키 값으로 하는 세션 정보를 찾아 검출 결과로 리포트합니다.

```mermaid
sequenceDiagram
    participant A as 서비스 사이트
    participant B as PallyCon 서비스
    A ->> B: 유출 의심 영상
    Note right of B: 워터마크(페이로드) 검출
    B -->> B: 영상 프레임 분석
    opt 워터마크 검출 시
    Note right of B: 워터마크 세션 데이터베이스
    B -->> B: 해당 세션 정보 검색
    end
    B ->> A: 검출 결과 리포트
```

워터마크 검출 서비스에 대한 상세 안내는 별도 문의해 주시기 바랍니다.

<a href="https://www.pallycon.com/contact/?lang=ko" target="_blank" class="btn btn-default">문의하기</a>

***