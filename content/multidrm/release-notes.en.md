---
title: Multi-DRM Release Notes
linktitle: Release Notes
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  multidrm:
    parent: Muti-DRM
    weight: 100

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 100
---

Release notes for PallyCon Multi-DRM products

## Server Products

### CLI Packager

|Date |Version |Descriptions |
|:---|:---|:---|
| 2020.04.27 | 3.6.0 | [Add]<br> - Feature that generate multiple manifest for each track(resolution)<br> - Multi-key packaging feature for Multi-DRM<br> - Set the video bitrate(bandwidth) |
| 2020.03.06 | 3.

### Wowza Integration SDK

|Date |Version |Descriptions |
|:---|:---|:---|
|  |  |  |


## Client Products

### Widevine Android SDK

|Date |Version |Descriptions |
|:---|:---|:---|
| 2020.07.27 | 1.10.2 |- Target OS version changed (28 -> 29) due to Google's policy.<br>- PallyConDownloadTask API is added to specify the folder name where the content will be saved.<br>- Content URL analysis processing is fixed.|
| 2020.05.12 | 1.10.1 |- The Exoplayer version applied to the SDK is changed from 2.10.4 to 2.10.8.|
| 2019.12.11 | 1.10.0 |- existDownloadedLicense API is added to show whether a license exists in the Database.|
| 2019.10.07 | 1.9.0 |- Exoplayer 2.10.x version update AndroidX, Android API 28,Gradle 5.4.1, Gradle Plugin 3.5.0 have been applied.<br>- The setEnableLog method has been removed.<br>- The SDK update also updated Android/AndroidTV Sample. Please check each sample for changes. |
| 2019.09.09 | 1.8.0 |- Licenses can be removed from the removeLicense(), removeLicenseByCustomData() and removeLicenseByProxy() methods without a network connection. |
| 2019.08.16 | 1.7.0 |- Optimizing the PallyConWVSDK size has been done.<br>- Added setCookie () function to MpdTrackSelector class.<br>- PallyconLoggingListener has been added so you can check the internal logs.<br>- The PallyconWVMSDK.setEnableLog function is excluded in the next version. |
| 2019.07.12 | 1.6.8 |- Fixed an issue with SetCookie function. |
| 2019.06.20 | 1.6.7 |- Added downloadLicenseByToken() function to download license as Token. |
| 2019.06.11 | 1.6.6 |- MediaDrmCallback has been changed so that the cookie set in the SDK API works.<br>- Exception handling has been added to the setCookie function.<br>- The enableLog function name has been changed to setEnableLog, and exception handling has been added. |
| 2019.06.05 | 1.6.5 |- Added support for 64-bit |
| 2019.04.24 | 1.6.4 |- Fixed app installation issue with old version(4.4 ~ 5.0) Android devices |
| 2019.02.13 | 1.6.3 |- Added callback function that is called after download is completed<br>- Added support for ExoPlayer 2.9.3 <br>- Added support for Gradle version 4.6, Android Plugin version 3.2.1, and compileSdkVersion 27<br>- Fixed internal errors when running on Android 9.0 |
| 2018.12.27 | 1.6.1 |- Added function to choose resolution when downloading adaptive DASH content |
| 2018.09.19 | 1.6.0 |- Added simple version sample project for easier trial of the SDK.<br>- Added SDK certification to verify that the target site has permissions. |
|  |  |  |

### FPS iOS SDK

