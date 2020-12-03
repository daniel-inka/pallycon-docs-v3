---
title: Widevine AndroidTV SDK Guide
linktitle: Widevine AndroidTV SDK
summary: This document describes how to use the libraries and sample project included in the SDK.
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
lastmod: "2020-09-16T00:00:00Z"
draft: false

menu:
  multidrm:
    parent: Client Integration
    weight: 80

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 80
---

## Overview

PallyCon Widevine Android TV SDK makes it easy to apply Google's Widevine Modular DRM when developing media service apps for Android TV platform. This document describes how to use the libraries and sample project included in the SDK.

Details of PallyCon Multi DRM service integration are provided in [License Token Guide](../../license/license-token). For technical questions about using the SDK, please visit our [Helpdesk site](https://pallycon.zendesk.com).

> SDK products can be requested when subscribing to PallyCon cloud commercial service. Then SDK can be downloaded from the `Download` page of PallyCon Console site.

## Requirements

- Android version 5.0 or later
- This SDK supports ExoPlayer 2.8.2 version. (for older versions such as 2.3.0 ~ 2.8.1, please contact us)

## Note

- This SDK has been tested on Android Studio 3.1.2 and Gradle 4.4. It will not work on the simulator.
- To develop application using the SDK, first sign up for PallyCon Console Site, and get `Site ID` and `Site Key` from the site.

## Quick Start

You can apply PallyCon Widevine Android TV SDK to your development project by following these steps:

1. Create Android TV module : Android Studio -> File -> New -> New Module

1. Extract downloaded SDK file.

1. Copy `PallyconWVMSDK.jar` file to `project/module/libs/` folder in your project.

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
            classpath 'com.android.tools.build:gradle:3.1.2'  
  
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

1. Apply the below configuration in `build.gradle(module)`.

    ```java 
    android {
        defaultConfig {
            minSdkVersion 21
            targetSdkVersion 26
            multiDexEnabled true
        }
    }
    dependencies {
        implementation fileTree(dir: 'libs', include: ['*.jar'])  
        implementation fileTree(dir: 'libs', include: ['*.aar'])  
        implementation 'com.android.support:leanback-v17:26.1.0'  
        implementation 'com.android.support:appcompat-v7:26.1.0'  
        implementation 'com.github.bumptech.glide:glide:3.7.0'  
        implementation 'com.google.android.exoplayer:exoplayer:2.8.2'  
        implementation group: 'org.bouncycastle', name: 'bcprov-jdk15on', version: '1.59'  
        implementation group: 'org.bouncycastle', name: 'bcpkix-jdk15on', version: '1.59'
    } 
    ```

1. Implement `PallyconDownloadTask.PallyconDownloadEventListener` in `VideoDetailsFragment`. (refer to sample project)

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

1. Create `PallyconDownloadTask` object with content information.

    ```java
    downloadTask = new PallyconDownloadTask(getActivity(), Uri.parse(mSelectedMovie.uri), mSelectedMovie.name, VideoDetailsFragment.this, eventHandler);
    ```

1. Start downloading content if the content is not previously downloaded.

    ``` java
    Intent intent = new Intent(getActivity(), PlaybackOverlayActivity.class);
    intent.putExtra(DetailsActivity.MOVIE, mSelectedMovie);
    boolean result = downloadTask.isDownloadCompleted();
    if(result == true) {
        startActivity(intent);
    } else {
        downloadTask.execute();
    }
    ```

1. After the download is complete, you can issue a license using the following APIs before playback.

    ```java
    WVMAgent.downloadLicense(mSelectedMovie.drmSchemeUuid, mSelectedMovie.drmLicenseUrl, Uri.parse(mSelectedMovie.uri), mSelectedMovie.userId, mSelectedMovie.cid, mSelectedMovie.oid);
    ```

1. Implement player in `PlaybackOverlayActivity.java` using [ExoPlayer development guide](http://google.github.io/ExoPlayer/guide.html).

1. Implement `ExoPlayer.EventListener`. (refer to sample project)

    ```java
    private ExoPlayer.EventListener playerEventListener = new ExoPlayer.EventListener() {
        @Override
        public void onTimelineChanged(Timeline timeline, Object manifest) {
            // refer to API document and sample source
        }

        @Override
        public void onTracksChanged(TrackGroupArray trackGroups, TrackSelectionArray trackSelections) {
            // refer to API document and sample source
        }

        @Override
        public void onLoadingChanged(boolean isLoading) {
            // refer to API document and sample source
        }

        @Override
        public void onPlayerStateChanged(boolean playWhenReady, int playbackState) {
            // refer to API document and sample source
        }

        @Override
        public void onPlayerError(ExoPlaybackException e) {
            // refer to API document and sample source
        }

        @Override
        public void onPositionDiscontinuity() {
            // refer to API document and sample source
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

1. Implement `PlaybackOverlayFragment.OnPlayPauseClickedListener`.

    ```java
    if (position == 0 || mPlaybackState == LeanbackPlaybackState.IDLE) {
        initializePlayer(movie);
        mPlaybackState = LeanbackPlaybackState.IDLE;
    }
    if (playPause && mPlaybackState != LeanbackPlaybackState.PLAYING) {
        mPlaybackState = LeanbackPlaybackState.PLAYING;
        if (position > 0) {
            player.seekTo(position);
            player.setPlayWhenReady(true);
        }
    } else {
        mPlaybackState = LeanbackPlaybackState.PAUSED;
        player.setPlayWhenReady(false);
    }
    ```

1. When `onKeyUp` event is called, add communication with `PlaybackOverlayFragment`.

    ```java
    PlaybackOverlayFragment playbackOverlayFragment = (PlaybackOverlayFragment) getFragmentManager().findFragmentById(R.id.playback_controls_fragment);
    switch (keyCode) {
        case KeyEvent.KEYCODE_MEDIA_PLAY:
            playbackOverlayFragment.togglePlayback(false);
            return true;
        case KeyEvent.KEYCODE_MEDIA_PAUSE:
            playbackOverlayFragment.togglePlayback(false);
            return true;
        case KeyEvent.KEYCODE_MEDIA_PLAY_PAUSE:
            if (mPlaybackState == LeanbackPlaybackState.PLAYING) {
                playbackOverlayFragment.togglePlayback(false);
            } else {
                playbackOverlayFragment.togglePlayback(true);
            }
            return true;
        default:
            return super.onKeyUp(keyCode, event);
    }
    ```

1. Get `PallyconWVMSDK` instance in `initializePlayer()`.

    ```java
    PallyconWVMSDK WVMAgent = PallyconWVMSDKFactory.getInstance(this);
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

1. Input the above `drmSessionManager` as a parameter when creating ExoPlayer.

    ```java
    player = ExoPlayerFactory.newSimpleInstance(this, trackSelector, new DefaultLoadControl(), drmSessionManager);
    ```

1. Input the `playerEventListener` when registering Player Listener as well.

    ``` java
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
