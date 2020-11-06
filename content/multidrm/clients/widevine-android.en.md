---
title: "Widevine Android SDK Guide"
date: 2018-08-28T16:12:37+09:00
draft: false
weight: 20
---

## Overview

PallyCon Widevine Android SDK makes it easy to apply Google's Widevine Modular DRM when developing media service apps for Android. This document describes how to use the libraries and sample project included in the SDK.

Details of PallyCon Multi DRM service integration are provided in [License Token Guide]({{%ref "license-token.en.md"%}}). For technical questions about using the SDK, please visit our [Helpdesk site](https://pallycon.zendesk.com).

> SDK products can be requested when subscribing to PallyCon cloud commercial service. Then SDK can be downloaded from the `Download` page of PallyCon Console site.

## Requirements

- Android version 4.4 (KitKat) or later. [AndroidX](https://developer.android.com/jetpack/androidx) library is used.
- This SDK supports ExoPlayer 2.10.4 version. (please contact us for older version support)

## Note

- This SDK has been tested on Android Studio 3.5 and Gradle 5.6. It will not work on the simulator.
- To develop application using the SDK, first sign up for PallyCon Console Site, and get `Site ID` and `Site Key` from the site.

## Tutorial Video

This video is a tutorial for playing DRM content using the sample project included in the SDK.

{{< youtube POAKSZqkpHk >}}

> For optimal playback, select '1080p' as the video quality and enable subtitle (Korean or English) before starting playback.

## Quick Start

You can apply PallyCon Widevine Android SDK to your development project by following these steps:

1. Extract downloaded SDK file.

1. Copy `PallyconWVMSDK.aar` file to `project/module/libs/` folder in your project.

1. Apply the below configuration in `build.gradle (project)`.

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
    // in the individual module build.gradle files  }  
    }  
  
    allprojects {  
        repositories {  
            jcenter()  
            google()  
        }  
    }
    ```

1. Apply the below configuration in `build.gradle (module)`.

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

1. Implement `PallyconDownloadTask.PallyconDownloadEventListener` in `MainActivity`.(refer to sample project)

    ```java
    private PallyconDownloadTask.PallyconDownloadEventListener eventListener = new PallyconDownloadTask.PallyconDownloadEventListener() {
        @Override
        public void onPreExecute() {
            // called on preparing download
        }

        @Override
        public void onPostExecute() {
            // called on download complete
        }

        @Override
        public void onProgressUpdate(String fileName, long downloadedSize, long totalSize, int percent, int downloadIndex) {
            // called on progress update : refer to API document
        }

        @Override
        public void onCancelled() {
            // called on canceling download : refer to API document
        }

        @Override
        public void onNetworkError(NetworkConnectedException e) {
            // called on network error : refer to API document
        }

        @Override
        public void onPallyconDownloadError(PallyconDownloadException e) {
            // called on download error : refer to API document
        }
    };
    ```

1. To use the download callback, implement the callback function and enter it into the `PallyconDownloadTask` constructor. If you do not use callback, enter `null`.

    ```java
    DownloadCallbackImpl downloadCallback = new DownloadCallbackImpl(getApplicationContext());
    ```

1. Create `PallyconDownloadTask` object with content information.

    ```java
    downloadTask = new PallyconDownloadTask(MainActivity.this, content.uri, content.name, listener, eventHandler);
    ```

1. Start downloading content if the content is not previously downloaded.

    ```java
    boolean result = downloadTask.isDownloadCompleted();
    if(result == true) {
        Uri localUri = downloadTask.getLocalUri(content.uri, content.name);
    } else {
        downloadTask.execute();	
    }
    ```

1. After the download is complete, you can issue a license using the following APIs before playback

    ```java
    WVMAgent.downloadLicense(content.drmSchemeUuid, content.drmLicenseUrl, content.uri, content.userId, content.cid, content.oid);
    ```

1. Implement player in `PlayerActivity.java` using [ExoPlayer development guide](http://google.github.io/ExoPlayer/guide.html).

1. Implement `ExoPlayer.EventListener`. (refer to sample project)

    ```java
    private ExoPlayer.EventListener playerEventListener = new ExoPlayer.EventListener() {
        @Override
        public void onTimelineChanged(Timeline timeline, Object manifest){
            //  refer to API document & sample source
        }

        @Override
        public void onTracksChanged(TrackGroupArray trackGroups, TrackSelectionArray trackSelections) {
            //  refer to API document & sample source
        }

        @Override
        public void onLoadingChanged(boolean isLoading) {
            //  refer to API document & sample source
        }

        @Override
        public void onPlayerStateChanged(boolean playWhenReady, int playbackState) {
            //  refer to API document & sample source
        }

        @Override
        public void onPlayerError(ExoPlaybackException e) {
            //  refer to API document & sample source
        }

        @Override
        public void onPositionDiscontinuity() {
            //  refer to API document & sample source
        }
    };
    ```

1. Implement `PallyconStreamingDrmSessionManager.PallyconEventListener`. (refer to sample project)

    ```java
    private PallyconStreamingDrmSessionManager.PallyconEventListener eventListener = new PallyconStreamingDrmSessionManager.PallyconEventListener() {
        @Override
        public void onDrmKeysLoaded(Map<String, String> licenseInfo) {
            // refer to API document
        }

        @Override
        public void onDrmSessionManagerError(Exception e) {
            // refer to API document
            if (e instanceof NetworkConnectedException) {

            } else if (e instanceof PallyconDrmException) {

            } else if (e instanceof PallyconEncrypterException) {

            } else if (e instanceof PallyconServerResponseException) {

            } else if (e instanceof KeysExpiredException) {

            } else if (e instanceof DetectedDeviceTimeModifiedException) {
                // Important : content playback should be prohibited to prevent illegal use of content.
            } else {

            }
        }
    };
    ```

1. Get `PallyconWVMSDK` instance.

    ```java
    PallyconWVMSDK WVMAgent = PallyconWVMSDKFactory.getInstance(this, Global.enableNcgCenc);
    ```

1. Set `Site ID` and `Site Key` issued by PallyCon Console Site to `PallyconWVMSDK` instance.

    ```java
    WVMAgent.init(this, eventHandler, "SITEID", "SITEKEY");
    ```

1. Register a listener to receive events from the object of `PallyconWVMSDK`.

    ```java
    WVMAgent.setPallyconEventListener(pallyconEventListener);
    ```

1. Create `PallyconDrmSessionManager` with information for license acquisition.

    ```java
    drmSessionManager = WVMAgent.createDrmSessionManager(drmSchemeUuid, drmLicenseUrl, uri, userId, cid, oid);
    ```

    > If you use license token type integration, set the token information as below.
    ```java
    drmSessionManager = WVMAgent.createDrmSessionManagerByToken(drmSchemeUuid, drmLicenseUrl, uri, userId, cid, token);
    ```

1. Input the above `drmSessionManager` as a parameter when creating ExoPlayer

    ```java
    player = ExoPlayerFactory.newSimpleInstance(this, trackSelector, new DefaultLoadControl(), drmSessionManager);
    ```

1. Input the `playerEventListener` when registering Player Listener as well.

    ```java
    player.addListener(playerEventListener);
    ```

## **Preventing screen recording**

> To prevent content leaks with screen recording apps, you should block the capture function by adding the following code to your application:
> ```java
> SurfaceView view = (SurfaceView)simpleExoPlayerView.getVideoSurfaceView();
> view.setSecure(true);
> ```

## API References

 > Please refer to the doc/en/api_reference.html file of the SDK zip file for detailed explanation of each API.

***
