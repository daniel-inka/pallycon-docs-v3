---
title: License Token Tutorial
linktitle: License Token Tutorial
summary: This document provides a tutorial for token-type DRM license integration.
toc: true
type: book
date: "2020-05-05T00:00:00+01:00"
lastmod: "2020-11-11T00:00:00Z"
draft: false
menu:
  multidrm:
    parent: License Issuance
    weight: 20
    name: License Token Tutorial

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 20
---

## Overview {#intro}

This document provides step-by-step instructions on how to generate tokens for the token-based license issuance, along with sample code. 

## Notice {#notice}

Before you read this tutorial, please keep the following points in mind:

### Where tokens are created

- The creation of the token must be performed on the Content Management System (CMS) back-end server of the content service to which PallyCon Multi-DRM will be applied.
- Generating tokens directly from within client-side JavaScript or mobile applications is a security risk and should be avoided.

### How to use the created token

- Before playing the DRM content on the client player, the CMS backend must determine whether the client or user is authorized to play. Then it generates a token with DRM license rules (time limit and security options) and pass it to the client.
- The function of delivering the token data to the client should be implemented by the content service. Usually it is transmitted with the metadata (playback URL, DRM linked data, poster image, etc.) of the DRM contents to be played.

### Sample codes and inputs

- The sample code in this document is based on Node.JS. If you use any other server-side programming language, please refer to the sample code and implement the converted code yourself.
- The various setting values shown in the sample code are for reference only. The generated token data in the sample code does not actually work.
- To create a working token, you need to enter the setting value of your account after signing up to PallyCon service.

## Step 1 - Input setting values {#step1}

Prepare various input values required for license token generation as follows.

```js
const AES_IV = "0123456789abcdef";
const siteInfo = {
  siteId: "TUTO",
  siteKey: "lU5D8s3PWoLls3PWFWkClULlFWk5D8oC",
  accessKey: "LT2FVJDp2Xr018zf4Di6lzvNOv3DKP20"
};

let licenseInfo = {
  drmType: "Widevine",
  contentId: "bigbuckbunny",
  userId: "test-user"
};

let licenseRule = {
  playback_policy: {
    limit: true,
    persistent: false,
    duration: 3600
  }
};

console.log("license rule : " + JSON.stringify(licenseRule));
```

### Input value description

