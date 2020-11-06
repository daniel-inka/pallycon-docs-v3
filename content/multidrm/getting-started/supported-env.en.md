---
title: Supported Environments
linktitle: Supported Environments
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  multidrm:
    parent: Getting Started
    weight: 3

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 3
---

PallyCon Multi-DRM product supports the following client platforms and operating systems:

## PC platforms

|OS |Web browser or SDK |Streaming Type |DRM |Download Scenario |Remarks |
|:---|:---|:---|:---|:---|:---|
|Windows 8.1 or later |IE11, Edge |MPEG-DASH |PlayReady |Not supported | |
|Windows 7.0 or later, Mac OS |Chrome, FireFox |MPEG-DASH |Widevine Modular |Not supported | |
|Windows 7.0 or later |NCG Windows SDK |HLS or Progressive Download |Netsync Content Guard |Supported |File decryption only |
|Windows 10 | PallyCon PlayReady UWP SDK | MPEG-DASH | PlayReady | Supported | does not support offline |
|Mac OS 10.10 or later |Safari 8 or later |HLS |FairPlay Streaming |Not supported |. |

## Mobile platforms

|OS |Web browser or SDK |Streaming Type |DRM |Download Scenario |Remarks |
|:---|:---|:---|:---|:---|:---|
|Android 4.4 or later |PallyCon Widevine SDK |MPEG-DASH |Widevine Modular |Supported from Android 5.0 |Google-approved devices only |
|Android 4.0 or later |NCG Android SDK |HLS or Progressive Download |Netsync Content Guard |Supported |Non google-approved devices can be supported |
|Android 4.4 or later |Chrome v57 or later |MPEG-DASH | Widevine Modular | can support using PWA |Widevine L1 needs Chrome v62 or later |
|iOS 9.0 or later |PallyCon FPS SDK for iOS |HLS |FairPlay Streaming |Supported from iOS 10.0 | |
|iOS 9.0 or later |NCG iOS SDK |HLS or Progressive Download |Netsync Content Guard |Supported |. |
|iOS 11.2 or later | Safari v11.2(same as iOS version) or later |HLS | FairPlay Streaming | Not supported |. |

## OTT platforms

|OS / Platform |Client SDK |Streaming Type |DRM |Download Scenario |Remarks |
|:---|:---|:---|:---|:---|:---|
|Android TV 4.4 or later |PallyCon Widevine SDK |MPEG-DASH |Widevine Modular |Supported from Android 5.0 |Google-approved devices only |
|Android TV 4.0 or later |NCG Android SDK |HLS or Progressive |Netsync Content Guard |Supported |Non google-approved devices can be supported |
|Amazon Fire TV |PallyCon Widevine SDK |MPEG-DASH |Widevine Modular | Not supported | |
|tvOS 10.0 or later (AppleTV 4th-Gen) |PallyCon FPS SDK for tvOS |HLS |FairPlay Streaming |Not supported | |
|Samsung Smart TV (Tizen OS 2.3 or later) | Native DRM integration | MPEG-DASH | PlayReady |Not supported | |
|Samsung Smart TV (Tizen OS 3.0 or later) | Native DRM integration | MPEG-DASH | Widevine Modular |Not supported | |
|LG Smart TV (webOS 3.0 or later) | Native DRM integration | MPEG-DASH | PlayReady |Not supported | |
|Roku OS 8.0 or later | PallyCon PlayReady Roku SDK | MPEG-DASH | PlayReady | Not supported | |
|Xbox One | PallyCon PlayReady UWP SDK | MPEG-DASH | PlayReady | Not supported | |
|ChromeCast | Custom receiver and sender samples | MPEG-DASH | PlayReady or Widevine Modular | Not supported | . |

## CMAF (Common Media Application Format) Single Content Support

|OS |Browser or SDK |Streaming Type |DRM |
|:---|:---|:---|:---|
|Android 7.1 or later |PallyCon Widevine Android SDK |DASH or HLS |Widevine Modular |
|Android TV 7.1 or later |PallyCon Widevine AndroidTV SDK |DASH or HLS |Widevine Modular |
|iOS 10.0 or later |Safari V10 or later, PallyCon FPS iOS SDK |HLS |FairPlay Streaming |
|tvOS 10.0 or later |Safari V10 or later, PallyCon FPS tvOS SDK |HLS |FairPlay Streaming |
|Windows 7.0, macOS 10.10 or later | Chrome 68 or later |DASH or HLS | Widevine Modular |
|macOS 10.12 or later | Safari V10 or later |HLS | FairPlay Streaming |

## HTTPS Requirement

In accordance with the security policy of web browsers such as Google Chrome, all URLs related to content services, such as streaming URLs, should be applied HTTPS by default. (ex: DASH mpd URL, HLS m3u8 URL, NCG file URL)

***