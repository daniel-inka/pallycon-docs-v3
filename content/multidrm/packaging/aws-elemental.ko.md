---
linktitle: AWS Elemental 연동 가이드
weight: 40

# Page metadata.
title: AWS Elemental 연동 가이드
summary: 이 가이드는 MediaConvert, MediaPackage 샘플 구축하는 방법을 포함하여 설명합니다.
date: "2018-09-09T00:00:00Z"
lastmod: "2020-11-03T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: true  # Show table of contents? true/false
type: book  # Do not modify.

menu:
  multidrm:
    weight: 40
    parent: 콘텐츠 패키징
---

## 개요

PallyCon KMS는 AWS Elemental MediaConvert, MediaPackage 에서 Multi DRM 패키징에 필요한 키를 발급해주는 [SPEKE](https://docs.aws.amazon.com/ko_kr/speke/latest/documentation/what-is-speke.html) (Secure Packager and Encoder Key Exchange)를 지원합니다.

PallyCon KMS URL을 DRM encryption setting 항목의 URL에 세팅하면 간단하게 연동이 완료됩니다. 이 가이드는 MediaConvert, MediaPackage 샘플 구축하는 방법을 포함하여 설명합니다.

## 동영상 튜토리얼

MediaConvert를 이용해 DASH VOD 콘텐츠에 DRM을 적용하고, 패키징된 DRM 콘텐츠를 재생하는 튜토리얼 영상입니다.

{{< youtube SZwphL0ryYY >}}

> 최적의 재생을 위해 화면 품질을 '1080p'로 선택하고 자막(한글 또는 영문)을 선택하여 재생하시기 바랍니다.

## MediaConvert 연동 {#mediaconvert}

### MediaConvert IAM 권한 생성 {#mediaconvert-iam}

[IAM 설정  AWS Guide 문서](https://docs.aws.amazon.com/ko_kr/mediaconvert/latest/ug/iam-role.html)를 참조해 아래와 같은 작업을 진행합니다.

1. AWS Console에서 IAM 서비스를 선택합니다.
2. `Roles` 탭에 들어가 `create role`을 선택합니다.
3. `MediaConvert`를 선택 후 `Next: permission` 버튼을 클릭합니다.
4. S3접근 권한과 APIGateway 접근 권한이 가능한 부분을 확인하고 `Next: Review` 버튼을 클릭합니다.
{{< figure library="true" numbered="true" src="iam-1.png" title="IAM role" >}}

5. RoleName을 `MediaConvert-role`로 설정 후 `create role` 버튼을 눌러 생성합니다.
{{< figure library="true" numbered="true" src="iam-2.png" title="Create role" >}}

### MediaConvert 작업 생성 및 IAM 권한 설정  {#mediaconvert-iam-set}

1. AWS Console에서 MediaConvert 서비스를 선택합니다.
2. Jobs 탭의 `create job` 버튼을 눌러 job 생성을 시작합니다.
3. Job settings 화면의 IAM role 설정 부분에 이전 단계에서 생성한 `MediaConvert-role`을 선택합니다.
{{< figure library="true" numbered="true" src="mediaconvert-0.png" title="MediaConvert role" >}}

### MediaConvert Input 설정  {#mediaconvert-input}

1. Input 항목에 s3에 있는 패키징할 컨텐츠 경로를 입력합니다.
{{< figure library="true" numbered="true" src="mediaconvert-1.png" title="MediaConvert input" >}}

### MediaConvert Output groups 설정 {#mediaconvert-outputgroups}

1. Output groups 에 Add 버튼을 눌러 ouptput을 추가합니다. (PlayReady와 Widevine의 경우는 dash ISO, FairPlay의 경우는 Apple HLS를 선택)
{{< figure library="true" numbered="true" src="mediaconvert-2.png" title="MediaConvert output" >}}

2. Custom group name에는 사용자가 구분하기 편한 이름을 입력합니다.

3. Destination 항목에 패키징이 완료된 파일이 들어갈 s3상의 경로를 입력합니다.
{{< figure library="true" numbered="true" src="mediaconvert-3.png" title="MediaConvert output" >}}

4. DRM encryption 옵션을 선택 후 Resource ID, System ID, URL을 입력합니다.
	- Resource ID: [DRM 라이선스 토큰 가이드](../../license/license-token)에 나온 연동 규격에서 CID(content id)에 해당 하는 값입니다.
	- System ID:  [Dash System ID](http://dashif.org/identifiers/content_protection/)에 규정된 DRM 별 system id 값 입니다. DASH output에는 아래 화면과 같이 Widevine과 PlayReady ID를 입력하고, HLS output에는 FairPlay ID를 입력합니다.
		- PlayReady: 9A04F079-9840-4286-AB92-E65BE0885F95
		- Widevine: EDEF8BA9-79D6-4ACE-A3C8-27DCD51D21ED
		- FairPlay: 94CE86FB-07FF-4F43-ADB8-93D2FA968CA2
	- 키 공급자 URL: 다음과 같은 KMS 연동 URL을 입력합니다. 마지막 부분의 `enc-token`은 PallyCon 서비스 가입 시 생성되는 API 인증 토큰으로, [PallyCon 콘솔](https://console.pallycon.com) 사이트에서 확인할 수 있습니다.
        ```
		https://kms.pallycon.com/cpix/getKey?enc-token={enc-koken}
		```
	- 인증서 ARN: 빈칸으로 둡니다.
	- 재생 디바이스 호환성: CENC v1
	{{< figure library="true" numbered="true" src="mediaconvert-4.png" title="DRM encryption" >}}

5. Outputs 를 설정후 Create 버튼을 클릭합니다.
	- DASH output의 경우, 비디오와 오디오가 하나의 output으로 출력되면 일부 Widevine 환경에서 재생이 안 되므로 비디오와 오디오 트랙을 나눠서 생성해야 합니다. ('Add output' 버튼 클릭)
	{{< figure library="true" numbered="true" src="mediaconvert-5.png" title="MediaConvert output 1" >}}
	{{< figure library="true" numbered="true" src="mediaconvert-6.png" title="MediaConvert output 2" >}}

6. s3 상에 생성된 파일을 재생할 수 있도록 make public 또는 사용권한 설정을 해줍니다.

### Apple HLS 출력 그룹 DRM 설정 {#applehls}

애플 기기와 다른 플랫폼을 모두 지원하려면 하나의 입력에 대해서 'DASH ISO'와 'Apple HLS' 두 개의 출력 그룹을 생성해야 합니다. 'Apple HLS' 출력 그룹의 DRM 암호화 옵션은 아래와 같이 설정합니다.

- 암호화 방법: 샘플 AES
- 키 공급자 유형: SPEKE
- Resource ID: DASH 출력 그룹과 동일한 콘텐츠 ID 입력
- System ID: FairPlay DRM system ID (94CE86FB-07FF-4F43-ADB8-93D2FA968CA2)
- 키 공급자 URL: DASH 출력 그룹과 동일한 PallyCon KMS URL
- 그 외 항목: 기본값 그대로 사용

### CMAF 패키징 관련 유의 사항  {#notes-on-cmaf}

`DASH-ISO`와 `Apple HLS` 외에도 SPEKE 연동을 통해 `CMAF` (Common Media Application Format) 방식의 출력을 생성할 수도 있습니다.

하지만 현재 시점에 애플 기기는 `AES CBC 암호화` 만을 지원하고 윈도우즈(Edge, IE 브라우저)는 `AES CTR 암호화` 만을 지원하기 때문에 하나의 CMAF 콘텐츠로 모든 플랫폼을 지원하는 것은 아직 불가능합니다.

## MediaPackage 연동 {#mediapackage}

HLS upload가 가능한 AWS MediaLive 같은 서비스와 연동하여 실시간으로 컨텐츠를 암호화 할 수 있습니다.

> 주의: MediaPackage에서 DRM 패키징 연동을 수행하려면 MediaLive의 Output Group에서는 Encryption 설정을 해제해야 합니다.

### MediaPackage IAM 권한 생성 {#mediapackage-iam}

1. [MediaConvert IAM 권한 생성](#mediaconvert-iam) 과 동일하게 생성하며 Role Name만 SPEKEAccess 로 생성합니다.

2. Roles 탭에서 SPEKEAccess role을 선택 후 Trust relationships 탭의 Edit trust relationship 버튼을 클릭합니다.
 {{< figure library="true" numbered="true" src="iam-3.png" title="SPEKEAccess role" >}}

3. Principal.Service의 값을 mediapackage.amazonaws.com 으로 변경 후 Update 버튼을 클릭합니다.
 {{< figure library="true" numbered="true" src="iam-4.png" title="Update role" >}}

### MediaPackage Channel 생성  {#mediapackage-channel}

1. AWS Console에서 MediaPackage 서비스를 선택합니다.

2. Channel을 생성합니다.
 {{< figure library="true" numbered="true" src="mediapackage-0.png" title="Create channel" >}}

3. endpoints 에서 Add 버튼을 눌러 endpoint 설정을 합니다.

4. endpoint name, packager settings 부분 등 원하는 컨텐츠 스펙에 맞게 설정 합니다.

5. [MediaConvert Output groups 설정의 4번](#mediaconvert-outputgroups) 과 동일한 방식으로 Encryption과 Output을 설정합니다.

6. Role ARN에 생성한 SPEKEAccess Role을 입력합니다.

7. Save 버튼을 클릭합니다.
 {{< figure library="true" numbered="true" src="mediapackage-1.png" title="MediaPackage options" >}}

### Key rotation 지원 관련

- MediaPackage를 통한 key rotation 기능은 현재 지원하고 있지 않습니다. 따라서 MediaPackage Endpoint의 Encryption 옵션에서 `Additional Configuration`의 `Key rotation` 체크를 해제해야 합니다.
