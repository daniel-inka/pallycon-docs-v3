---
title: Multi-DRM Native Client Integration Guide
linktitle: Native Client Integration
summary: This document guides how to acquire DRM license from PallyCon Multi-DRM Cloud Server to playback content on various multi-drm client platforms without using PallyCon client SDKs.
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
lastmod: "2020-09-16T00:00:00Z"
draft: false

menu:
  multidrm:
    parent: Client Integration
    weight: 40

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 40
---

## Overview

PallyCon Multi-DRM Cloud Server provides DRM license issuance for Google Widevine Modular, Apple FairPlay Streaming and Microsoft PlayReady.

There are two types of methods for issuing multi-DRM (FPS, Widevine, PlayReady, NCG) licenses from PallyCon cloud server.

1. Callback type 
   - When PallyCon cloud server receives license request from multi-DRM client, it first checks service site's callback page to see if the user has valid permissions.
   - In the case of a request from an authorized user, the service site returns information such as authentication, usage rights (unlimited, fixed period) and various security options to the PallyCon cloud server through the callback web page.
   - PallyCon cloud server receives the response from the callback page and issues the license to the client.

2. Token type (refer to [License Token Guide](../../license/license-token)
   - When a multi-DRM client tries to play DRM content, the service site can create license token with pre-defined specification and send the token to the client.
   - The service site can set usage rights (expiration date or unlimited) and various security options through the token data.
   - When a client requests a license with a token, the PallyCon cloud server validates the token and issues a license.

This document guides how to acquire DRM license from PallyCon Multi-DRM Cloud Server to playback content on various multi-drm client platforms without using PallyCon client SDKs.

## Multi-DRM License Issuance {#workflow}

{{< figure library="true" src="Integration_Multi_DRM_Overview_en.png" >}}

1) Prepare Playback
   - Client (Player) receives DRM integration data (PallyCon CustomData or token) from the service site to play DRM contents and attempts to play DRM contents.

2) License Request
   - Client sets PallyCon Custom Data or token into license challenge data and sends the data (created by native DRM agent) to PallyCon Multi-DRM Cloud Server.

3) Callback User Authentication
   - In case of callback type integration, PallyCon Multi-DRM Cloud Server requests user authentication to service site via callback interface page.
   - In the token-based integration, user authentication is handled at the service site before token issuance, so this process is omitted.

4) Response Usage Right Info
   - After user authentication, service site returns content usage right info to PallyCon server via callback interface page.

5) License Issuance
   - PallyCon Multi-DRM Cloud Server creates and returns license data by DRM type, using usage right info received from callback page or token request.

## Widevine Modular / PlayReady DRM License Integration {#widevine-playready}

### Request

- Request URI : https://license.pallycon.com/ri/licenseManager.do

- Request Method : POST

- There are two ways to send custom data: Custom HTTP Header, URL Parameter. In the case of PlayReady, the customdata field of the PlayReady Challenge is also available (only license token string).

