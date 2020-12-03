---
title: PlayReady UWP SDK 가이드
linktitle: PlayReady UWP SDK
summary: 본 문서는 PallyCon PlayReady UWP SDK에 포함된 라이브러리와 샘플 프로젝트의 사용법을 설명합니다.
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
lastmod: "2020-09-16T00:00:00Z"
draft: false

menu:
  multidrm:
    parent: 클라이언트 연동
    weight: 50

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 50
---

## 개요

**PallyCon PlayReady UWP SDK**는 Universal Windows Platform (UWP) 환경에서 미디어 서비스 앱을 개발할 때 Microsoft사의 PlayReady DRM을 쉽게 적용할 수 있게 해주는 제품입니다. 본 문서는 PallyCon PlayReady UWP SDK에 포함된 라이브러리와 샘플 프로젝트의 사용법을 설명합니다.

SDK와 연동되는 PallyCon 멀티 DRM 서비스에 대한 설명은 [라이선스 토큰 가이드](../../license/license-token)를 참고하시기 바랍니다.
SDK 사용과 관련한 기술 문의는 [헬프데스크](https://pallycon.zendesk.com) 사이트를 이용해 주시기 바랍니다.

## 지원 환경

- Windows 10 이상 (OS 빌드 10586 이상)
- XBox One 10 이상

## 확인 사항

- 제공되는 SDK는 `Visual Studio 2017(버전 15)` 에서 개발 및 테스트 되었습니다.
- SDK 샘플 프로젝트에서 재생 테스트하려면 [PallyCon 콘솔 사이트](https://console.pallycon.com)에 가입하여 `Site ID`, `Site Key` 발급과 PallyCon Packager로 암호화된 DASH 컨텐츠가 필요합니다.
- **위 조건이 갖춰지지 않으면 PallyCon PlayReady SDK Sample로 DASH 콘텐츠 재생이 불가합니다.**

## 프로젝트 설정

다음과 같은 과정으로 PallyCon PlayReady UWP SDK를 개발 프로젝트에 추가할 수 있습니다.

1. PallyCon 콘솔 사이트에서 SDK zip 파일을 다운로드 받아 압축을 풉니다.
2. `lib` 폴더에서 제공된 `PallyConPRSDK.dll` 라이브러리를 구현하는 프로젝트 참조에 추가합니다.
3. `using PallyConPRSDK;`, `using PallyConSDK.DownloadTask;` `using PallyConSDK.DownloadTask.ProxyServer;` 를 선언하여 구현합니다.

## 퀵 스타트

PallyCon PlayReady UWP SDK를 이용하여 DASH 콘텐츠를 스트리밍 재생하거나 다운로드하는 방법은 아래 코드와 같습니다.

### 콘텐츠 재생

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
        public string LicenseURL = "License Acquisition URL";
        public string Token = "Token String";

        public MainPage()
        {
            this.InitializeComponent();
            try
            {
            	// 3. Site ID, Site Key 입력
                pallyconsdk.Initialize("Site ID", "Site Key");
                // 4. MediaElement에 ProtectionManager 설정
                // Reactive 또는 Proactive 컨텐츠에 따라 isProactive 파라미터를 설정. 기본: false(Reactive).
                Player.ProtectionManager = pallyconsdk.CreateProtectionManagerByToken(ContentURL, LicenseURL, Token, false);
                // 5. 재생
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

### 콘텐츠 다운로드 및 재생

원격지에 있는 DASH 콘텐츠를 로컬에 다운로드하여 재생할 수 있습니다.

> **NOTE:** 
> * 다운로드된 콘텐츠를 재생하는 경우에도 라이선스 획득을 위해 반드시 네트워크에 연결되어 있어야 합니다. (오프라인 재생 불가)

***
MainPage.xaml.cs

~~~Cs
// MainPage.xaml.cs
// 1. PallyConPRSDK 선언
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
            	// 3. Site ID, Site Key 입력
                pallyconsdk.Initialize("Site ID", "Site Key");
                // 4. Proxy Server 실행
                proxyServer = pallyconsdk.CreateProxyServer();
                proxyServer.Initialize();
                // 5. 다운로드 컨텐츠 정보 입력
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
            // 6. MediaElement에 ProtectionManager 설정
            // Reactive 또는 Proactive 컨텐츠에 따라 isProactive를 설정(기본 Reactive)
            Player.ProtectionManager = pallyconsdk.CreateProtectionManagerByToken(ContentURL, LicenseURL, Token, false);
            // 7. Proxy Server로 재생 URL 획득
            Uri localMpdUrl = proxyServer.GetContentUri(contentName);
            // 8. 자막 (sample code 참조)
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
            // 9. 재생
            Player.Source = new Uri(localMpdUrl);
        }

        private void DownloadFail(string contentName, string failedFileUrl, HttpResponseMessage response)
        {
        	Debug.WriteLine("Content Name: {0} Folder Uri: {1}, StatusCode: {2}", contentName, failedFileUrl, response.StatusCode);
        }
    }
}
~~~

## PlayReady 하드웨어 DRM

하드웨어 기반 PlayReady DRM은 Windows 10 PC와 스마트TV, XBOX 콘솔, 태블릿 등의 다양한 디바이스에서 지원됩니다. Windows PC가 PlayReady 하드웨어 DRM을 지원하려면 Windows 10을 실행하고 있어야 하고 지원되는 하드웨어 구성을 포함해야 합니다. 하드웨어 DRM에 대한 보다 자세한 사항은 [Microsoft PlayReady Hardware DRM](https://docs.microsoft.com/ko-kr/windows/uwp/audio-video-camera/hardware-drm)에서 확인하시기 바랍니다.
