---
title: License Error Code
linktitle: License Error Code
summary: This document provides a list of error code about DRM license integration.
toc: true
type: book
date: "2020-05-05T00:00:00+01:00"
lastmod: "2020-11-11T00:00:00Z"
draft: false
menu:
  multidrm:
    parent: License Issuance
    weight: 40
    name: License Error Code

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 40
---

This document provides a list of error code about DRM license integration.

## Error Code List {#error-code}

|<div style="width:70px">Error Code</div> |Error Message |
|---|---|
|1002 |Trial plan’s license limit or user limit exceeded. |
|1003 |Cannot find the Site ID. |
|1004 |The Site ID's service is stopped. |
|1005 |Trial plan period ended. |
|1006 |Failed to decrypt SITE KEY. |
|2001 |DB connection failed. |
|2701 |Failed to write license issuance record on DB. |
|2702 |Failed to write unsuccessful license record on DB. |
|7001 |Cannot find package info. |
|7002 |Failed to create data for Content Usage Info URL. |
|7006 |Failed to decrypt KEY on license request. |
|7007 |Failed to parse pallycon-customdata. |
|7008 |Failed to decrypt pallycon-customdata. |
|7009 |Failed to parse token. |
|7010 |Cannot find token data. |
|7011 |Token is expired. |
|7012 |Does not match with token's CID. |
|7013 |Invalid JSON data in token. |
|7016 |Invalid token. |
|7017 |Failed to save token use history. |
|7018 |Invalid DRM type in token. |
|7019 |The hash values do not match.|
|7021 |The value of Drm type of not NCG.|
|7101 |Failed to create Widevine license request data. |
|7102 |Failed to create Widevine license. |
|7103 |Failed to communicate with Widevine license server. |
|7104 |This Site ID does not have Widevine service. |
|7105 |Cannot find Widevine Device ID. |
|7106 |Failed to get Widevine service cert. |
|7107 |Invalid Widevine license challenge data. |
|7110 |DRM device certificate revoked from Widevine DRM Proxy (Widevine Proxy internal_status : 127)|
|7111 |Widevine proxy response parsing fail |
|7112 |Widevine proxy response : invalid widevine pssh data (Widevine Proxy internal_status : 152) |
|7113 |Failed to get Widevine server certificate file.|
|7201 |Failed to create PlayReady license. |
|7202 |Failed to communicate with PlayReady license server. |
|7203 |Failed to create request data for PlayReady license. |
|7204 |Failed to read request data for PlayReady license. |
|7205 |Failed to parse PlayReady request data. |
|7206 |No PlayReady Customdata in request. |
|7300 |Failed to decrypt FairPlay Streaming service cert. |
|7301 |This Site ID does not have FairPlay Streaming service. |
|7302 |Cannot find private key for FairPlay Streaming. |
|7303 |Invalid FairPlay Streaming key for the Site ID. |
|7304 |Failed to create FairPlay Streaming license. |
|7305 |FairPlay Streaming SPC value is Null. |
|7306 |FairPlay Streaming Device ID is Null. |
|7308 |FairPlay assetId is invalid.|
|7315 |Failed to get FPS server certificate file. |
|7316 |Failed to decrypt Spc with RSA.|
|7317 |FairPlay Spc data is invalid. |
|8002 |Custom Error. Contains error code and message returned from callback page for user authentication. (JSON format) {"ERROR":"custom error code", "MESSAGE":"custom error message"} |
|8004 |Invalid nonce value. |
|8006 |Invalid XML data from callback page. |
|8701 |Failed to access Content Usage Info URL. |
|8705 |Invalid URL for Content Usage Info. |
|8706 |Failed to decrypt data from Content Usage Info URL. |
|8707 |Invalid start or end datetime for playback period. |
|8708 |Invalid datetime format for playback period. |
|8709 |Start of playback period should be earlier than end of the period. |
|8710 |Start of playback period has not come yet. |
|8711 |Playback period is over. |
|8712 |Invalid HDCP setting. |
|8715 |Invalid CGMS-A setting. |
|8716 |Invalid APS setting. |
|9001 |Missing request parameter. |
|9999 |Unsupported mode. |
