---
title: 라이선스 관련 에러코드
linktitle: 라이선스 관련 에러코드
summary: DRM 라이선스 연동과 관련한 에러코드 목록입니다.
toc: true
type: book
date: "2020-05-05T00:00:00+01:00"
lastmod: "2020-11-11T00:00:00Z"
draft: false
menu:
  multidrm:
    parent: 라이선스 발급 연동
    weight: 40
    name: 라이선스 관련 에러코드

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 40
---

DRM 라이선스 연동과 관련한 에러코드 목록입니다.

## 에러 코드 목록 {#error-code}

|<div style="width:70px">에러 코드</div> |에러 메시지 |
|---|---|
|1002 |트라이얼 플랜의 라이선스 제한 또는 유저수 제한이 초과하였습니다. |
|1003 |등록되지 않은 Site ID 입니다. |
|1004 |서비스가 종료된 Site ID 입니다. |
|1005 |트라이얼 플랜 기간이 종료 되었습니다. |
|1006 |SITE KEY 복호화에 실패하였습니다. |
|2001 |DB 접속에 실패하였습니다. |
|2701 |DB에 라이선스 발급 이력 저장이 실패하였습니다. |
|2702 |DB에 실패한 라이선스 발급 이력 저장이 실패하였습니다. |
|7001 |패키징 정보를 찾을 수 없습니다. |
|7002 |콘텐츠 사용 정보 URL에 전달될 데이터 생성에 실패하였습니다. |
|7006 |키 복호화를 실패하였습니다. (라이선스 요청 시) |
|7007 |pallycon-customdata 파싱에 실패하였습니다. |
|7008 |pallycon-customdata 복호화에 실패하였습니다. |
|7009 |Token 파싱에 실패하였습니다. |
|7010 |Token 정보를 찾을 수 없습니다. |
|7011 |Token 기간이 만료되었습니다. |
|7012 |Token CID와 일치하지 않습니다. |
|7013 |Token Json 형식이 잘못되었습니다. |
|7016 |사용할 수 없는 Token 입니다. |
|7017 |Token 사용 이력 저장에 실패하였습니다. |
|7018 |Token DRM 타입이 잘못되었습니다. |
|7019 |요청 데이터의 해시 값이 일치하지 않습니다. |
|7021 |DRM 타입 값이 NCG로 설정되어있지 않습니다. |
|7101 |Widevine 라이선스 요청 데이터 생성이 실패하였습니다. |
|7102 |Widevine 라이선스 생성이 실패하였습니다. |
|7103 |Widevine 라이선스 발급 서버와 통신을 실패하였습니다. |
|7104 |Widevine 서비스를 지원하지 않는 Site ID 입니다. |
|7105 |Widevine Device ID를 찾을 수 없습니다. |
|7106 |Widevine 서비스 인증서를 받아오는데 실패하였습니다. |
|7107 |Widevine 챌린지 데이터가 유효하지 않습니다. |
|7110 |해당 기기에 대한 Widevine 인증이 철회되어 라이선스 발급이 거부되었습니다. (internal_status : 127)|
|7111 |Widevine 프록시 응답에 대한 파싱이 실패하였습니다. |
|7112 |Widevine 프록시 응답: 유효하지 않은 pssh 데이터 (internal_status : 152) |
|7113 |Widevine 서버 인증서 파일 획득이 실패하였습니다. |
|7201 |PlayReady 라이선스 생성이 실패하였습니다. |
|7202 |PlayReady 라이선스 발급 서버와 통신이 실패하였습니다. |
|7203 |PlayReady 라이선스 요청 데이터 생성이 실패하였습니다. |
|7204 |PlayReady 라이선스 요청 데이터 읽기가 실패하였습니다. |
|7205 |PlayReady 요청 데이터 파싱에 실패하였습니다. |
|7206 |PlayReady Customdata 가 없습니다. |
|7300 |FairPlay Streaming 서비스 인증서 복호화에 실패하였습니다. |
|7301 |FairPlay Streaming 서비스를 신청하지 않은 Site ID 입니다. |
|7302 |FairPlay Streaming 용 개인키를 찾을 수 없습니다. |
|7303 |Site ID의 FairPlay Streaming 키 정보가 잘못 되었습니다. |
|7304 |FairPlay Streaming 라이선스 생성이 실패하였습니다. |
|7305 |FairPlay Streaming SPC 값이 Null 입니다. |
|7306 |FairPlay Streaming Device ID 값이 Null 입니다. |
|7308 |FairPlay Asset ID가 잘못 되었습니다. |
|7315 |FairPlay 서버 인증서 파일 획득이 실패하였습니다. |
|7316 |FairPlay Spc의 RSA 복호화가 실패하였습니다.|
|7317 |FairPlay Spc 데이터가 유효하지 않습니다. |
|8002 |Custom Error. Callback 페이지를 통한 User 인증 시 반환 되는 Error code, message 값. json 형식으로 표시 {"ERROR":"custom error code", "MESSAGE":"custom error message"} |
|8004 |nonce 값이 잘못되었습니다. |
|8006 |콜백으로 전달받은 XML 값이 잘못되었습니다. |
|8701 |콘텐츠 사용 정보 URL에 접근이 실패하였습니다. |
|8705 |콘텐츠 사용 정보 URL이 유효한 URL이 아닙니다. |
|8706 |콘텐츠 사용 정보 URL로부터 전달받은 데이터 복호화가 실패하였습니다. |
|8707 |재생 기간 정보의 시작 시간 또는 종료 시간이 제대로 입력되지 않았습니다. |
|8708 |재생 기간 정보 포맷이 잘못 입력되었습니다. |
|8709 |재생 기간 정보의 시작 시간이 종료 시간보다 뒤로 설정되었습니다. |
|8710 |재생 기간이 아직 되지 않았습니다. |
|8711 |재생 기간이 종료되었습니다. |
|8712 |HDCP 설정 값이 잘못되었습니다. |
|8715 |CGMS-A 설정 값이 잘못되었습니다. |
|8716 |APS 설정 값이 잘못되었습니다. |
|9001 |요청 파라미터가 없습니다. |
|9999 |지원하지 않는 모드입니다. |
