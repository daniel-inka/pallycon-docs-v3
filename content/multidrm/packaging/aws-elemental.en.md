---
linktitle: AWS Elemental Integration
weight: 4

# Page metadata.
title: AWS Elemental Integration
summary: This guide explains how to integrate with MediaConvert or MediaPackage service.
date: "2018-09-09T00:00:00Z"
lastmod: "2020-11-03T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: true  # Show table of contents? true/false
type: book  # Do not modify.

menu:
  multidrm:
    weight: 4
    parent: Content Packaging
---

## Overview

PallyCon KMS supports [SPEKE](https://docs.aws.amazon.com/en_US/speke/latest/documentation/what-is-speke.html) (Secure Packager and Encoder Key Exchange), which issues the keys required for Multi DRM packaging in AWS Elemental MediaConvert and MediaPackage.

PallyCon KMS URL may be set to the URL of DRM encryption setting of AWS Elemental, then the link is completed easily. This guide explains how to integrate with MediaConvert or MediaPackage service.

## Tutorial Video

This video is a tutorial for enabling DRM encryption on MediaConvert and playing the DASH VOD DRM content.

{{< youtube SZwphL0ryYY >}}

> For optimal playback, select '1080p' as the video quality and enable subtitle (Korean or English) before starting playback.

## MediaConvert integration {#mediaconvert}

### Create MediaConvert IAM role {#mediaconvert-iam}

Please refer to [IAM Settings AWS Guide Document](https://docs.aws.amazon.com/mediaconvert/latest/ug/iam-role.html) and proceed as follows.

1. In the AWS Console, select the IAM service.
2. Click the `Roles` tab and select `create role`.
3. Select `MediaConvert` and click the `Next: permission` button.
4. Confirm the S3 Access and APIGateway access permissions and click `Next: Review` button.
![iam1](/docs/images/iam-1.png)

5. Set RoleName to `MediaConvert-role` and click the `create role` button.
![iam2](/docs/images/iam-2.png)

### Create MediaConvert job and set IAM role {#mediaconvert-iam-set}

1. In the AWS Console, select the MediaConvert service.
2. Click the `create job` button on the Jobs tab to start job creation.
3. Select the `MediaConvert-role` created in the previous step in the IAM role setting section of the Job settings screen.
![mediaconvert0](/docs/images/mediaconvert-0.png)

### Set MediaConvert Input {#mediaconvert-input}

1. In the Input field, enter the content path to be packaged in s3.
![mediaconvert1](/docs/images/mediaconvert-1.png)

### Set MediaConvert Output groups {#mediaconvert-outputgroups}

1. Add the ouptput to the output groups by pressing the Add button. (Dash ISO for PlayReady and Widevine, Apple HLS for FairPlay)
![mediaconvert2](/docs/images/mediaconvert-2.png)

2. In Custom group name, enter a name that is easy for you to identify.

3. In the Destination field, type the path on s3 that contains the package-completed file.
![mediaconvert3](/docs/images/mediaconvert-3.png)

4. Select the DRM encryption option, and then enter the Resource ID, System ID, and URL.
	- Resource ID: It is a value corresponding to the content ID (CID) in the integration specification in [Multi DRM License Integration Guide]({{%ref "multidrm-license.en.md"%}}).
	- System ID: The DRM-specific system id value specified in [Dash System ID](http://dashif.org/identifiers/content_protection). You need to set PlayReady and Widevine ID for DASH output(as shown below) and set FairPlay ID for HLS output.
		- PlayReady: 9A04F079-9840-4286-AB92-E65BE0885F95
		- Widevine: EDEF8BA9-79D6-4ACE-A3C8-27DCD51D21ED
		- FairPlay: 94CE86FB-07FF-4F43-ADB8-93D2FA968CA2
	- Key Provider URL: Enter the following KMS URL. The `enc-token` at the end of the URL is an API authentication token that is generated when you sign up PallyCon service, and can be found on the [PallyCon Console](https://console.pallycon.com) site.
		```
		https://kms.pallycon.com/cpix/getKey?enc-token={enc-token}
		```
	- Certificate ARN: leave it blank
	- Play device compatibility: CENC v1

	![mediaconvert4](/docs/images/mediaconvert-4.png)

5. Set the Outputs and click the Create button.
	- In case of widevine, it is mandatory to create the video and audio track separately because there are clients that can not play if you do not divide video and audio tracks into output. (click 'add output' button to add track)

	![mediaconvert5](/docs/images/mediaconvert-5.png)
	![mediaconvert6](/docs/images/mediaconvert-6.png)

6. Make public or set permission on the S3 storage to play the generated file stored on it.

### Output group configuration for Apple HLS {#applehls}

If you want to support Apple devices as well as others, you need to create both 'DASH ISO' and 'Apple HLS' output groups for a single input. Please set the below DRM encryption parameters in Apple HLS group.

- Encryption method: Sample AES
- Key provider type: SPEKE
- Resource ID: the same content ID as DASH output
- System ID: DRM system ID for FairPlay (94CE86FB-07FF-4F43-ADB8-93D2FA968CA2)
- Key provider URL: same as DASH output (PallyCon KMS URL with enc token)
- the other items: leave them as default

### Notes on CMAF Packaging {#notes-on-cmaf}

In addition to `DASH-ISO` and `Apple HLS`, `CMAF`(Common Media Application Format) type output can also be generated through SPEKE integration.

However, at this time, Apple devices only support `AES CBC encryption` and Windows(Edge, IE browser) only support `AES CTR encryption`, so it is not yet possible to support all platforms with one CMAF content.

## MediaPackage integration {#mediapackage}

Content can be encrypted in real time in conjunction with services such as AWS MediaLive which can upload HLS.

> Note: In order to perform DRM packaging in MediaPackage, you must turn off Encryption in the Output Group of MediaLive.

### Create MediaPackage IAM role {#mediapackage-iam}

1. Create the same as [MediaConvert IAM Authorization](#mediaconvert-iam), and create only Role Name with SPEKEAccess.

2. On the Roles tab, select SPEKEAccess role and click the Edit trust relationship button on the Trust relationships tab.
![iam3](/docs/images/iam-3.png)

3. Change the value of Principal.Service to mediapackage.amazonaws.com and click the Update button.
 ![iam4](/docs/images/iam-4.png)

### Create MediaPackage Channel {#mediapackage-channel}

1. In the AWS Console, select the MediaPackage service.

2. Create a channel.
 ![mediapackage0](/docs/images/mediapackage-0.png)

3. At the endpoints, press the Add button to set the endpoint.

4. Set the endpoint name, packager settings, etc. according to the desired content specification.

5. Configure Encryption and Outputs in the same way as [MediaConvert Output groups setting no. 4](#mediaconvert-outputgroups).

6. Enter the SPEKEAccess Role created in Role ARN.

7. Click the Save button.
![mediapackage1](/docs/images/mediapackage-1.png)

### Support for key rotation

- Key rotation via MediaPackage is currently not supported. Therefore, you must uncheck the `Key rotation` of `Additional Configuration` in the Encryption option of a MediaPackage Endpoint.
