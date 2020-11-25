---
title: Widevine Android SDK 가이드
linktitle: Widevine Android SDK
summary: 본 문서는 Widevine Android SDK에 포함된 라이브러리와 샘플 프로젝트의 사용법에 대해 설명합니다.
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
lastmod: "2020-09-16T00:00:00Z"
draft: false
featured: true

menu:
  multidrm:
    parent: 클라이언트 연동
    weight: 20

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 20
---

## 개요

PallyCon Widevine Android SDK는 안드로이드 OS용 미디어 서비스 앱을 개발할 때 구글의 Widevine Modular DRM을 쉽게 적용할 수 있게 해주는 제품입니다. 본 문서는 SDK에 포함된 라이브러리와 샘플 프로젝트의 사용법에 대해 설명합니다.

Widevine 클라이언트와 연동되는 PallyCon 멀티 DRM 서비스에 대한 설명은 [라이선스 토큰 가이드](../../license/license-token)를 참고하시기 바랍니다.
SDK 사용과 관련한 기술 문의는 [헬프데스크](https://pallycon.zendesk.com) 사이트를 이용해 주시기 바랍니다.

> SDK 제품은 PallyCon 클라우드 상용 서비스 가입 시 신청할 수 있으며, 신청한 SDK는 PallyCon 콘솔 사이트의 `다운로드` 페이지에서 다운로드 받을 수 있습니다.

## 지원 환경

- 안드로이드 4.4 버전(KitKat) 이상, [AndroidX](https://developer.android.com/jetpack/androidx) 라이브러리 사용
- 본 SDK는 ExoPlayer 2.10.4 버전을 지원합니다. (기타 버전에 대한 지원은 별도 문의 바랍니다.)

## 확인 사항

- 본 SDK는 Android Studio 3.5와 Gradle 5.6 버전에서 테스트 되었으며, 에뮬레이터에서는 동작하지 않습니다.
- SDK를 이용한 개발을 위해서는 우선 PallyCon 콘솔 사이트에 가입하여 Site ID와 Site Key를 발급받아야 합니다.

## 동영상 튜토리얼

SDK에 포함된 샘플 프로젝트를 사용하여 DRM 콘텐츠를 재생하는 튜토리얼 영상입니다.

{{< youtube POAKSZqkpHk >}}

> 최적의 재생을 위해 화면 품질을 '1080p'로 선택하고 자막(한글 또는 영문)을 선택하여 재생하시기 바랍니다.

## 퀵 스타트

다음과 같은 과정으로 PallyCon Widevine 안드로이드 SDK를 개발 프로젝트에 추가할 수 있습니다.

1. 다운로드 받은 SDK 파일의 압축을 해제합니다.

1. libs 폴더의 `PallyconWVMSDK.aar` 파일을 작업 중인 프로젝트의 `project/module/libs/` 폴더에 복사합니다.

1. `build.gradle(project)`에 다음의 사항을 반영합니다.

    ```java
    buildscript { 
        repositories {  
            jcenter()  
            google()  
            flatDir {  
		        dirs 'libs'  
		    }
        }  
        dependencies {  
            classpath 'com.android.tools.build:gradle:3.2.1'  
            // NOTE: Do not place your application dependencies here; they belong  
            // in the individual module build.gradle files  
        }  
    }  
 
    allprojects {  
        repositories {  
            jcenter()  
            google()  
        }  
    }
    ```

1. `build.gradle(module)`에 다음의 사항을 반영합니다.

    ```java
    android {
        defaultConfig {
            minSdkVersion 19
            targetSdkVersion 28
            multiDexEnabled true
        }

        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
    }

    dependencies {
        implementation fileTree(dir: 'libs', include: ['*.jar'])
        implementation fileTree(dir: 'libs', include: ['*.aar'])
        implementation 'androidx.appcompat:appcompat:1.1.0'
        implementation 'androidx.vectordrawable:vectordrawable-animated:1.1.0'
        implementation 'androidx.recyclerview:recyclerview:1.0.0'
        implementation 'androidx.legacy:legacy-support-v4:1.0.0'
        implementation 'androidx.mediarouter:mediarouter:1.1.0'
        implementation 'com.google.android.gms:play-services-cast-framework:17.1.0'
        implementation 'com.google.android.exoplayer:exoplayer:2.10.4'
    }
    ```

1. `MainActivity` 에 `PallyconDownloadTask.PallyconDownloadEventListener`를 구현합니다. (샘플 소스 참조)

    ```java
    private PallyconDownloadTask.PallyconDownloadEventListener eventListener = new PallyconDownloadTask.PallyconDownloadEventListener() {
        @Override
        public void onPreExecute() {
            // 다운로드 준비
        }

        @Override
        public void onPostExecute() {
            // 다운로드 완료
        }

        @Override
        public void onProgressUpdate(String fileName, long downloadedSize, long totalSize, int percent, int downloadIndex) {
            // 화면 업데이트 : API 레퍼런스 문서를 참고해 주십시오.
        }

        @Override
        public void onCancelled() {
            // 다운로드 취소시 : API 레퍼런스 문서를 참고해 주십시오.
        }

        @Override
        public void onNetworkError(NetworkConnectedException e) {
            // 네트워크 오류시 : API 레퍼런스 문서를 참고해 주십시오.
        }

        @Override
        public void onPallyconDownloadError(PallyconDownloadException e) {
            // 다운로드 오류시 : API 레퍼런스 문서를 참고해 주십시오.
        }
    };
    ```
1. 다운로드 콜백을 사용하려면 callback 함수를 구현하여 `PallyconDownloadTask` 생성자에 입력하고, 콜백을 사용하지 않으면 `null` 을 입력합니다.

    ```java
    DownloadCallbackImpl downloadCallback = new DownloadCallbackImpl(getApplicationContext());
    ```

1. 재생할 콘텐츠 정보를 넣어 `PallyconDownloadTask` 객체를 생성합니다.

    ```java
    downloadTask = new PallyconDownloadTask(MainActivity.this, content.uri, content.name, MainActivity.this, eventHandler, downloadCallback);
    ```

1. 콘텐츠가 이미 다운로드 되어 있는지 확인 후 다운로드를 실행합니다.

    ```java
    boolean result = downloadTask.isDownloadCompleted();
    if(result == true) {
        Uri localUri = downloadTask.getLocalUri(content.uri, content.name);
    } else {
        downloadTask.execute();
    }
    ```

1. 다운로드가 완료된 후 재생 전에 다음의 API를 사용하여 라이센스를 발급 받을 수 있습니다.

    ```java
    WVMAgent.downloadLicense(content.drmSchemeUuid, content.drmLicenseUrl, content.uri, content.userId, content.cid, content.oid);
    ```

1. `PlayerActivity.java` 에 [ExoPlayer 개발 가이드](http://google.github.io/ExoPlayer/guide.html)를 참고해 플레이어를 구현합니다.

1. `ExoPlayer.EventListener`를 구현합니다. (샘플 소스 참조)

    ```java
    private ExoPlayer.EventListener playerEventListener = new ExoPlayer.EventListener() {
        @Override
        public void onTimelineChanged(Timeline timeline, Object manifest) {
            // API 레퍼런스 문서 및 샘플 소스를 참고해 주십시오.
        }

        @Override
        public void onTracksChanged(TrackGroupArray trackGroups, TrackSelectionArray trackSelections) {
            // API 레퍼런스 문서 및 샘플 소스를 참고해 주십시오.
        }

        @Override
        public void onLoadingChanged(boolean isLoading) {
            // API 레퍼런스 문서 및 샘플 소스를 참고해 주십시오.
        }

        @Override
        public void onPlayerStateChanged(boolean playWhenReady, int playbackState) {
            // API 레퍼런스 문서 및 샘플 소스를 참고해 주십시오.
        }

        @Override
        public void onPlayerError(ExoPlaybackException e) {
            // API 레퍼런스 문서 및 샘플 소스를 참고해 주십시오.
        }

        @Override
        public void onPositionDiscontinuity() {
            // API 레퍼런스 문서 및 샘플 소스를 참고해 주십시오.
        }
    };
    ```

1. `pallyconEventListener`를 구현합니다. (샘플 소스 참조)

    ```java
    private PallyconEventListener pallyconEventListener= new PallyconEventListener() {
        @Override
        public void onDrmKeysLoaded(Map<String, String> licenseInfo) {
            // API 레퍼런스 문서 및 샘플 소스를 참고해 주십시오.
        }

        @Override
        public void onDrmSessionManagerError(Exception e) {
            // API 레퍼런스 문서 및 샘플 소스를 참고해 주십시오.
            if (e instanceof NetworkConnectedException) {

            } else if (e instanceof PallyconDrmException) {

            } else if (e instanceof PallyconEncrypterException) {

            } else if (e instanceof PallyconServerResponseException) {

            } else if (e instanceof KeysExpiredException) {

            } else if (e instanceof DetectedDeviceTimeModifiedException) {
                // 중요: 콘텐츠의 불법적인 사용을 막기 위해 콘텐츠 재생을 금지해야 합니다.
            } else {

            }
        }
    };
    ```

1. `PallyconWVMSDK` 객체를 가져옵니다.

    ```java
    WVMAgent = PallyconWVMSDKFactory.getInstance(this, Global.enableNcgCenc);
    ```

1. `PallyconWVMSDK` 객체에 PallyCon 콘솔 사이트에서 확인한 `Site ID`와 `Site Key`를 설정합니다.

    ```java
    WVMAgent.init(this, eventHandler, "SITEID", "SITEKEY");
    ```

1. `PallyconWVMSDK` 객체에서 전달되는 이벤트를 받을 리스너를 등록합니다.

    ```java
    WVMAgent.setPallyconEventListener(pallyconEventListener);
    ```

1. 라이센스 획득에 필요한 정보를 포함하여 `PallyconDrmSessionManager`를 생성합니다.

    ```java
    drmSessionManager = WVMAgent.createDrmSessionManager(drmSchemeUuid, drmLicenseUrl, uri, userId, cid, oid);
    ```

    > 라이선스 토큰을 사용하는 경우, 별도로 획득한 토큰 정보를 설정합니다.
    ```java
    drmSessionManager = WVMAgent.createDrmSessionManagerByToken(drmSchemeUuid, drmLicenseUrl, uri, userId, cid, token);
    ```

1. ExoPlayer를 생성할 때 위 과정에서 획득한 `drmSessionManager`를 입력합니다.

    ```java
    player = ExoPlayerFactory.newSimpleInstance(this, trackSelector, new DefaultLoadControl(), drmSessionManager);
    ```

1. `Player Listener`를 등록할 때 위 과정에서 획득한 `playerEventListener`를 입력합니다.

    ```java
    player.addListener(playerEventListener);
    ```

## **동영상 레코딩 차단**

> 화면 녹화 앱을 이용한 콘텐츠 유출을 방지하려면, 어플리케이션에 다음 코드를 추가해 캡쳐 기능을 차단해야 합니다.
> ```java
> SurfaceView view = (SurfaceView)simpleExoPlayerView.getVideoSurfaceView();
> view.setSecure(true);
> ```

## API 레퍼런스 문서

> SDK에서 제공하는 API들에 대한 상세 설명은 SDK 파일에 포함된 API 레퍼런스 문서(doc/ko/api_reference.html)를 참고하시기 바랍니다.
