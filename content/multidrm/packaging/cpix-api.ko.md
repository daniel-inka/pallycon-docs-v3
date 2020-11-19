---
linktitle: CPIX API 가이드
weight: 3

# Page metadata.
title: CPIX API 가이드
summary: 본 API를 이용하면 CPIX 기반의 키 연동을 지원하는 인코더/트랜스코더 솔루션과 PallyCon 멀티 DRM을 쉽게 연동할 수 있습니다.
date: "2018-09-09T00:00:00Z"
lastmod: "2020-06-26T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: true  # Show table of contents? true/false
type: book  # Do not modify.

menu:
  multidrm:
    weight: 3
    parent: 콘텐츠 패키징
---

## 개요

CPIX API는 DASH 산업 포럼에서 정의한 [Content Protection Information Exchange Format (CPIX) 표준 가이드](https://dashif.org/docs/CPIX2.1/HTML/Index.html)에 따라 구현된 API로, 미디어 콘텐츠의 패키징 과정에서 멀티 DRM을 적용하는데 필요한 키를 연동하는 규격을 정의합니다. 본 API를 이용하면 CPIX 기반의 키 연동을 지원하는 인코더/트랜스코더 솔루션과 PallyCon 멀티 DRM을 쉽게 연동할 수 있습니다.

## CPIX API 유형

PallyCon 멀티 DRM은 API의 구현 주체에 따라 다음과 같은 유형의 CPIX API와 연동 가능합니다.

- `PallyCon CPIX API`: PallyCon에서 구현한 CPIX API로, 인코더/트랜스코더 솔루션 업체에서 해당 API로 PallyCon 멀티 DRM과 연동할 수 있습니다. `Flussonic Media Server`와의 연동에 PallyCon CPIX API가 적용되었습니다.

- `SPEKE API`: AWS Elemental에서 구현한 CPIX API로, PallyCon과 사전 연동되어 AWS Elemental 미디어 서비스의 MediaPackage 또는 MediaConvert 제품에서 PallyCon 멀티 DRM을 쉽게 적용할 수 있습니다. SPEKE API 연동에 대한 자세한 내용은 [해당 가이드](../aws-elemental)를 참고하시기 바랍니다.

- `Anevia CPIX API`: Anevia에서 구현한 CPIX API로, Anevia NEA-DVR 솔루션과 사전 연동되어 PallyCon 멀티 DRM을 쉽게 적용할 수 있습니다.

## PallyCon CPIX API

DASH-IF의 CPIX 구현 가이드에 따라 PallyCon에서 구현하여 제공하는 CPIX API입니다.

> CPIX 규격에 대한 자세한 사항은 다음의 가이드를 참고하시기 바랍니다.
> https://dashif.org/docs/CPIX2.1/HTML/Index.html

### 지원 기능

PallyCon CPIX API를 통한 연동은 아래와 같은 콘텐츠와 기능을 지원합니다.

| | PlayReady | Widevine | FairPlay | AES-128 |
|:-----:|:-----:|:-----:|:-----:|:-----:|
| DASH | <i class="fas fa-check"></i> (키 로테이션 지원)| <i class="fas fa-check"></i> (키 로테이션 지원) | | |
| HLS | | | <i class="fas fa-check"></i> (키 로테이션 지원) | <i class="fa fa-check"></i> (NCG DRM 지원)|

### KMS 연동 URL 규격

PallyCon CPIX API에서 제공하는 KMS 연동 URL 규격은 다음과 같습니다. URL 마지막 부분의 `enc-token`은 PallyCon 서비스 가입 시 생성되는 API 인증 토큰으로, PallyCon 콘솔 사이트에서 확인할 수 있습니다.

```s
V1 규격 KMS URL - https://kms.pallycon.com/v1/cpix/pallycon/getKey/{enc-token}
V2 규격 KMS URL - https://kms.pallycon.com/v2/cpix/pallycon/getKey/{enc-token}
```

> 인코더/트랜스코더 솔루션에서 위 URL을 호출하여 DRM 암호화에 필요한 키를 발급 받습니다. 요청 데이터의 Body 부분에 아래 예제와 같은 CPIX 데이터를 추가하여 `POST` 방식으로 호출합니다.

### KMS 연동 V2 규격 및 멀티키 패키징 지원

V2 규격에는 멀티키 패키징을 위한 요청/응답 데이터가 추가되었습니다. 멀티키 패키징은 해상도별 영상 트랙을 각각 다른 키로 암호화하는 것으로, 헐리우드 스튜디오의 요구 사항인 해상도 별 보안 레벨 설정을 위해 필요합니다.

멀티키 패키징 시, 해당 XML 요청 데이터에 아래와 같은 사항을 추가로 입력해 전송합니다.

> 싱글키 패키징 시에는 요청 데이터 XML 예제에서 아래 항목들을 제외하고 요청합니다.

#### IntendedTrackType

`cpix:ContentKeyUsageRule` 태그에 추가되는 파라미터로, 해당 kid로 암호화될 트랙(SD, HD 등)을 설정합니다.
입력 가능한 값과 기본 해상도는 아래와 같습니다.

- AUDIO: 오디오 트랙용
- SD: 576p 이하 해상도
- HD: 720p ~ 1080p
- UHD1: 4K
- UHD2: 8K

#### VideoFilter

`minPixels`와 `maxPixels` 파라미터로 해당 비디오 트랙의 최소/최대 픽셀 수를 지정합니다.

### Content ID 입력

PallyCon CPIX API를 이용해 콘텐츠를 패키징할 때, 콘텐츠 서비스의 CMS에서 관리하는 해당 콘텐츠의 고유 ID(CID)를 입력해야 합니다. 해당 CID는 PallyCon 라이선스 서버에 등록되어 콘텐츠 재생 시 DRM 라이선스의 요청 및 발급에 사용됩니다.

아래 각 콘텐츠 유형 별 요청 규격(XML)의 `cpix:CPIX` 태그에 아래와 같이 콘텐츠 ID를 입력해 전송합니다. (최대 200자리 영숫자 문자열)

```xml
<cpix:CPIX id="your-content-id"
```

### 예제 - 라이브 DASH 콘텐츠

> 라이브 콘텐츠의 경우, ContentKeyPeriodList index 값에 따라 로테이션 된 키 값을 응답합니다.

#### Request body

```xml
<?xml version="1.0" encoding="UTF-8"?>
<cpix:CPIX id="cpix-test-cid" xmlns:cpix="urn:dashif:org:cpix" xmlns:pskc="urn:ietf:params:xml:ns:keyprov:pskc" xmlns:speke="urn:aws:amazon:com:speke">
	<cpix:ContentKeyList>
		<cpix:ContentKey kid="ca65e643-482c-4a21-a204-05570a1e276c"></cpix:ContentKey>
	</cpix:ContentKeyList>
	<cpix:DRMSystemList>
	    <!-- Common encryption / MSS (Playready) -->
		<cpix:DRMSystem kid="ca65e643-482c-4a21-a204-05570a1e276c" systemId="9A04F079-9840-4286-AB92-E65BE0885F95" />
		<!-- Common encryption (Widevine)-->
		<cpix:DRMSystem kid="681e5b39-49f2-4dfa-b744-86573c22e6fb" systemId="EDEF8BA9-79D6-4ACE-A3C8-27DCD51D21ED" />
	</cpix:DRMSystemList>
	<cpix:ContentKeyPeriodList>
		<cpix:ContentKeyPeriod id="keyPeriod_e7c4423c-a2c6-46f4-bef3-2bfed6bae9c9" index="128"/>
	</cpix:ContentKeyPeriodList>
	<cpix:ContentKeyUsageRuleList>
            <cpix:ContentKeyUsageRule intendedTrackType="HD" kid="ca65e643-482c-4a21-a204-05570a1e276c"> <!-- V2에 추가됨 : intendedTrackType -->
                <cpix:KeyPeriodFilter periodId="keyPeriod_e7c4423c-a2c6-46f4-bef3-2bfed6bae9c9"/>
                <cpix:VideoFilter maxPixels="2073600" minPixels="921600"/><!-- V2 추가됨 -->
            </cpix:ContentKeyUsageRule>
	</cpix:ContentKeyUsageRuleList>
</cpix:CPIX>
```

#### Response

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<cpix:CPIX id="cpix-test-cid" xmlns:cpix="urn:dashif:org:cpix" xmlns:speke="urn:aws:amazon:com:speke" xmlns:pskc="urn:ietf:params:xml:ns:keyprov:pskc" >
    <cpix:ContentKeyList>
        <cpix:ContentKey explicitIV="S2ljHDlFRStjCuOpN8I7+w==" kid="6bac8ec3-0ee3-379f-3d21-290b3a2bc9c3">
            <cpix:Data>
                <pskc:Secret>
                    <pskc:PlainValue>OlRFK6LSytFr2HnN1HqwWw==</pskc:PlainValue>
                </pskc:Secret>
            </cpix:Data>
        </cpix:ContentKey>
    </cpix:ContentKeyList>
    <cpix:ContentKeyPeriodList>
        <cpix:ContentKeyPeriod id="keyPeriod_e7c4423c-a2c6-46f4-bef3-2bfed6bae9c9" index="128"/>
    </cpix:ContentKeyPeriodList>
    <cpix:ContentKeyUsageRuleList>
        <cpix:ContentKeyUsageRule intendedTrackType="HD" kid="6bac8ec3-0ee3-379f-3d21-290b3a2bc9c3"><!-- V2에 추가됨 : intendedTrackType -->
            <cpix:KeyPeriodFilter periodId="keyPeriod_e7c4423c-a2c6-46f4-bef3-2bfed6bae9c9"/>
            <cpix:VideoFilter maxPixels="2073600" minPixels="921600"/><!-- V2에 추가됨 -->
        </cpix:ContentKeyUsageRule>
    </cpix:ContentKeyUsageRuleList>
    <cpix:DRMSystemList>
        <cpix:DRMSystem kid="6bac8ec3-0ee3-379f-3d21-290b3a2bc9c3" systemId="9A04F079-9840-4286-AB92-E65BE0885F95">
            <cpix:ContentProtectionData>qAUAAAEAAQCeB...A=</cpix:ContentProtectionData><!-- V2에 추가됨 -->
            <speke:ProtectionHeader>qAUAAAEAAQCeB...A=</speke:ProtectionHeader><!-- V2에 추가됨 -->
            <cpix:PSSH>AAADTHBzc2gAAAAAmgTweZhAQoarkuZb4IhflQAAAywsAwAAAQABACIDPABXAFIATQBIAEUAQQBEAEUAUgAgAHgAbQBsAG4AcwA9ACIAaAB0AHQAcAA6AC8ALwBzAGMAaABlAG0AYQBzAC4AbQBpAGMAcgBvAHMAbwBmAHQALgBjAG8AbQAvAEQAUgBNAC8AMgAwADAANwAvADAAMwAvAFAAbABhAHkAUgBlAGEAZAB5AEgAZQBhAGQAZQByACIAIAB2AGUAcgBzAGkAbwBuAD0AIgA0AC4AMAAuADAALgAwACIAPgA8AEQAQQBUAEEAPgA8AFAAUgBPAFQARQBDAFQASQBOAEYATwA+ADwASwBFAFkATABFAE4APgAxADYAPAAvAEsARQBZAEwARQBOAD4APABBAEwARwBJAEQAPgBBAEUAUwBDAFQAUgA8AC8AQQBMAEcASQBEAD4APAAvAFAAUgBPAFQARQBDAFQASQBOAEYATwA+ADwASwBJAEQAPgB3ADQANgBzAGEAKwBNAE8AbgB6AGMAOQBJAFMAawBMAE8AaQB2AEoAdwB3AD0APQA8AC8ASwBJAEQAPgA8AEMASABFAEMASwBTAFUATQA+ADYAaQBiAFAANAAwAHUAVABPAFoAMAA9ADwALwBDAEgARQBDAEsAUwBVAE0APgA8AEwAQQBfAFUAUgBMAD4AaAB0AHQAcABzADoALwAvAHQAZQBzAHQAdABvAGsAeQBvAC4AcABhAGwAbAB5AGMAbwBuAC4AYwBvAG0ALwByAGkALwBwAGwAYQB5AHIAZQBhAGQAeQAvAGwAaQBjAGUAbgBzAGUATQBhAG4AYQBnAGUAcgAuAGQAbwA8AC8ATABBAF8AVQBSAEwAPgA8AEMAVQBTAFQATwBNAEEAVABUAFIASQBCAFUAVABFAFMAPgA8AFAARQBSAEkATwBEAF8ASQBOAEQARQBYAD4AMQAyADgAPAAvAFAARQBSAEkATwBEAF8ASQBOAEQARQBYAD4APAAvAEMAVQBTAFQATwBNAEEAVABUAFIASQBCAFUAVABFAFMAPgA8AC8ARABBAFQAQQA+ADwALwBXAFIATQBIAEUAQQBEAEUAUgA+AA==</cpix:PSSH>
        </cpix:DRMSystem>
        <cpix:DRMSystem kid="6bac8ec3-0ee3-379f-3d21-290b3a2bc9c3" systemId="EDEF8BA9-79D6-4ACE-A3C8-27DCD51D21ED">
            <cpix:PSSH>AAAAaHBzc2gAAAAA7e+LqXnWSs6jyCfc1R0h7QAAAEgIARIQa6yOww7jN589ISkLOivJwxoMaW5rYWVudHdvcmtzIh1jcGl4LXRlc3QtY2lkMDAwMDAwMDAwMDAwMDA4MCoCSEQ4gAE=</cpix:PSSH>
        </cpix:DRMSystem>
    </cpix:DRMSystemList>
</cpix:CPIX>
```

### 예제 - 라이브 HLS 콘텐츠

#### Request body

```xml
<?xml version="1.0" encoding="UTF-8"?>
<cpix:CPIX id="cpix-test-cid" xmlns:cpix="urn:dashif:org:cpix" xmlns:pskc="urn:ietf:params:xml:ns:keyprov:pskc" xmlns:speke="urn:aws:amazon:com:speke" >
 <cpix:ContentKeyList>
     <cpix:ContentKey kid="681e5b39-49f2-4dfa-b744-86573c22e6fb"></cpix:ContentKey>
 </cpix:ContentKeyList>
 <cpix:DRMSystemList>
    <!-- HLS SAMPLE-AES (FairPlay) -->
    <cpix:DRMSystem kid="681e5b39-49f2-4dfa-b744-86573c22e6fb" systemId="94CE86FB-07FF-4F43-ADB8-93D2FA968CA2">
        <cpix:ContentProtectionData />
        <cpix:PSSH />
        <cpix:URIExtXKey />
    </cpix:DRMSystem>
 </cpix:DRMSystemList>
</cpix:CPIX>
```

#### Response

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<cpix:CPIX id="cpix-test-cid" xmlns:cpix="urn:dashif:org:cpix" xmlns:speke="urn:aws:amazon:com:speke" xmlns:pskc="urn:ietf:params:xml:ns:keyprov:pskc" >
    <cpix:ContentKeyList>
        <cpix:ContentKey explicitIV="S2ljHDlFRStjCuOpN8I7+w==" kid="6bac8ec3-0ee3-379f-3d21-290b3a2bc9c3">
            <cpix:Data>
                <pskc:Secret>
                    <pskc:PlainValue>OlRFK6LSytFr2HnN1HqwWw==</pskc:PlainValue>
                </pskc:Secret>
            </cpix:Data>
        </cpix:ContentKey>
    </cpix:ContentKeyList>
    <cpix:ContentKeyPeriodList>
        <cpix:ContentKeyPeriod id="keyPeriod_e7c4423c-a2c6-46f4-bef3-2bfed6bae9c9" index="128"/>
    </cpix:ContentKeyPeriodList>
    <cpix:ContentKeyUsageRuleList>
        <cpix:ContentKeyUsageRule intendedTrackType="HD" kid="6bac8ec3-0ee3-379f-3d21-290b3a2bc9c3"><!-- V2에 추가됨 : intendedTrackType -->
            <cpix:KeyPeriodFilter periodId="keyPeriod_e7c4423c-a2c6-46f4-bef3-2bfed6bae9c9"/>
            <cpix:VideoFilter maxPixels="2073600" minPixels="921600"/><!-- V2에 추가됨 -->
        </cpix:ContentKeyUsageRule>
    </cpix:ContentKeyUsageRuleList>
    <cpix:DRMSystemList>
        <!-- HLS SAMPLE-AES (FairPlay) -->
        <cpix:DRMSystem kid="6bac8ec3-0ee3-379f-3d21-290b3a2bc9c3" systemId="94CE86FB-07FF-4F43-ADB8-93D2FA968CA2">
            <cpix:HLSSignalingData>I0VYVC1YLUtFWTpN...DA1</cpix:HLSSignalingData><!-- V2에 추가됨 -->
            <cpix:HLSSignalingData playlist="master">I0VYVC1YLVNFU...DU=</cpix:HLSSignalingData><!-- V2에 추가됨 -->
            <cpix:URIExtXKey>c2tkOi8vY3BpeC10ZXN0LWNpZDAwMDAwMDAwMDAwMDAwODA=</cpix:URIExtXKey>
        </cpix:DRMSystem>
    </cpix:DRMSystemList>
</cpix:CPIX>
```

### 예제 - VOD DASH 콘텐츠

#### Request body

```xml
<?xml version="1.0" encoding="UTF-8"?>
<cpix:CPIX id="your-content-id" xmlns:cpix="urn:dashif:org:cpix" xmlns:pskc="urn:ietf:params:xml:ns:keyprov:pskc" xmlns:speke="urn:aws:amazon:com:speke">
  <cpix:ContentKeyList>
      <cpix:ContentKey kid="681e5b39-49f2-4dfa-b744-86573c22e6fb"></cpix:ContentKey>
  </cpix:ContentKeyList>
  <cpix:DRMSystemList>
      <!-- Common encryption / MSS (Playready) -->
      <cpix:DRMSystem kid="681e5b39-49f2-4dfa-b744-86573c22e6fb" systemId="9A04F079-9840-4286-AB92-E65BE0885F95" />
      <!-- Common encryption (Widevine)-->
      <cpix:DRMSystem kid="681e5b39-49f2-4dfa-b744-86573c22e6fb" systemId="EDEF8BA9-79D6-4ACE-A3C8-27DCD51D21ED" />
  </cpix:DRMSystemList>
</cpix:CPIX>
```

#### Response

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<cpix:CPIX id="cpix-test-cid" xmlns:cpix="urn:dashif:org:cpix" xmlns:speke="urn:aws:amazon:com:speke" xmlns:pskc="urn:ietf:params:xml:ns:keyprov:pskc" >
   <cpix:ContentKeyList>
       <cpix:ContentKey explicitIV="MDEyMzQ1Njc4OWFiY2RlZg==" kid="12ea753c-23e7-bc02-4474-b2b976c43beb">
           <cpix:Data>
               <pskc:Secret>
                   <pskc:PlainValue>SzC1qc1cEpyFU6t/lL7Byw==</pskc:PlainValue>
               </pskc:Secret>
           </cpix:Data>
       </cpix:ContentKey>
   </cpix:ContentKeyList>
   <cpix:DRMSystemList>
       <!-- Common encryption / MSS (Playready) -->
       <cpix:DRMSystem kid="12ea753c-23e7-bc02-4474-b2b976c43beb" systemId="9A04F079-9840-4286-AB92-E65BE0885F95">
            <cpix:ContentProtectionData>qAUAAAEAAQCeB...A=</cpix:ContentProtectionData><!-- V2에 추가됨 -->
            <speke:ProtectionHeader>qAUAAAEAAQCeB...A=</speke:ProtectionHeader><!-- V2에 추가됨 -->
           <cpix:PSSH>AAACwnBzc2gAAAAAmgTweZhAQoarkuZb4IhflQAAAqKiAgAAAQABAJgCPABXAFIATQBIAEUAQQBEAEUAUgAgAHgAbQBsAG4AcwA9ACIAaAB0AHQAcAA6AC8ALwBzAGMAaABlAG0AYQBzAC4AbQBpAGMAcgBvAHMAbwBmAHQALgBjAG8AbQAvAEQAUgBNAC8AMgAwADAANwAvADAAMwAvAFAAbABhAHkAUgBlAGEAZAB5AEgAZQBhAGQAZQByACIAIAB2AGUAcgBzAGkAbwBuAD0AIgA0AC4AMAAuADAALgAwACIAPgA8AEQAQQBUAEEAPgA8AFAAUgBPAFQARQBDAFQASQBOAEYATwA+ADwASwBFAFkATABFAE4APgAxADYAPAAvAEsARQBZAEwARQBOAD4APABBAEwARwBJAEQAPgBBAEUAUwBDAFQAUgA8AC8AQQBMAEcASQBEAD4APAAvAFAAUgBPAFQARQBDAFQASQBOAEYATwA+ADwASwBJAEQAPgBQAEgAWABxAEUAdQBjAGoAQQByAHgARQBkAEwASwA1AGQAcwBRADcANgB3AD0APQA8AC8ASwBJAEQAPgA8AEMASABFAEMASwBTAFUATQA+AC8ARgA3AEIAMQBmAEgAMgBlADAAYwA9ADwALwBDAEgARQBDAEsAUwBVAE0APgA8AEwAQQBfAFUAUgBMAD4AaAB0AHQAcABzADoALwAvAHQAZQBzAHQAdABvAGsAeQBvAC4AcABhAGwAbAB5AGMAbwBuAC4AYwBvAG0ALwByAGkALwBwAGwAYQB5AHIAZQBhAGQAeQAvAGwAaQBjAGUAbgBzAGUATQBhAG4AYQBnAGUAcgAuAGQAbwA8AC8ATABBAF8AVQBSAEwAPgA8AC8ARABBAFQAQQA+ADwALwBXAFIATQBIAEUAQQBEAEUAUgA+AA==</cpix:PSSH>
       </cpix:DRMSystem>
       <!-- Common encryption (Widevine)-->
       <cpix:DRMSystem kid="12ea753c-23e7-bc02-4474-b2b976c43beb" systemId="EDEF8BA9-79D6-4ACE-A3C8-27DCD51D21ED">
           <cpix:PSSH>AAAAVXBzc2gAAAAA7e+LqXnWSs6jyCfc1R0h7QAAADUIARIQEup1PCPnvAJEdLK5dsQ76xoMaW5rYWVudHdvcmtzIg1jcGl4LXRlc3QtY2lkKgJIRA==</cpix:PSSH>
       </cpix:DRMSystem>
   </cpix:DRMSystemList>
</cpix:CPIX>
```

### 예제 - VOD HLS 콘텐츠

#### Request body

```xml
<?xml version="1.0" encoding="UTF-8"?>
<cpix:CPIX id="your-content-id" xmlns:cpix="urn:dashif:org:cpix" xmlns:pskc="urn:ietf:params:xml:ns:keyprov:pskc" xmlns:speke="urn:aws:amazon:com:speke">
 <cpix:ContentKeyList>
     <cpix:ContentKey kid="681e5b39-49f2-4dfa-b744-86573c22e6fb"></cpix:ContentKey>
 </cpix:ContentKeyList>
 <cpix:DRMSystemList>
    <!-- HLS SAMPLE-AES (FairPlay) -->
    <cpix:DRMSystem kid="681e5b39-49f2-4dfa-b744-86573c22e6fb" systemId="94CE86FB-07FF-4F43-ADB8-93D2FA968CA2" />
    <!-- HLS NCG AES-128 (systemId is implementation specific) -->
    <cpix:DRMSystem kid="98ee5596-cd3e-a20d-163a-e382420c6eff" systemId="81376844-f976-481e-a84e-cc25d39b0b33" />
 </cpix:DRMSystemList>
</cpix:CPIX>
```

#### Response

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<cpix:CPIX id="cpix-test-cid" xmlns:cpix="urn:dashif:org:cpix" xmlns:speke="urn:aws:amazon:com:speke" xmlns:pskc="urn:ietf:params:xml:ns:keyprov:pskc" >
    <cpix:ContentKeyList>
        <cpix:ContentKey explicitIV="MDEyMzQ1Njc4OWFiY2RlZg==" kid="12ea753c-23e7-bc02-4474-b2b976c43beb">
            <cpix:Data>
                <pskc:Secret>
                    <pskc:PlainValue>ODQ4REQwMTM5OURGNDczQQ==</pskc:PlainValue>
                </pskc:Secret>
            </cpix:Data>
        </cpix:ContentKey>
    </cpix:ContentKeyList>
    <cpix:DRMSystemList>
        <!-- HLS SAMPLE-AES (FairPlay) -->
        <cpix:DRMSystem kid="12ea753c-23e7-bc02-4474-b2b976c43beb" systemId="94CE86FB-07FF-4F43-ADB8-93D2FA968CA2">
            <cpix:HLSSignalingData>I0VYVC1YLUtFWTpN...DA1</cpix:HLSSignalingData><!-- V2에 추가됨 -->
            <cpix:HLSSignalingData playlist="master">I0VYVC1YLVNFU...DU=</cpix:HLSSignalingData><!-- V2에 추가됨 -->
            <cpix:URIExtXKey>c2tkOi8vRXVwMVBDUG52QUpFZExLNWRzUTc2dz09</cpix:URIExtXKey>
        </cpix:DRMSystem>
        <!-- HLS NCG AES-128 (systemId is implementation specific) -->
        <cpix:DRMSystem kid="12ea753c-23e7-bc02-4474-b2b976c43beb" systemId="81376844-f976-481e-a84e-cc25d39b0b33">
            <cpix:URIExtXKey>aHR0cHM6Ly90ZXN0dG9reW8ucGFsbHljb24uY29tL3JpL2hsc2tleVBhY2thZ2VyLmRvP3NpZD1ERU1PJmZpbGVuYW1lPWNwaXgtdGVzdC1jaWQmY2lkPWNwaXgtdGVzdC1jaWQ=</cpix:URIExtXKey>
        </cpix:DRMSystem>
    </cpix:DRMSystemList>
</cpix:CPIX>
```

### Flussonic Media Server 연동

`Flussonic Media Server`는 멀티 디바이스 클라이언트를 대상으로 비디오 캡쳐, 트랜스코딩, 녹화, 보안 및 전송까지 온라인 비디오 서비스를 구축할 수 있도록 해주는 서버 소프트웨어입니다. `Flussonic Media Server` 제품은 `PallyCon CPIX API`를 통해 PallyCon 멀티 DRM 서비스와 연동되어 있습니다.

- Flussonic Media Server 웹사이트 : https://flussonic.com/flussonic-media-server
- Flussonic - Pallycon 연동 가이드 : https://flussonic.com/doc/content-protection-with-drm/pallycon-drm 

## SPEKE API

Secure Packager and Encoder Key Exchange (SPEKE) API는 AWS Elemental에서 구현한 CPIX API로, AWS Elemental MediaPackage 또는 MediaConvert 제품과의 연동에 사용됩니다. 연동 URL 이외에 기본 규격은 PallyCon CPIX API와 동일하며, 상세한 연동 방법은 [해당 가이드](../aws-elemental)를 참고하시기 바랍니다.

### KMS 연동 URL 규격

SPEKE API 연동을 위한 KMS URL 규격은 다음과 같습니다. URL 마지막 부분의 `enc-token`은 PallyCon 서비스 가입 시 생성되는 API 인증 토큰으로, PallyCon 콘솔 사이트에서 확인할 수 있습니다.

```
https://kms.pallycon.com/cpix/getKey?enc-token={enc-token}
```

## Anevia CPIX API

Anevia의 NEA-DVR 솔루션과 연동을 위한 CPIX API입니다. 해당 API를 통하여 PallyCon과 사전 연동되어 Anevia 솔루션에 PallyCon 멀티 DRM을 쉽게 적용할 수 있습니다.

### 지원 기능

Anevia CPIX API를 통한 연동은 아래와 같은 콘텐츠와 기능을 지원합니다.

| | PlayReady | Widevine | FairPlay | AES-128 |
|:---:|:-----:|:-----:|:-----:|:-----:|
| DASH | <i class="fas fa-check"></i> | <i class="fas fa-check"></i> | | |
| HLS | | | <i class="fas fa-check"></i> | |

### KMS 연동 URL 규격

Anevia CPIX API 연동을 위한 KMS URL 규격은 다음과 같습니다. 아래 규격에 따라 생성된 URL을 Anevia 솔루션에서 `GET` 방식으로 호출하여 키를 발급 받습니다.

```
https://kms.pallycon.com/cpix/anevia/{streamingFormat}/{encToken}/{cid}
```

|Path|Description|
|-----|-----|
|streamFormat| dash/hls. streaming format type에 따라 지원하는 drm type으로 key를 발급합니다. |
|encToken| PallyCon 서비스 가입 시 생성되는 API 인증 토큰으로, PallyCon 콘솔 사이트에서 확인할 수 있습니다.|
|cid| 콘텐츠의 고유 ID, 최대 200 바이트 영숫자 |

### Anevia CPIX API 예제

#### 요청 URL 예제

```
https://kms.pallycon.com/cpix/anevia/dash/eyJzaXRlX2lkIjoiREVNTyIsICJhY2Nlc3Nfa2V5IjoiZHNJb2xjN2gxRzhUVW1JMTdiWXd4aFV1TkZvRmNlNzJjeDllTU9rNjJ3YjhWTjJQZGdwV1lISXhTRVg5ZjBIaSJ9/test-cid
```

#### 응답 데이터 예제

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<cpix:CPIX id="test-cid" xmlns:cpix="urn:dashif:org:cpix" xmlns:xenc="http://www.w3.org/2001/04/xmlenc#" xmlns:pskc="urn:ietf:params:xml:ns:keyprov:pskc" xmlns:ns3="urn:aws:amazon:com:speke" xmlns:ds="http://www.w3.org/2000/09/xmldsig#" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <cpix:ContentKeyList>
        <cpix:ContentKey explicitIV="MDEyMzQ1Njc4OWFiY2RlZg==" kid="cbc2eedd-4284-fd94-c17b-0d6fb56c146b">
            <cpix:Data>
                <pskc:Secret>
                    <pskc:PlainValue>dLlreLqTHdROTxGnw/3G7g==</pskc:PlainValue>
                </pskc:Secret>
            </cpix:Data>
        </cpix:ContentKey>
    </cpix:ContentKeyList>
    <cpix:DRMSystemList>
        <cpix:DRMSystem kid="cbc2eedd-4284-fd94-c17b-0d6fb56c146b" systemId="9A04F079-9840-4286-AB92-E65BE0885F95">
            <cpix:PSSH>AAACvnBzc2gAAAAAmgTweZhAQoarkuZb4IhflQAAAp6eAgAAAQABAJQCPABXAFIATQBIAEUAQQBEAEUAUgAgAHgAbQBsAG4AcwA9ACIAaAB0AHQAcAA6AC8ALwBzAGMAaABlAG0AYQBzAC4AbQBpAGMAcgBvAHMAbwBmAHQALgBjAG8AbQAvAEQAUgBNAC8AMgAwADAANwAvADAAMwAvAFAAbABhAHkAUgBlAGEAZAB5AEgAZQBhAGQAZQByACIAIAB2AGUAcgBzAGkAbwBuAD0AIgA0AC4AMAAuADAALgAwACIAPgA8AEQAQQBUAEEAPgA8AFAAUgBPAFQARQBDAFQASQBOAEYATwA+ADwASwBFAFkATABFAE4APgAxADYAPAAvAEsARQBZAEwARQBOAD4APABBAEwARwBJAEQAPgBBAEUAUwBDAFQAUgA8AC8AQQBMAEcASQBEAD4APAAvAFAAUgBPAFQARQBDAFQASQBOAEYATwA+ADwASwBJAEQAPgAzAGUANwBDAHkANABSAEMAbABQADMAQgBlAHcAMQB2AHQAVwB3AFUAYQB3AD0APQA8AC8ASwBJAEQAPgA8AEMASABFAEMASwBTAFUATQA+AFQAWABrAFkAcgAwAGQAdgBIAEgAYwA9ADwALwBDAEgARQBDAEsAUwBVAE0APgA8AEwAQQBfAFUAUgBMAD4AaAB0AHQAcABzADoALwAvAGwAaQBjAGUAbgBzAGUALgBwAGEAbABsAHkAYwBvAG4ALgBjAG8AbQAvAHIAaQAvAHAAbABhAHkAcgBlAGEAZAB5AC8AbABpAGMAZQBuAHMAZQBNAGEAbgBhAGcAZQByAC4AZABvADwALwBMAEEAXwBVAFIATAA+ADwALwBEAEEAVABBAD4APAAvAFcAUgBNAEgARQBBAEQARQBSAD4A</cpix:PSSH>
        </cpix:DRMSystem>
        <cpix:DRMSystem kid="cbc2eedd-4284-fd94-c17b-0d6fb56c146b" systemId="EDEF8BA9-79D6-4ACE-A3C8-27DCD51D21ED">
            <cpix:PSSH>AAAAUHBzc2gAAAAA7e+LqXnWSs6jyCfc1R0h7QAAADAIARIQy8Lu3UKE/ZTBew1vtWwUaxoMaW5rYWVudHdvcmtzIgh0ZXN0LWNpZCoCSEQ=</cpix:PSSH>
        </cpix:DRMSystem>
    </cpix:DRMSystemList>
</cpix:CPIX>
```
