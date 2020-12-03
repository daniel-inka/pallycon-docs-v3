---
title: Watermarking Release Notes
linktitle: Watermarking Release Notes
summary: Release notes for PallyCon Forensic Watermarking products
toc: true
type: book
date: "2020-05-05T00:00:00+01:00"
lastmod: "2020-11-11T00:00:00Z"
draft: false
menu:
  multidrm:
    parent: Forensic Watermarking
    weight: 100
    name: Watermarking Release Notes

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 100
---

## Server Products

### Watermark Packaging Server

|Date |Version |Descriptions |
|:---|:---|:---|
| 2020.11.05 | 1.3.2 |- Fixed issue with non-DRM packaging|
| 2020.11.03 | 1.3.1 |- Fixed issue with checking codec for trial account|
| 2020.09.16 | 1.3.0 |- Added DASH related packaging options <br>- Added multi-key related options for DRM packaging<br>- Optimized watermark preprocessing logic to reduce pixelation|
| 2020.09.08 | 1.2.1 |- The minimum length limit for packaging video is changed from 12 minutes to 5 minutes.<br>- Supports packaging of adaptive contents with separate audio tracks|
| 2020.08.19 | 1.2.0 |- HEVC packaging support, correction of case processing error of subtitle language code|
| 2020.07.29 | 1.1.1 |- Updated the length limit of the input file path (up to 512 bytes supported)
| 2020.06.10 | 1.1.0 |- Added support for Akamai CDN integration|
| 2019.01.15 | 1.0.0 |- SaaS Packaging Server 1.0 release|

### Watermark Packaging API

|Date |Version |Descriptions |
|:---|:---|:---|
| 2020.10.27 | 2.3.1 |- Fixed issue about trial check |
| 2020.09.16 | 2.3.0 |- Added DASH related packaging options <br>- Added multi-key related options for DRM packaging<br>- Changed packaging 'Start Date' to the start of input content downloading |
| 2020.08.19 | 2.2.0 |- Added prevention of storage removal|
| 2020.06.10 | 2.1.0 |- Added support for Akamai CDN integration|
| 2020.05.12 | 2.0.0 |- Updated to V2 API |

### Watermark Session API

|Date |Version |Descriptions |
|:---|:---|:---|
| 2020.12.01 | 1.2.0 |- Fixed the API response delay issue|
| 2020.09.15 | 1.1.1 |- Added API call limit for trial account (max 1,000 calls)<br>- Added limit for watermark string length (max 254 bytes) |
| 2020.05.13 | 1.1.0 |- Added SessionUrl API for Akamai CDN |
| 2019.12.26 | 1.0.0 |- Initial release of Session API |

## Client and Other Products

### Lambda@Edge Module for CloudFront 

|Date |Version |Descriptions |
|:---|:---|:---|
| 2020.11.10 | 2.2.0 |- Added support for AWS MediaConvert<br>- Moved siteKey, integration type configuration to config.json |
| 2020.09.16 | 2.1.0 |- Added support for generate_tracktype_manifests option. |
| 2020.03.26 | 2.0.1 |- Changed the number of ts per GOP. |
| 2019.08.22 | 2.0.0 |- Updated for Shaka Packager integration. |
| 2018.01.27 | 1.0.0 |- Initial release of Lambda@Edge module |

### CLI Preprocessor

|Date |Version |Descriptions |
|:---|:---|:---|
| 2020.09.09 | 2.0.3 |- Optimized watermark strength for better invisibility|
| 2020.07.24 | 2.0.2 |- Extended input filename length from 256 to 512 |
| 2020.01.30 | 2.0.0 |- Updated for shorter video length required to detect watermark. (12 mins -> 5 mins)<br>- Optimized watermark strength for invisibility and robustness.|
| 2019.04.11 | 1.3.1 |- Added support for HDR 10 hardware decoding.<br>- Shows 'DEMO' in the video when a trial key is used. |
| 2019.03.11 | 1.3.0 |- Added support for UHD resolution, HEVC codec, and HDR.<br>- Added support for multi-thread decoding. |
| 2018.07.16 | 1.0.0 |- Initial release of CLI preprocessor |

### Visible Watermark SDK

|Date |Version |Descriptions |
|:---|:---|:---|
| 2020.09.29 | 1.2.0 |- Added 'margin' to positiontype option. Watermark position can be set with fixed value.|
| 2020.09.08 | 1.1.0 |- Added siteId parameter for authentication.<br>- Added support for linebreak in watermark text. Removed the text length limit. |
| 2020.06.02 | 1.0.0 |- Initial release of Visible Watermark SDK|

***