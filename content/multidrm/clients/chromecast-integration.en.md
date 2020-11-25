---
title: Chromecast Integration Guide
linktitle: Chromecast Integration
summary: This document describes how to integrate Chrome Cast with the FairPlay iOS SDK and Widevine Android SDK.
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
lastmod: "2020-09-16T00:00:00Z"
draft: false
featured: true

menu:
  multidrm:
    parent: Client Integration
    weight: 90

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 90
---

## Overview

This document describes how to integrate Chrome Cast with the FairPlay iOS SDK and Widevine Android SDK.

## Supported SDKs

- PallyCon FPS iOS SDK (iOS 9.0 or later)
- PallyCon Widevine Android SDK (Android 4.4 or later)

## Prerequisites

- For Android devices, you must have the latest version of '[Google Play Services](https://play.google.com/store/apps/details?id=com.google.android.gms)' installed.

- Chromecast does not support Apple FairPlay DRM. In order to cast FairPlay DRM content from your iOS device to Chromecast, the same content must be packaged as DASH-CENC content with Widevine DRM.

- Your Chromecast and mobile device must be connected to the same Wi-Fi. You may need to install "Google Home" on your mobile device to set up your device. Follow the instructions provided by the app after installation.

- "Google Home" can be downloaded from [Play Store](https://play.google.com/store/apps/details?id=com.google.android.apps.chromecast.app) or [App Store](https: /itunes.apple.com/us/app/google-home/id680819774).

- For the information of devices that supports Chromecast ([Chromecast Ultra](https://store.google.com/us/product/chromecast_ultra) or [Chromecast 2nd generation](https://store.google.com/us/product/chromecast_2015 )), Smart TV, OTT equipment (Nexus Player, CJ Viewing etc.), please refer to the manufacturer's guide document.

## Chromecast Integration

- Google's built-in receiver app does not support DRM. To work with PallyCon SDK, you need to create a Custom Receiver app using the Media Player Library.

- A pre-built Custom Receiver app that works with PallyCon SDK is provided for fast integration test. The app ID is '1CDBBCB6' and it is applied to the sample app in the PallyCon SDK.

- In order to play PallyCon Multi-DRM applied contents in Custom Receiver app, custom data used for license issuance must be transferred to Custom Receiver app.

  - For more information on custom data, please visit [here](../multidrm-native-integration/#pallycon-custom-data-v2).
  - Refer to the PallyCon sample app for sending custom data from the Sender app to the Receiver app.
  - Please refer to [Google Cast Guides - DRM](https://developers.google.com/cast/docs/player#drm-playreadywidevine) and PallyCon Custom Receiver app for handling custom data in Receiver app.
  
- After modifying the PallyCon Custom Receiver app, you can use it by registering in [Google Cast SDK Developer Console](https://cast.google.com/publish/).

- For more information on developing Sender and Receiver apps, see [Google Cast Guides](https://developers.google.com/cast/docs/developers) and the PallyCon SDK sample app.

- For more information on registering your Custom Receiver app, please refer to [Google Cast Guides - Registration](https://developers.google.com/cast/docs/registration).
