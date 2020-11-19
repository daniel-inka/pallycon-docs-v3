---
linktitle: CPIX API Guide
weight: 3

# Page metadata.
title: CPIX API Guide
summary: With this API, PallyCon Multi DRM can be easily linked with encoder/transcoder solution supporting CPIX based key exchange.
date: "2018-09-09T00:00:00Z"
lastmod: "2020-06-26T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: true  # Show table of contents? true/false
type: book  # Do not modify.

menu:
  multidrm:
    weight: 3
    parent: Content Packaging
---

## Overview

The CPIX API is an API implemented in accordance with the [Content Protection Information Exchange Format (CPIX) Standard Guide](https://dashif.org/docs/CPIX2.1/HTML/Index.html) as defined by the DASH Industry Forum. It defines specifications that link the keys required to apply multi-DRM in the process of packaging media contents. With this API, PallyCon Multi DRM can be easily linked with encoder/transcoder solution supporting CPIX based key exchange.

## CPIX API Types

PallyCon Multi DRM can be integrated with the following types of CPIX API depending on the implementation subject of API.

- `PallyCon CPIX API`: The CPIX API implemented by PallyCon, allowing encoder/transcoder solution vendors to work with PallyCon Multi DRM with the corresponding API. `Flussonic Media Server` is integrated using `PallyCon CPIX API`.

- `SPEKE API`: The CPIX API implemented by AWS Elemental, which is pre-integrated with PallyCon to easily apply PallyCon Multi DRM to MediaPackage or MediaConvert products of AWS Elemental Media Service. For more information about SPEKE API integration, please refer to [the guide](../aws-elemental).

- `Anevia CPIX API`: The CPIX API implemented by Anevia. It can be easily applied to PallyCon Multi DRM with Anevia NEA-DVR solution.

## PallyCon CPIX API

This API is implemented by PallyCon according to the CPIX implementation guide of DASH-IF.

> Please refer to the following guide for details of CPIX specification.
> https://dashif.org/docs/CPIX2.1/HTML/Index.html

### Supported Features

PallyCon CPIX API supports the following content types and functions.

| | PlayReady | Widevine | FairPlay | AES-128 |
|:-----:|:-----:|:-----:|:-----:|:-----:|
| DASH | <i class="fas fa-check"></i> (key rotation supported)| <i class="fas fa-check"></i> (key rotation supported) | | |
| HLS | | | <i class="fas fa-check"></i> (key rotation supported) | <i class="fa fa-check"></i> (NCG DRM supported)|

### KMS URL Specification

PallyCon CPIX API provides the following KMS URL specifications. The `enc-token` at the end of the URL is an API authentication token that is generated when you sign up PallyCon service, and can be found on the PallyCon Console site.

```
KMS URL V1 - https://kms.pallycon.com/v1/cpix/pallycon/getKey/{enc-token}
KMS URL V2 - https://kms.pallycon.com/v2/cpix/pallycon/getKey/{enc-token}
```

> Encoder/transcoder solution calls the above URL to get the key needed for DRM encryption. Add the CPIX data as shown in the following example to the body part of the request data, and call it by `POST` method.

### KMS Specification V2 and Multi-key Packaging

Request/response data for multi-key packaging has been added to the V2 specification. Multi-key packaging encrypts video tracks by resolution with different keys, and is required for setting the security level for each resolution by Hollywood Studio requirement.

For multi-key packaging, the following items should be added to the corresponding XML request data.

> If you want to package with single key, you may remove the below items from the example request XML.

#### IntendedTrackType

Sets the track type (SD, HD, etc) to be encrypted with the kid in `cpix:ContentKeyUsageRule` tag.
The values that can be entered and the default resolution are as follows.

- AUDIO: for audio track
- SD: 576p or less
- HD: 720p ~ 1080p
- UHD1: 4K
- UHD2: 8K

#### VideoFilter

You can set the min/max pixels of the video track using `VideoFilter` tag and `minPixels` / `maxPixels` parameters.

### Content ID Specification

When packaging content with PallyCon CPIX API, you must enter a unique content ID(CID) managed by the CMS of the content service. The CID is registered with PallyCon license server and used to request and issue DRM licenses on  content playback.

Enter the content ID (max 200 alphanumeric characters) in the `cpix:CPIX` tag of the request body(XML) for each content type as below.

```xml
<cpix:CPIX id="your-content-id"
```

### Example for DASH live content

> For live content, the API responds with a key value that is rotated according to the ContentKeyPeriodList index value.

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
            <cpix:ContentKeyUsageRule intendedTrackType="HD" kid="ca65e643-482c-4a21-a204-05570a1e276c"><!-- Added to V2: intendedTrackType -->
                <cpix:KeyPeriodFilter periodId="keyPeriod_e7c4423c-a2c6-46f4-bef3-2bfed6bae9c9"/>
                <cpix:VideoFilter maxPixels="2073600" minPixels="921600"/><!-- Added to V2 -->
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
        <cpix:ContentKeyUsageRule intendedTrackType="HD" kid="6bac8ec3-0ee3-379f-3d21-290b3a2bc9c3"><!-- Added to V2 : intendedTrackType -->
            <cpix:KeyPeriodFilter periodId="keyPeriod_e7c4423c-a2c6-46f4-bef3-2bfed6bae9c9"/>
            <cpix:VideoFilter maxPixels="2073600" minPixels="921600"/><!-- Added to V2 -->
        </cpix:ContentKeyUsageRule>
    </cpix:ContentKeyUsageRuleList>
    <cpix:DRMSystemList>
        <cpix:DRMSystem kid="6bac8ec3-0ee3-379f-3d21-290b3a2bc9c3" systemId="9A04F079-9840-4286-AB92-E65BE0885F95">
            <cpix:ContentProtectionData>qAUAAAEAAQCeB...A=</cpix:ContentProtectionData><!-- Added to V2 -->
            <speke:ProtectionHeader>qAUAAAEAAQCeB...A=</speke:ProtectionHeader><!-- Added to V2 -->
            <cpix:PSSH>AAADTHBzc2gAAAAAmgTweZhAQoarkuZb4IhflQAAAywsAwAAAQABACIDPABXAFIATQBIAEUAQQBEAEUAUgAgAHgAbQBsAG4AcwA9ACIAaAB0AHQAcAA6AC8ALwBzAGMAaABlAG0AYQBzAC4AbQBpAGMAcgBvAHMAbwBmAHQALgBjAG8AbQAvAEQAUgBNAC8AMgAwADAANwAvADAAMwAvAFAAbABhAHkAUgBlAGEAZAB5AEgAZQBhAGQAZQByACIAIAB2AGUAcgBzAGkAbwBuAD0AIgA0AC4AMAAuADAALgAwACIAPgA8AEQAQQBUAEEAPgA8AFAAUgBPAFQARQBDAFQASQBOAEYATwA+ADwASwBFAFkATABFAE4APgAxADYAPAAvAEsARQBZAEwARQBOAD4APABBAEwARwBJAEQAPgBBAEUAUwBDAFQAUgA8AC8AQQBMAEcASQBEAD4APAAvAFAAUgBPAFQARQBDAFQASQBOAEYATwA+ADwASwBJAEQAPgB3ADQANgBzAGEAKwBNAE8AbgB6AGMAOQBJAFMAawBMAE8AaQB2AEoAdwB3AD0APQA8AC8ASwBJAEQAPgA8AEMASABFAEMASwBTAFUATQA+ADYAaQBiAFAANAAwAHUAVABPAFoAMAA9ADwALwBDAEgARQBDAEsAUwBVAE0APgA8AEwAQQBfAFUAUgBMAD4AaAB0AHQAcABzADoALwAvAHQAZQBzAHQAdABvAGsAeQBvAC4AcABhAGwAbAB5AGMAbwBuAC4AYwBvAG0ALwByAGkALwBwAGwAYQB5AHIAZQBhAGQAeQAvAGwAaQBjAGUAbgBzAGUATQBhAG4AYQBnAGUAcgAuAGQAbwA8AC8ATABBAF8AVQBSAEwAPgA8AEMAVQBTAFQATwBNAEEAVABUAFIASQBCAFUAVABFAFMAPgA8AFAARQBSAEkATwBEAF8ASQBOAEQARQBYAD4AMQAyADgAPAAvAFAARQBSAEkATwBEAF8ASQBOAEQARQBYAD4APAAvAEMAVQBTAFQATwBNAEEAVABUAFIASQBCAFUAVABFAFMAPgA8AC8ARABBAFQAQQA+ADwALwBXAFIATQBIAEUAQQBEAEUAUgA+AA==</cpix:PSSH>
        </cpix:DRMSystem>
        <cpix:DRMSystem kid="6bac8ec3-0ee3-379f-3d21-290b3a2bc9c3" systemId="EDEF8BA9-79D6-4ACE-A3C8-27DCD51D21ED">
            <cpix:PSSH>AAAAaHBzc2gAAAAA7e+LqXnWSs6jyCfc1R0h7QAAAEgIARIQa6yOww7jN589ISkLOivJwxoMaW5rYWVudHdvcmtzIh1jcGl4LXRlc3QtY2lkMDAwMDAwMDAwMDAwMDA4MCoCSEQ4gAE=</cpix:PSSH>
        </cpix:DRMSystem>
    </cpix:DRMSystemList>
</cpix:CPIX>
```

### Example for HLS live content

#### Request body

```xml
<?xml version="1.0" encoding="UTF-8"?>
<cpix:CPIX id="your-content-id" xmlns:cpix="urn:dashif:org:cpix" xmlns:pskc="urn:ietf:params:xml:ns:keyprov:pskc" xmlns:speke="urn:aws:amazon:com:speke">
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
        <cpix:ContentKeyUsageRule intendedTrackType="HD" kid="6bac8ec3-0ee3-379f-3d21-290b3a2bc9c3"><!-- Added to V2: intendedTrackType -->
            <cpix:KeyPeriodFilter periodId="keyPeriod_e7c4423c-a2c6-46f4-bef3-2bfed6bae9c9"/>
            <cpix:VideoFilter maxPixels="2073600" minPixels="921600"/><!-- Added to V2 -->
        </cpix:ContentKeyUsageRule>
    </cpix:ContentKeyUsageRuleList>
    <cpix:DRMSystemList>
        <!-- HLS SAMPLE-AES (FairPlay) -->
        <cpix:DRMSystem kid="6bac8ec3-0ee3-379f-3d21-290b3a2bc9c3" systemId="94CE86FB-07FF-4F43-ADB8-93D2FA968CA2">
            <cpix:HLSSignalingData>I0VYVC1YLUtFWTpN...DA1</cpix:HLSSignalingData><!-- Added to V2 -->
            <cpix:HLSSignalingData playlist="master">I0VYVC1YLVNFU...DU=</cpix:HLSSignalingData><!-- Added to V2 -->
            <cpix:URIExtXKey>c2tkOi8vY3BpeC10ZXN0LWNpZDAwMDAwMDAwMDAwMDAwODA=</cpix:URIExtXKey>
        </cpix:DRMSystem>
    </cpix:DRMSystemList>
</cpix:CPIX>
```

### Example for DASH VOD content

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
            <cpix:ContentProtectionData>qAUAAAEAAQCeB...A=</cpix:ContentProtectionData><!-- Added to V2 -->
            <speke:ProtectionHeader>qAUAAAEAAQCeB...A=</speke:ProtectionHeader><!-- Added to V2 -->
           <cpix:PSSH>AAACwnBzc2gAAAAAmgTweZhAQoarkuZb4IhflQAAAqKiAgAAAQABAJgCPABXAFIATQBIAEUAQQBEAEUAUgAgAHgAbQBsAG4AcwA9ACIAaAB0AHQAcAA6AC8ALwBzAGMAaABlAG0AYQBzAC4AbQBpAGMAcgBvAHMAbwBmAHQALgBjAG8AbQAvAEQAUgBNAC8AMgAwADAANwAvADAAMwAvAFAAbABhAHkAUgBlAGEAZAB5AEgAZQBhAGQAZQByACIAIAB2AGUAcgBzAGkAbwBuAD0AIgA0AC4AMAAuADAALgAwACIAPgA8AEQAQQBUAEEAPgA8AFAAUgBPAFQARQBDAFQASQBOAEYATwA+ADwASwBFAFkATABFAE4APgAxADYAPAAvAEsARQBZAEwARQBOAD4APABBAEwARwBJAEQAPgBBAEUAUwBDAFQAUgA8AC8AQQBMAEcASQBEAD4APAAvAFAAUgBPAFQARQBDAFQASQBOAEYATwA+ADwASwBJAEQAPgBQAEgAWABxAEUAdQBjAGoAQQByAHgARQBkAEwASwA1AGQAcwBRADcANgB3AD0APQA8AC8ASwBJAEQAPgA8AEMASABFAEMASwBTAFUATQA+AC8ARgA3AEIAMQBmAEgAMgBlADAAYwA9ADwALwBDAEgARQBDAEsAUwBVAE0APgA8AEwAQQBfAFUAUgBMAD4AaAB0AHQAcABzADoALwAvAHQAZQBzAHQAdABvAGsAeQBvAC4AcABhAGwAbAB5AGMAbwBuAC4AYwBvAG0ALwByAGkALwBwAGwAYQB5AHIAZQBhAGQAeQAvAGwAaQBjAGUAbgBzAGUATQBhAG4AYQBnAGUAcgAuAGQAbwA8AC8ATABBAF8AVQBSAEwAPgA8AC8ARABBAFQAQQA+ADwALwBXAFIATQBIAEUAQQBEAEUAUgA+AA==</cpix:PSSH>
       </cpix:DRMSystem>
       <!-- Common encryption (Widevine)-->
       <cpix:DRMSystem kid="12ea753c-23e7-bc02-4474-b2b976c43beb" systemId="EDEF8BA9-79D6-4ACE-A3C8-27DCD51D21ED">
           <cpix:PSSH>AAAAVXBzc2gAAAAA7e+LqXnWSs6jyCfc1R0h7QAAADUIARIQEup1PCPnvAJEdLK5dsQ76xoMaW5rYWVudHdvcmtzIg1jcGl4LXRlc3QtY2lkKgJIRA==</cpix:PSSH>
       </cpix:DRMSystem>
   </cpix:DRMSystemList>
</cpix:CPIX>
```

### Example for HLS VOD content

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
            <cpix:HLSSignalingData>I0VYVC1YLUtFWTpN...DA1</cpix:HLSSignalingData><!-- Added to V2  -->
            <cpix:HLSSignalingData playlist="master">I0VYVC1YLVNFU...DU=</cpix:HLSSignalingData><!-- Added to V2 -->
            <cpix:URIExtXKey>c2tkOi8vRXVwMVBDUG52QUpFZExLNWRzUTc2dz09</cpix:URIExtXKey>
        </cpix:DRMSystem>
        <!-- HLS NCG AES-128 (systemId is implementation specific) -->
        <cpix:DRMSystem kid="12ea753c-23e7-bc02-4474-b2b976c43beb" systemId="81376844-f976-481e-a84e-cc25d39b0b33">
            <cpix:URIExtXKey>aHR0cHM6Ly90ZXN0dG9reW8ucGFsbHljb24uY29tL3JpL2hsc2tleVBhY2thZ2VyLmRvP3NpZD1ERU1PJmZpbGVuYW1lPWNwaXgtdGVzdC1jaWQmY2lkPWNwaXgtdGVzdC1jaWQ=</cpix:URIExtXKey>
        </cpix:DRMSystem>
    </cpix:DRMSystemList>
</cpix:CPIX>
```

### Flussonic Media Server Integration

`Flussonic Media Server` is a server software that helps you capture, transcode, record, protect, and deliver video to users across multiple devices. `Flussonic Media Server` is integrated with PallyCon Multi-DRM service using `PallyCon CPIX API`.

- Flussonic Media Server Website : https://flussonic.com/flussonic-media-server
- Flussonic - Pallycon integration guide : https://flussonic.com/doc/content-protection-with-drm/pallycon-drm 

## SPEKE API

Secure Packager and Encoder Key Exchange (SPEKE) API is the CPIX API implemented by AWS Elemental and is used to interface with AWS Elemental MediaPackage or MediaConvert products. The basic specification is the same as the PallyCon CPIX API except the KMS URL. For details on SPEKE integration, please refer to [the guide](../aws-elemental).

### KMS URL Specification

KMS URL specification for SPEKE API is as follows. The `enc-token` at the end of the URL is an API authentication token that is generated when you sign up PallyCon service, and can be found on the PallyCon Console site.

```
https://kms.pallycon.com/cpix/getKey?enc-token={enc-token}
```

## Anevia CPIX API

CPIX API for integration with Anevia's NEA-DVR solution. Through this API, PallyCon multi-DRM can be applied easily to Anevia solution.

### Supported Features

Multi-DRM integration via Anevia CPIX API supports the following content types and functions.

| | PlayReady | Widevine | FairPlay | AES-128 |
|:---:|:-----:|:-----:|:-----:|:-----:|
| DASH | <i class="fas fa-check"></i> | <i class="fas fa-check"></i> | | |
| HLS | | | <i class="fas fa-check"></i> | |

### KMS URL Specification

The KMS URL specification for Anevia CPIX API is as follows. The URL generated according to the following specifications is called by the Anevia solution in `GET` method to get the key.

```
https://kms.pallycon.com/cpix/anevia/{streamingFormat}/{encToken}/{cid}
```

|Path|Description|
|-----|-----|
|streamFormat| dash or hls. The key is issued with the drm type supported according to the streaming format type. |
|encToken| The API authentication token that is created when you sign up for the PallyCon service. You can find it on PallyCon Console site. |
|cid| Content ID |

### Anevia CPIX API Example

#### Request URL Example

```
https://kms.pallycon.com/cpix/anevia/dash/eyJzaXRlX2lkIjoiREVNTyIsICJhY2Nlc3Nfa2V5IjoiZHNJb2xjN2gxRzhUVW1JMTdiWXd4aFV1TkZvRmNlNzJjeDllTU9rNjJ3YjhWTjJQZGdwV1lISXhTRVg5ZjBIaSJ9/test-cid
```

#### Response Data Example

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
