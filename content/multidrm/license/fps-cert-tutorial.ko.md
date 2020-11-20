---
title: FairPlay 인증서 등록 자습서
linktitle: FairPlay 인증서 자습서
summary: FPS 인증서를 발급 받고 등록하는 과정에 대한 자습서입니다.
toc: true
type: book
date: "2020-05-05T00:00:00+01:00"
lastmod: "2020-11-11T00:00:00Z"
draft: false
menu:
  multidrm:
    parent: 라이선스 발급 연동
    weight: 30
    name: FairPlay 인증서 자습서

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 30
---

## 개요

애플의 [FairPlay Streaming (FPS) DRM](https://developer.apple.com/streaming/fps/)을 사용하기 위해서는 콘텐츠 서비스 측에서 애플로부터 `FPS Deployment Package`를 발급받은 후 아래 네 가지 데이터를 생성해 PallyCon 서비스에 등록해야 합니다.

```s
- FPS Certificate 파일 (.der or .cer)
- Private key 파일 (.pem)
- Private key 암호 문자열
- Application secret key (ASK) 문자열
```

본 문서는 해당 과정을 고객사에서 정확히 처리할 수 있도록 단계 별로 자세하게 안내합니다.

## 1단계: 애플 개발자 계정 생성 및 Deployment Package 신청

`FPS Deployment Package` 발급을 위해서는 고객사(또는 iOS 앱 외주 개발사)의 애플 개발자 계정이 필요합니다. 계정이 없는 경우, 다음의 사이트에서 개발자 계정을 생성합니다.

- https://developer.apple.com/support/enrollment/

애플 [FairPlay Streaming 웹사이트](https://developer.apple.com/streaming/fps/) 하단의 ['Request Deployment Package' 링크](https://developer.apple.com/contact/fps/)를 클릭하고 애플 개발자 계정으로 로그인합니다.

입력 양식에 따라 Deployment Package를 신청하면, 애플 측의 확인 과정 후에 `FPS Credential Creation Guide` 문서가 포함된 패키지를 발급 받을 수 있습니다.

> 신청 과정에서 애플로부터 `Key Server Module(KSM)`의 구축 및 테스트를 완료했는지 질문을 받을 수 있습니다. 이에 대해서는 '3rd party DRM 업체를 이용하고 있으며 해당 업체가 이미 KSM 구축과 테스트를 마쳤다'고 답변하면 됩니다.

## 2단계: 개인키와 인증서 서명 요청(CSR) 파일 생성

발급된 `FPS Deployment Package`의 가이드 문서를 참고해 개인키(privatekey.pem)파일과 인증서 서명 요청(certreq.csr)파일을 생성합니다. 아래 내용은 가이드 문서의 `Generating a Certiﬁcate Signing Request` 부분 중 OpenSSL 방식을 설명합니다.

> 본 과정을 수행하는 PC 또는 서버 환경에 OpenSSL이 설치되어 있어야 합니다.

1. 개인키(privatekey.pem) 생성
  {{< figure library="true" src="fps_tutorial_01_PrivateKeyFile(pem).png" >}}
  - 다음 CLI 명령어를 실행해 개인키를 생성합니다.
  ```
  openssl genrsa -aes256 -out privatekey.pem 1024
  ```
  - [Enter pass phrase for privatekey.pem: ] 입력란에 개인키 암호를 임의로 입력합니다. 여기에서 지정한 개인키 암호 문자열을 별도로 기록해둡니다.
  - 개인키 암호는 최대 32자까지 지원하며, 특수문자는 지원되지 않습니다.

2. 인증서 서명 요청 파일 생성
  {{< figure library="true" src="fps_tutorial_02_CertificateSigningRequestFile(csr).png" >}}
  - 다음 명령어를 실행합니다. -subj 파라미터의 내용은 고객사에 맞게 수정할 수 있습니다.
  ```
  openssl req -new -sha1 -key privatekey.pem -out certreq.csr -subj "/CN=SubjectName/OU=OrganizationalUnit/O=Organization/C=US"
  ```
  - [Enter pass phrase for privatekey.pem: ] 입력란에 이전 과정에서 지정한 개인키 암호를 입력합니다. 

## 3단계: 애플 개발자 포털에서 FPS 인증서 생성

1. 아래 화면과 같이 애플 개발자 포털에 로그인해 `Certificate, IDs & Profiles` 메뉴로 이동합니다.
  {{< figure library="true" src="fps_tutorial_03_AppleDeveloperPortalLogin.png" >}}

2. 해당 메뉴 화면에서 `+` 버튼을 눌러 `Create a New Certificate` 화면으로 이동합니다.
  {{< figure library="true" src="fps_tutorial_04_CertificatesList.png" >}}

3. `FairPlay Streaming Certificate` 항목을 선택하고 `Continue` 버튼을 클릭합니다.
  {{< figure library="true" src="fps_tutorial_05_CertTypeSelect.png" >}}

4. `Choose File`을 클릭하고 이전 단계에서 생성한 `certreq.csr` 파일을 선택한 뒤 `Continue` 버튼을 클릭합니다.
  {{< figure library="true" src="fps_tutorial_06_ChooseCsrFile.png" >}}

5. `Application Secret Key(ASK)` 문자열을 복사해 별도로 기록한 후에 아래 빈 칸에 붙여넣고 `Continue` 버튼을 클릭합니다.
  {{< figure library="true" src="fps_tutorial_07_ASKValueCheck.png" >}}

6. ASK 문자열을 별도로 기록해두었는지 확인하는 팝업이 표시됩니다. `Generate` 버튼을 클릭합니다.
  {{< figure library="true" src="fps_tutorial_08_ASKSavedCheckPopup.png" >}}

7. 위 과정이 완료되면 `Certificate` 목록에 `FairPlay Streaming` 유형으로 생성된 인증서가 표시됩니다.
  {{< figure library="true" src="fps_tutorial_09_ChecklistofAddedFPSCert.png" >}}

8. `Download` 버튼을 클릭해 `FPS 인증서 파일`(fairplay.cer)을 저장합니다.
  {{< figure library="true" src="fps_tutorial_10_DownloadCert.png" >}}

## 4단계: PallyCon 콘솔 사이트에 인증서 등록

1. [PallyCon 콘솔 사이트](https://console.pallycon.com)에 로그인 합니다.

2. `사이트 설정` 메뉴를 클릭하고 `연동 설정` 탭으로 이동합니다.

3. `멀티DRM 설정`의 `FPS Cert 등록` 항목에서 `등록` 버튼을 클릭합니다.

4. 팝업 창에 이전 과정에서 생성된 파일(개인키, 인증서)과 문자열(개인키 암호, ASK)을 입력해 등록합니다.

> 주의: 테스트용으로 2단계 과정을 여러 번 수행한 경우, 서로 맞지 않는 인증서/비밀키 쌍 또는 잘못된 비밀키 암호를 등록하는 경우가 발생할 수 있습니다. 정확한 데이터를 등록할 수 있도록 유의하시기 바랍니다.

## 다음 과정

이상으로 `FairPlay 인증서 등록 자습서`를 마칩니다.

`FPS Cert 등록`을 완료한 후에는 [콘텐츠 패키징](../../packaging)을 통해 FairPlay DRM으로 보호되는 HLS 콘텐츠를 생성하고 [클라이언트 플레이어 연동](../../clients)으로 재생을 확인합니다.

> 클라이언트 연동 시 FPS Certification 파일의 다운로드 URL은 `https://license.pallycon.com/ri/fpsKeyManager.do?siteId='Site_ID'` 입니다. `Site_ID`는 PallyCon에서 발급된 서비스 사이트 ID(4byte)를 입력합니다.