|Name |Value |
|---|---|
|pallycon-customdata-v2 | Use custom data or token values depending on the user authentication method. <br> 1) base64 encode([PallyCon Custom Data v2 Format](#pallycon-custom-data-v2)) <br> 2) license token string (refer to [License Token Guide](../../license/license-token)) |

> POST body : License Challenge Data created by platform’s native DRM client 

### Response

- Success

|Name |Value    |
|---|---|
|status code |200 OK |
|response body |native DRM license data<br> - Widevine Modular: binary data<br> - PlayReady DRM : base64 encoding string |

- Failure

|Name |Value    |
|---|---|
|status code |200 OK |
|response body |JSON Data <br> {"errorCode": "error code","message": "error message"} |

## FairPlay Streaming DRM License Integration {#fairplay}

To apply FPS DRM, content service site should follow the below steps before starting integration.

1. Enroll in Apple developer program ( https://developer.apple.com/support/enrollment/ )
2. Request `FPS Deployment Package` to Apple ( https://developer.apple.com/streaming/fps/ )
3. Using the `deployment package`, create the below four items and register them on `Integration Settings` page of PallyCon Console site.

```s
- FPS certificate file (.der or .cer)
- Private key file (.pem)
- Password for the private key (string)
- Application secret key (ASK) string
```

> Please refer to the [FairPlay Cert Registration Tutorial]({{<ref "fps-cert-tutorial.en.md">}}) for details on the above registration process.

### 1. FPS Certification File download integration spec

To implement FPS, client app should download FPS Certificate(.cer) file from PallyCon Server using the below integration spec.  

#### Request

- Request URI : https://license.pallycon.com/ri/fpsKeyManager.do?siteId=<service site id>
- Request Method : GET

|Parameter |Value |
|---|---|
|siteid |Service Site ID(4 byte) - from PallyCon Console site |

#### Response

- Success

| Name          | Value       |
|---|---|
| status code   | 200 OK      |
| response body | base64 encoding (fps certificate data) |

- Failure

| Name        | Value  |
|---|---|
| status code | 200 OK |
| response body |JSON Data {"errorCode": "error code", "message": "error message"}        |

> The FPS Cert download URL is provided for your convenience. If needed, a Cert file (.cer or .der) may be hosted on your own server and its URL may be used instead of the above specification.

### 2. FPS License Integration Spec

#### Request

- Request URI : https://license.pallycon.com/ri/licenseManager.do
- Request Method : POST
- Custom HTTP Header, URL Parameter are available.

|Name |Value |
|---|---|
|pallycon-customdata-v2 | Use custom data or token values depending on the user authentication method. <br> 1) base64 encode([PallyCon Custom Data v2 Format](#pallycon-custom-data-v2)) <br> 2) license token string (refer to [License Token Guide](../../license/license-token)) |

- POST body : spc='base64 encoding(spc data)'

|Parameter |Value |
|---|---|
|spc |base64 encoding ( SPC data created by native drm client ) |

#### Response

- Success

|Name |Value |
|---|---|
|status code | 200 OK |
|response body |base64 encoding (CKC data from FPS KSM) |

- Failure

|Name |Value |
|---|---|
|response body | JSON Data {"errorCode": "error code", "message": "error message"}

## PallyCon Custom Data v2 Format {#pallycon-custom-data-v2}

### JSON Format

```json
{
    "drm_type": "<multi-drm type>",
    "site_id": "<site id>",
    "data": "<base64 encoding(aes256 encryption(custom data json string))>"
}
```

| Name     | Value                        |
| ---      | ---                          |
| drm_type | DRM type <br> ("NCG", "Widevine", "PlayReady", "FairPlay") |
|SiteID | Service site ID issued by PallyCon Cloud Service (4byte) |
|JSON Data | User authentication info (custom data json string) <br> aes256 encryption -> base64 encoding |

### Custom Data JSON Format

```json
{
    "user_id": "user id",
    "cid": "content id",
    "oid": "optional id"
}
```

|Name |Value |
|---|---|
|user_id |ID of service site's user |
|cid | Unique ID of content |
|oid | Optional data (such as order info) which needs to be sent to service site for the integration. |

### Example

```json
PallyCon Customdata v2 string
{
     "drm_type":"Widevine", 
     "site_id":"DEMO",
     "data":"4wvWFelCQ4ynPUaCSWeb1fcNuFUFqFhEH0jivn11OStvVPP/05wUkNhdKCGchNz1"
}

custom data json string
{"user_id":"test", "cid":"test cid"}

"pallycon-customdata-v2" : "eyJkcm1fdHlwZSI6IldpZGV2aW5lIiwgInNpdGVfaWQiOiJERU1PIiwgImRhdGEiOiI0d3ZXRmVs
Q1E0eW5QVWFDU1dlYjFmY051RlVGcUZoRUgwaml2bjExT1N0dlZQUC8wNXdVa05oZEtDR2NoTnox
In0NCg=="
```

## AES256 Encryption {#aes256}

```s
AES256 Encryption
- mode : CBC
- key : 32 byte (Site key from PallyCon Console site)
- iv : 16 byte (0123456789abcdef)
- padding : pkcs7
```

AES256 Encryption/Decryption should be processed as below using site authentication key which is  created by ‘Service Request’ on PallyCon Console site. ( The key can be found on PallyCon Console’s settings page )

## Examples {#examples}

```s
PallyCon Customdata v2 string
{
     "drm_type":"Widevine",
     "site_id":"DEMO",
     "data":"4wvWFelCQ4ynPUaCSWeb1fcNuFUFqFhEH0jivn11OStvVPP/05wUkNhdKCGchNz1"
}
custom data json string 
{"user_id":"test", "cid":"test cid"}

1) Using Custom HTTP Header

setRequestHeader("pallycon-customdata-v2", "eyJkcm1fdHlwZSI6IldpZGV2aW5lIiwgInNpdGVfaWQiOiJERU1PIiwgImRhdGEiOiI0d3ZXRmVs
Q1E0eW5QVWFDU1dlYjFmY051RlVGcUZoRUgwaml2bjExT1N0dlZQUC8wNXdVa05oZEtDR2NoTnox
In0NCg==");

2) Using URL parameter

license url = "https://license.pallycon.com/ri/licenseManager.do?pallycon-customdata-v2=eyJkcm1fdHlwZSI6IldpZGV2aW5lIiwgInNpdGVfaWQiOiJERU1PIiwgImRhdGEiOiI0d3ZXRmVs
Q1E0eW5QVWFDU1dlYjFmY051RlVGcUZoRUgwaml2bjExT1N0dlZQUC8wNXdVa05oZEtDR2NoTnox
In0NCg=="

3) Using PlayReady customdata ( only license token string )

<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns:soap="https://schemas.xmlsoap.org/soap/envelope/">
<soap:Body>AcquireLicense
    xmlns="http://schemas.microsoft.com/DRM/2007/03/protocols">
<challenge>
...

<ClientInfo> ... </ClientInfo>
<CustomData>eyJ0b2tlbiI6IlRhXC9SdkJBZ1U4ZDdwSExqeXVyK2MxTU05N1pXUm1pSmdXYzJcL1U5MnBPbWJhOXNtXC9ybHdmc0lNM2ZValhzSTUiLCJzaXRlX2lkIjoiREVNTyIsImRybV90eXBlIjoiUGxheVJlYWR5IiwiY2lkIjoidHJheSJ9</CustomData>
```

## PallyCon Custom Data Format (Old version) {#custom-data-v1}

> **DRMType(1) + SiteID(4) + base64 E( aes256 E ( json data ))**

| Name     | Value                        |
| ---      | ---                          |
| DRM Type | Native DRM Type <br> "W": Widevine, "P": PlayReady, "F": FairPlay Streaming |
|SiteID |Service Site ID created by PallyCon (4byte) |
|JSON Data |User Authentication data <br>AES256 encryption and base64 encoding |

### JSON Data Format

```json
{
    "user_id": "user id",
    "cid": "content id",
    "oid": "optional id"
}
```

|Name |Value |
|---|---|
|user_id |Service Site User ID |
|cid |Content ID |
|oid | Optional data (such as order info) which needs to be sent to service site for the integration. |

### Example

```s
- DRM: Widevine Modular,
- Site ID: DEMO,
- JSON: {"user_id": "test_user", "cid":"DEMOCID1", "oid":""}

"pallycon-customdata" : "WDEMOeGNlZWRzIHRoZSBzaG9ydCm5hbCBwbGVhc3VyZS4="

1) Using Custom HTTP Header

setRequestHeader("pallycon-customdata", "WDEMO4wvWFelCQ4ynPUaCSWeb1fcNuFUFqFhEH0jivn11OStvVPP/05wUkNhdKCGchNz1");

2) Using URL parameter

license url = "https://license.pallycon.com/ri/licenseManager.do?pallycon-customdata=WDEMO4wvWFelCQ4ynPUaCSWeb1fcNuFUFqFhEH0jivn11OStvVPP/05wUkNhdKCGchNz1"
```
