---
title: FairPlay iOS SDK Guide
linktitle: FairPlay iOS SDK
summary: This document describes how to use the libraries and sample project included in the FairPlay iOS SDK.
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
lastmod: "2020-09-16T00:00:00Z"
draft: false

menu:
  multidrm:
    parent: Client Integration
    weight: 30

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 30
---

## Overview

PallyCon FairPlay iOS SDK makes it easy to apply Apple's FairPlay Streaming DRM when developing media service apps for iOS. This document describes how to use the libraries and sample projects included in the SDK.

Details of PallyCon Multi DRM service integration are provided in [License Token Guide](../../license/license-token). For technical questions about using the SDK, please visit our [Helpdesk site](https://pallycon.zendesk.com).

> SDK products can be requested when subscribing to PallyCon cloud commercial service. Then SDK can be downloaded from the `Download` page of PallyCon Console site.

## Supported Environments

- iOS 9.0 or later
- ARC(Automatic Reference Counting) required
- This SDK has been tested on `Xcode 11`. It does not work on Simulator due to the Apple's policy.

> If a simulator build is required for testing apps other than FPS DRM operation, the FPS SDK related code must be excluded from the build using the following macro, and the FPS library must be excluded from the build target.
> 
> #if !targetEnvironment(simulator)
> ...
> #endif

## Prerequisites

To apply FairPlay Streaming DRM, content service site should follow the below steps before starting integration.

1. Enroll in Apple developer program ( https://developer.apple.com/support/enrollment/ )
2. Request `FPS Deployment Package` to Apple ( https://developer.apple.com/streaming/fps/ )
3. Using the `deployment package`, create the below four items and register them on `Integration Settings` page of PallyCon Console site. 

```s
- FPS certificate file (.der or .cer)
- Private key file (.pem)
- Password for the private key (string)
- Application secret key (ASK) string
```

> **NOTE:**
>
> Please refer to the [FairPlay Cert Registration Tutorial]({{<ref "fps-cert-tutorial.en.md">}}) for details on the above registration process.

## Tutorial Video

This video is a tutorial for playing DRM content in an iOS app using the FPS SDK.

{{< youtube 8hGu_1HQ1NY >}}

> For optimal playback, select '1080p' as the video quality and enable subtitle (Korean or English) before starting playback.

## Configuring your project

You can add PallyCon FPS SDK by following steps:

1. Extract `PallyConFPSSDK.zip` file.
1. Add the `PallyConFPSSDK.framework` or `PallyConFPSSDK.xcframework` library provided in the `lib` folder to your project.
  - Add the framework's path to `Linked Frameworks and Libraries` setting in Xcode `General` tab.
  - Add the framework's path to `Framework Search Paths` setting in Xcode `Build Settings` tab.
1. Set `Enable Bitcode` in Build Settings tab to `No`.

> - If you are using the latest version of Xcode, you can solve the Swift version issue by using the `PallyConFPSSDK.xcframework` library.
> - [GoogleCast Framework](https://developers.google.com/cast/docs/developers#ios) is required to build 'Advanced Sample'.
> - Use [COCOAPODS](https://cocoapods.org/) to install GoogleCast.framework (pod install), and execute the created `PallyConFPSAdvanced.xcworkspace`.

## Quick Start

The following code shows how to stream or download FPS HLS content using PallyConFPS SDK.

### Streaming FPS content

```swift
// 1. initialize a PallyConFPS SDK.
do {
	let pallyconSdk: PallyConFPS = try PallyConFPSSDK(siteId:"Site ID", siteKey:"Site Key", fpsLicenseDelegate:"PallyConFPSLicenseDelegate")
} catch PallyConSDKException.DatabaseProcessError(let message) {
	print("PallyConFPSSDK initialize failed.\n\(message)")
}

// 2. Set parameters required for FPS content playback.
pallyconSdk.prepare(urlAsset:"Asset", userId:"User ID", contentId:"ContentID", optionalId:"optionalId")
// If you use a token, set the token information and call the method below. 
// pallyconSdk.prepare(urlAsset:"Asset", userId:"User ID",  contentId:"ContentID", token:"Token String")
// If you use a customData, set the customData information and call the method below.
// pallyconSdk.prepare(urlAsset:"Asset", customData:"CustomData")
```

### Downloading FPS content

```swift
// 1. Create PallyConFPSSDK instance.
do {
    let pallyconSdk: PallyConFPS = try PallyConFPSSDK(siteId:"Site ID", siteKey:"Site Key", fpsLicenseDelegate:"PallyConFPSLicenseDelegate")
} catch PallyConSDKException.DatabaseProcessError(let message) {
    print("PallyConFPSSDK initialize failed.\n\(message)")
}

// 2. Create DownloadTask instance.
// You need to use DownloadTask instance for content download.
// DownloadTask should be created by PallyConFPS instance.
let downloadTask = pallyconSdk.createDownloadTask(url:"Content URL", userId:"User ID", contentId:"Content ID", optionalId:"Order ID", downloadDelegate:"PallyConFPSDownloadDelegate")
/**
If you use a token, set the token information and call the method below. 
let downloadTask = pallyconSdk.createDownloadTask(url:"Content URL", userId:"User ID", contentId:"Content ID", token:"Token String", downloadDelegate:"PallyConFPSDownloadDelegate")
*/

// 3. Start downloading content
// If the content download was canceled previously, it will be resumed.
downloadTask.resume()

// 4. Cancel download (if needed)
downloadTask.cancel()
```

### Acquiring CustomData information

```swift
// 1. Create PallyConFPSSDK instance.
do {
    let pallyconSdk: PallyConFPS = try PallyConFPSSDK(siteId:"Site ID", siteKey:"Site Key", fpsLicenseDelegate:"PallyConFPSLicenseDelegate")
} catch PallyConSDKException.DatabaseProcessError(let message) {
    print("PallyConFPSSDK initialize failed.\n\(message)")
}

// 2. Acquire a CustomData information
do {
    let customDataInfo: PallyConTokenInfo = try pallyconSdk?.getCustomDataInfo(from:"CustomData")
} catch PallyConSDKException.InvalidParameter() {
    print("PallyConFPSSDK invalid customData")
}
```

### Acquiring Token information

```swift
// Acquire a Token information
let tokenInfo: PallyConTokenInfo = PallyConFPSSDK.getTokenInfo(from:"Token String")
```

### Create CustomData for Chromecast playback

```swift
// When using Chromecast SDK, getting CustomData required to create 'GCKMediaInformation' object
// CustomData must be created using PallyConFPS object.
let customData = pallyconSdk.getCustomDataForChromcast(userId: "User ID", contentId: "Content ID", optionalId: "Optional ID")
/**
When using a license token instead of callback, call the method below by setting the token information.
let customData = pallyconSdk.getCustomDataForChromcast(token: "Token String")
*/
```

## API References

> For descriptions of all the APIs provided by PallyCon FPS iOS SDK, see the API reference documentation included in the SDK file.