| **Name** | **Format** | **Description** |
| --- | --- | --- |
| AES_IV | 16 bytes string | "0123456789abcdef" (fixed value) |
| siteId | 4 uppercase alphabet | The ID of the service site registered on PallyCon Console site (check the PallyCon Console's 'Site Settings' page) |
| siteKey | 32 bytes base64 string | Encryption key required for service site integration (check the PallyCon Console's 'Site Settings' page) |
| accessKey | 32 bytes base64 string | Encryption key used for additional integration such as packager, license token, etc. (check the PallyCon Console's 'Site Settings' page) |
| drmType | Input one of these DRM. "NCG", "Widevine", "PlayReady", "FairPlay" (case sensitive) | Enter the type of DRM corresponding to the client environment. Refer to [DRM Platform Support](https://pallycon.com/drm-platform) |
| contentId | Max 200 alphanumeric characters | The unique ID of the DRM content to play. Must be the same as the CID used to package the content. |
| userId | Max 32 alphanumeric characters | End-user's ID which is managed by the content service. Input "LICENSETOKEN" if there is no user ID. |

> Value of `drmType` should be entered exactly in case according to the specification.

LicenseRule JSON data is used to set the [detailed rules]({{%ref "license-token.en.md"%}} for the content playback rights. This tutorial uses the default values as entered in the sample code above.

When you run Step 1 code, the following results are output to the browser console.

```s
license rule : {"playback_policy":{"limit":true,"persistent":false,"duration":3600}}
```

## Step 2 - Encrypt license rule {#step2}

Encrypt the license rules JSON string prepared in step 1 with AES256 algorithm and convert the result to a base64 string.

```js
// Add the following code after step 1 code

const crypto = require("crypto");

const cipher = crypto.createCipheriv("aes-256-cbc", siteInfo.siteKey, AES_IV);
let encryptedRule = cipher.update(
  JSON.stringify(licenseRule),
  "utf-8",
  "base64"
);
encryptedRule += cipher.final("base64");

console.log("encrypted rule : " + encryptedRule);
```

The following settings are used for AES encryption:

- Encryption algorithm: AES256 CBC mode
- Encryption key: The 32-byte PallyCon site key value entered in step 1
- IV (Initial Vector): 16 byte AES_IV value entered in Step 1
- Padding: pkcs7 (not shown in the above code because it is default)

> AES encryption processing must use the appropriate libraries and APIs for each server-side programming language.

The execution result of the second stage code is as follows.

```s
encrypted rule : bO9DzyQgtgfbSDWmqGgZXWZdWm7M4oZ4X0hC1hS6QNraiwLI0LwmNY+OfOh1L0KmtuH7NF1blUxep9YLWrNfSy4H/6swQW7pbjZnRqRguDQ=
```

## Step 3 - Create hash value {#step3}

The hash value used to verify the integrity of the token data is generated as follows:

```js
// Add the following code after step 2 code

const UTCTime = new Date().toISOString().replace(/\.\d{3}/gi, '');

let hashData = {
  siteId: siteInfo.siteId,
  accessKey: siteInfo.accessKey,
  drmType: licenseInfo.drmType,
  userId: licenseInfo.userId,
  cid: licenseInfo.contentId,
  token: encryptedRule,
  timestamp: UTCTime
};

const hashInput =
  hashData.accessKey +
  hashData.drmType +
  hashData.siteId +
  hashData.userId +
  hashData.cid +
  hashData.token +
  hashData.timestamp;

console.log("hash input : " + hashInput);

let hashString = crypto
  .createHash("sha256")
  .update(hashInput)
  .digest("base64");

console.log("hash string : " + hashString);
```

The input value for generating a hash is a string of various configuration values, encrypted license rules, and current time information according to [token specification]({{%ref "license-token.en.md"%}}).

> The SHA256 algorithm is used to generate hash values, and the resulting value of the hash function must be entered into the base64 function as a binary data, without being converted to a string.

The result of the execution of Step 3 code is as follows.

```s
hash input : LT2FVJDp2Xr018zf4Di6lzvNOv3DKP20WidevineTUTOtest-userbigbuckbunnybO9DzyQgtgfbSDWmqGgZXWZdWm7M4oZ4X0hC1hS6QNraiwLI0LwmNY+OfOh1L0KmtuH7NF1blUxep9YLWrNfSy4H/6swQW7pbjZnRqRguDQ=2019-07-17T08:57:04Z 

hash string : 0Zef3WHBbzQAR0oGf5KXn25G1TmvB2Bkvzv+7OqIWvQ=
```

## Step 4 - Create license token {#step4}

Using the data prepared so far, create the license token as follows.

```js
// Add the following code after step 3 code

let tokenData = {
  drm_type: licenseInfo.drmType,
  site_id: siteInfo.siteId,
  user_id: licenseInfo.userId,
  cid: licenseInfo.contentId,
  token: encryptedRule,
  timestamp: UTCTime,
  hash: hashString
};

console.log("token json : " + JSON.stringify(tokenData));

let base64Token = Buffer.from(JSON.stringify(tokenData)).toString("base64");

console.log("base64 encoded token : " + base64Token);
```

First, JSON data is created by combining various input values and encrypted license rules, timestamps, and hash values according to the token specification. Converting the corresponding JSON data to a Base64 string completes the token for the client to use for DRM license requests.

The result of executing the 4th step code is as follows.

```s
token json : {"drm_type":"Widevine","site_id":"TUTO","user_id":"test-user","cid":"bigbuckbunny","token":"bO9DzyQgtgfbSDWmqGgZXWZdWm7M4oZ4X0hC1hS6QNraiwLI0LwmNY+OfOh1L0KmtuH7NF1blUxep9YLWrNfSy4H/6swQW7pbjZnRqRguDQ=","timestamp":"2019-07-17T08:57:04Z","hash":"0Zef3WHBbzQAR0oGf5KXn25G1TmvB2Bkvzv+7OqIWvQ="} 

base64 encoded token : eyJkcm1fdHlwZSI6IldpZGV2aW5lIiwic2l0ZV9pZCI6IlRVVE8iLCJ1c2VyX2lkIjoidGVzdC11c2VyIiwiY2lkIjoiYmlnYnVja2J1bm55IiwidG9rZW4iOiJiTzlEenlRZ3RnZmJTRFdtcUdnWlhXWmRXbTdNNG9aNFgwaEMxaFM2UU5yYWl3TEkwTHdtTlkrT2ZPaDFMMEttdHVIN05GMWJsVXhlcDlZTFdyTmZTeTRILzZzd1FXN3BialpuUnFSZ3VEUT0iLCJ0aW1lc3RhbXAiOiIyMDE5LTA3LTE3VDA4OjU3OjA0WiIsImhhc2giOiIwWmVmM1dIQmJ6UUFSMG9HZjVLWG4yNUcxVG12QjJCa3Z6dis3T3FJV3ZRPSJ9
```

***

This completes the license token tutorial.

To play the actual DRM content using the created token, please refer to the [Bitmovin HTML5 Player Tutorial](https://pallycon.com/blog/how-to-integrate-pallycon-multi-drm-with-bitmovin-video-player) posted on the PallyCon blog site.
