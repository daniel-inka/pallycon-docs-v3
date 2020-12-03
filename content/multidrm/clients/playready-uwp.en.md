---
title: PlayReady UWP SDK Guide
linktitle: PlayReady UWP SDK
summary: This document describes how to use the libraries and sample projects included in the SDK.
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
lastmod: "2020-09-16T00:00:00Z"
draft: false

menu:
  multidrm:
    parent: Client Integration
    weight: 50

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 50
---

## Overview

PallyCon PlayReady UWP SDK makes it easy to apply Microsoft's PlayReady DRM when developing media service apps for Universal Windows Platform (UWP). This document describes how to use the libraries and sample projects included in the SDK.

Details of PallyCon Multi DRM service integration are provided in [License Token Guide](../../license/license-token).
For technical questions about using the SDK, please visit our [Helpdesk site](https://pallycon.zendesk.com).

> SDK products can be requested when subscribing to PallyCon cloud commercial service. Then SDK can be downloaded from the `Download` page of PallyCon Console site.

## Supported Environments

- Windows 10 or later (OS build 10586 or later)
- XBox One 10 or later

## Prerequisites

- This SDK is developed and tested on `Visual Studio 2017(version 15)`.

- You need to sign up on [PallyCon Console Site](https://console.pallycon.com) to get `Site ID` and `Site Key`, and prepare DASH content using PallyCon Packager.

- **You cannot test PallyCon PlayReady UWP SDK Sample to play DASH content until the above is done.**

## Configuring your project

You can apply PallyCon PlayReady UWP SDK by following steps:

1. Extract downloaded SDK zip file.
2. Add the `PallyConPRSDK.dll` file provided in `lib` folder to your UWP application project.
3. Declare and implement the followings: `using PallyConPRSDK;`, `using PallyConSDK.DownloadTask;` `using PallyConSDK.DownloadTask.ProxyServer;`

## Quick Start

The following code shows how to stream DASH content using PallyCon PlayReady UWP SDK.

### Streaming DASH content

***
MainPage.xaml

~~~xml
<!--MainPage.xaml-->
<Page
    x:Class="UWPImplementation.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:UWPImplementation"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d">

    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <MediaElement Name="Player" />
    </Grid>
</Page>
~~~

***
MainPage.xaml.cs

~~~cs
// MainPage.xaml.cs
// 1. PallyConPRSDK Declaration
using PallyConSDK;
namespace UWPImplementation
{
    public sealed partial class MainPage : Page
    {
    	// 2. SDK Instance
        public static PallyConPRSDK pallyconsdk = PallyConPRSDK.GetInstance;
        public string ContentURL = "Content URL";
        public string LicenseURL = "License Acqusition URL";
        public string Token = "Token String";

        public MainPage()
        {
            this.InitializeComponent();
            try
            {
            	// 3. Site ID, Site Key 
                pallyconsdk.Initialize("Site ID", "Site Key");
                // 4. Set ProtectionManager to MediaElement
                // Set isProactive parameter for Reactive or Proactive content type. default: false(Reactive)
                Player.ProtectionManager = pallyconsdk.CreateProtectionManagerByToken(ContentURL, LicenseURL, Token, false);
                // 5. Playback
                Player.Source = new Uri(ContentURL);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
            }
        }
    }
}
~~~

### Content download and play

You can download DASH content from server to local device.

> **NOTE:** 
> * To playback downloaded content, the device must be connected to network to get DRM license. (offline playback is not available)

***
MainPage.xaml.cs

~~~Cs
// MainPage.xaml.cs
// 1. Declare PallyConPRSDK
using PallyConSDK;
namespace UWPImplementation
{
    public sealed partial class MainPage : Page
    {
    	// 2. SDK Instance
        public static PallyConPRSDK pallyconsdk = PallyConPRSDK.GetInstance;
        public PallyConDownloadTask downloadTask;
        public PallyConProxyServer proxyServer;

        public string ContentURL = "Content URL";
        public string LicenseURL = "License Acquisition URL";
        public string Token = "Token String";

        public MainPage()
        {
            this.InitializeComponent();
            try
            {
            	// 3. Input Site ID, Site Key
                pallyconsdk.Initialize("Site ID", "Site Key");
                // 4. Run Proxy Server 
                proxyServer = pallyconsdk.CreateProxyServer();
                proxyServer.Initialize();
                // 5. Set content download info
                downloadTask.CreateDownloadTask(Content ID, Content Name, Content URL,
                			ProgressHandler, CompleteHandler, FailHandler);
				downloadTask.Start();
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
            }
        }

        private void DownloadProgress(string contentName, int index, int totalIndex)
        {
        	Debug.WriteLine("Content Name: {0} TotalIndex: {1} Index: {2}", contentName, totalIndex, index);
        }

        private void DownloadComplete(string contentName, StorageFolder fileFolder)
        {
        	Debug.WriteLine("Content Name: {0} Folder Path: {1}", contentName, fileFolder.Path);
            // 6. Set ProtectionManager to MediaElement
            // Set isProactive parameter for Reactive or Proactive content type. default: false(Reactive)
            Player.ProtectionManager = pallyconsdk.CreateProtectionManagerByToken(ContentURL, LicenseURL, Token, false);
            // 7. Get content URL from Proxy Server
            Uri localMpdUrl = proxyServer.GetContentUri(Content Name);
            // 8. Subtitle (refer to sample code)
            var subtitleLists = proxyServer.GetSubTitleUrl(localMpdUrl, contentName)
            MediaSource mediaSource = MediaSource.CreateFromUri(localMpdUrl);
            foreach (var subtitle in subtitleLists)
            {
            	var vttUri = new Uri(subtitle);
                var vtts = TimedTextSource.CreateFromUri(vttUri);
                mediaSource.ExternalTimedTextSources.Add(vtts);
            }
            MediaPlaybackItem mediaPlaybackItem = new MediaPlaybackItem(mediaSource);
            Player.SetPlaybackSource(mediaPlaybackItem);
            // 9. Playback
            Player.Source = new Uri(localMpdUrl);
        }

        private void DownloadFail(string contentName, string failedFileUrl, HttpResponseMessage response)
        {
        	Debug.WriteLine("Content Name: {0} Folder Uri: {1}, StatusCode: {2}", contentName, failedFileUrl, response.StatusCode);
        }
    }
}
~~~

## PlayReady Hardware DRM

Hardware-based PlayReady DRM is supported on a variety of devices, including Windows 10 PCs, Smart TVs, XBOX game consoles and tablets. Windows PCs must be running Windows 10 and include a supported hardware configuration in order to support PlayReady hardware DRM. Please check [Microsoft PlayReady Hardware DRM](https://docs.microsoft.com/ko-kr/windows/uwp/audio-video-camera/hardware-drm) webpage for more information.