|Date |Version |Descriptions |
|:---|:---|:---|
| 2020.07.06 | 1.8.8 |- Xcode 11.5 and swift 5.2.4 are applied to SDK.<br>- Add XCframework SDK. |
| 2020.02.24 | 1.8.7 |- Content download(DownloadTask) and license download have been modified to work together. |
| 2020.01.22 | 1.8.6 |- You can restrict the use of Cellular when downloading content.<br>- The simulator library(x86_64, i386) is removed from the SDK and distributed. |
| 2020.12.11 | 1.8.5 |- The existDownloadedLicense API is added to know whether the license exists. |
| 2019.11.07 | 1.8.4 |- Deploy an iOS and tvOS SDKs built on Xcode 11.2 and Swift 5.2. |
| 2019.10.18 | 1.8.3 |- Deploy an iOS and tvOS SDKs built on Xcode 11 and Swift 5.1. |
| 2019.07.29 | 1.8.2 |- Fixed Symbol not found on iOS9 target. |
| 2019.07.19 | 1.8.1 |- Fixed bug where saved licenses are not deleted. |
| 2019.06.01 | 1.8.0 |- The SDK certification process has been removed. |
| 2019.04.05 | 1.7.0 |- Supports Swift 5.0 version.<br>- Fixed a bug in download function. |
| 2019.01.28 | 1.6.5 |- Changed access to KeyChain to 'Always access'. |
| 2019.01.05 | 1.6.4 |- Updated SDK authentication.|
| 2018.12.20 | 1.6.3 |- Added a parameter to download only specific bandwidth files of adaptive stream contents. |
| 2018.11.29 | 1.6.2 |- Updated to initialize KeyChain information saved at initial SDK information acquisition.<br>- Updated to initialize information stored in KeyChain when authentication fails. |
| 2018.11.21 | 1.6.1 |- Fixed keychain issue of SDK certification<br>- Fixed a bug in Download API |
| 2018.09.19 | 1.6.0 |- Added simple version sample project for easier trial of the SDK.<br>- Supports Swift 4.2, iOS 12. Built with Xcode 10 and Swift 4.2.<br>- Added SDK certification to verify that the target site has permissions. |
 
### NCG Android SDK

|Date |Version |Descriptions |
|:---|:---|:---|
| 2020.09.03 | 2.6.3 |- 'scoped storage' permission is added to the SDK Sample project. |
| 2020.08.12 | 2.6.2 |- APIs that can be licensed with tokens are added. |
| 2020.07.01 | 2.6.1 |- Sample project errors are fixed. |
| 2020.05.28 | 2.6.0 |- Added setRootingCheckLevel() API, which allows you to specify the strength when checking Android routing. |
| 2020.04.29 | 2.5.5 |- Target version is changed to 29 according to Google policy.<br>- From Android Q(API 29), the'Widevine' UUID is used as the Device ID. | 
| 2020.04.02 | 2.5.4 |- SURI extraction function in EXT-X-MAP from m3u8 file is added to NCG Core and applied to NCG SDK.<br>- The NCG Advanced Sample changes to call different players depending on whether they are HLS content. |
| 2020.03.13 | 2.5.3 |- Android Rooting check is changed. | 
| 2020.02.05 | 2.5.2 |- Secure Time related logs are output to NcgExceptionalEventListener.<br>- NCG Core All logs are not output. |
| 2019.11.11 | 2.5.1 |- Android Target Sdk Version changed from 27 to 28.<br>- AndroidX is applied.<br>- Fixed an issue that prevented external output and routing checking on some devices. |
| 2019.10.07 | 2.5.0 |- The crash on the local web server has been removed. |
| 2019.06.05 | 2.4.0 |- Supports Android 64bit (arm64-v8a, x86_64)<br>As of August 2019, Android 64bit support was required and the NCG SDK included Android ABI arm64-v8a, x86_64. NCG SDK v2.4.0 or later must be applied to register an app in Google Play from August.<br>- Android API Level change (API 15 -> 16)<br>Minimal support in NDK has been changed to Android API Level 16 while supporting NCG SDK 64bit. The minimum support for 64bit Android is Android API Level 21. |
| 2019.02.27 | 2.3.2 |- Added support for Android verion 9.0. |
| 2018.12.27 | 2.3.1 |- Added security patch to prevent app running on Android emulator. |
| 2018.09.19 | 2.3.0 |- Added simple version sample project for easier trial of the SDK.<br>- Enhanced secure time logic for better security of offline playback. |

### NCG iOS SDK

|Date |Version |Descriptions |
|:---|:---|:---|
| 2019.10.08 | 2.5.0 |- The crash on the local web server has been removed. |
| 2019.01.22 | 2.4.2 |- Added new license token API. (acquireLicenseByToken) |
| 2018.11.27 | 2.4.1 |- Updated recovery process when RODB file is broken or modified. |
| 2018.09.19 | 2.4.0 |- Added simple version sample project for easier trial of the SDK.<br>- Enhanced secure time logic for better security of offline playback.<br>- Supports Swift 4.2, iOS 12. Built with Xcode 10 and Swift 4.2.<br>- Changed the name of C++ library from libstdc++ to libc++ for Xcode 10. |

