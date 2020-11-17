---
linktitle: PallyCon Anti-Capture Guide
weight: 2

# Page metadata.
title: PallyCon Anti-Capture Guide
date: "2018-09-09T00:00:00Z"
lastmod: "2018-09-09T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: true  # Show table of contents? true/false
type: book  # Do not modify.

menu:
  anticapture:
    weight: 2
    parent: Other Products
---

## Overview

`PallyCon Anti-Capture` is a product that can monitor recording programs when playing Widevine DRM video in Windows Chrome browser.

This document explains how to download and install the Anti-Capture product, explains the process and how to apply it to the player.

## Product Components

`PallyCon Anti-Capture` product consists of a Chrome browser extension, a JavaScript API and a Windows service application.

- PallyConLib.js: JavaScript API to send and receive messages from Chrome extensions

- PallyCon AntiCapture extension: Chrome browser extension (installed from Chrome Web Store)

- PallyCon AntiCapture Service: Windows service based application

![Anti Capture Modules](/docs/images/pallycon-anti-capture-modules.png)

On the web page, the `PallyConLib.js` API sends a message to the extension and an anti-capture program installed on the PC. The anti capture program returns the results to the extension in the requested message, and the extension returns the results back to the web page.

For example, if you send a message such as whether anti-capture is installed, monitoring start / stop, etc., the anti-capture program will receive the message through the extension and return the result.

## Sample Files

`PallyCon Anti-Capture` product provides the following test samples. You can use the samples to understand the anti-capture feature and refer to it when developing the actual service.

- Library for sending and receiving messages with Chrome extensions
  - PallyConLib.js

- Library for playing DRM DASH contents
  - video.js, dash.all.min.js, videojs-dash.js

- Other libraries applied to the page
  - bootstrap.min.css, jquery.js, style.css, video-js.css

## Client Environments

Client environment required to apply PallyCon Anti Capture is as follows.

- Windows 7, Windows 8.1, Windows 10

- Chrome browser version 79 and later (latest version as of December 2019)

### Program Installation

The relevant program must be installed on the client system as follows:

- Chrome extension: Go to the Chrome Web Store and install the PallyCon AntiCapture extension in the Chrome browser.

- Service Application: Download and install AntiCaptureService_Setup.exe.

## Anti-Capture Monitoring Process

When you play an HTML5 video with Anti Capture in the above client environment, the following process is repeated continuously to monitor the screen recording.

![Anti Capture Process](/docs/images/pallycon-anti-capture-process.png)

1. The player playing the content calls the PallyConLib.js API to send a message to the extension.

2. The message is received by the Native Message Host and delivered to the Anti-Capture service installed on your PC.

3. The Anticapture service receives the message and returns the monitored result.

4. The native message host that receives the result passes it to the extension and raises an event if there is an issue.

## Preparing Test Environment

Node.js can be used to test `PallyCon Anti-Capture` locally.

1. Download and install Node.js.

2. Open a terminal, go to the deployed `sample` folder and execute the `npm install` command.
  - Modules to run the sample are downloaded and installed.

3. When the download is complete, execute the `node app` command. (See picture below)
  - If no module is found, install it by running `npm install module name`.
  - After installation is complete, run the `node app` command.

4. In your browser, call the URL `http://localhost:3000/index.htm`.

![Anti Capture Setup](/docs/images/pallycon-anti-capture-setup.png)

### Applying to Your Website

To apply PallyCon Anti-Capture to a website other than PallyCon's demo page or a local environment, the domain must first be registered in the PallyCon anti-capture chrome extension.

If you want to use PallyCon Anti-Capture product, please provide your domain to register through a separate contact.

> You must use an https URL for your player webpage instead of http. (Requires SSL / TSL application)

## PallyConLib.js Module

The `PallyConLib.js` API delivers messages to anti-capture programs installed on your PC through Chrome extensions. When the result is returned, the extension will again accept `PallyConLib.js` and process it.

The step-by-step API shown below must be applied to the webpage from staging content to end playback.

| Progress Steps | PallyConLib.js API | Description |
| --- | --- | --- |
| 1. Load Page | CheckInstall | When the page loads, check to see if AntiCapture is installed. |
| 2. Prepare to play | ActivateAntiCapture | Activate the anti-capture program installed before starting video playback. |
| 3. Start playing | StartMonitoring | Monitoring starts when the video is played. `PallyConLib.js` internally calls `CheckCaptureStatus` every 5 seconds to check whether the screen has been recorded. An event occurs when screen recording is detected. |
| 4. After playback ends | StopMonitoring | Monitoring stops when video playback is completed / stopped. |
| 5. Deactivate | DeactivateAntiCapture | If there is no longer content playback, deactivate the monitoring with the DeactivateAntiCapture API. |

### API Descriptions

|API Name | Description |
|---|---|
| CheckInstall | Check the extension to see if Anti-Capture is installed on your PC. If NOK, you need to disable video playback and install anti-capture program. |
| ActivateAntiCapture | Enables monitoring of anti-capture services installed on your PC through the extension. |
| DeactivateAntiCapture | Deactivates the monitoring of anti-capture services installed on your PC through the extension. |
| CheckCaptureStatus | Anticapture program checks the monitored information and returns the result. |
| SetMonitoringInterval | Change the monitoring interval of the Anti-Capture service. A cycle of 5 seconds or less is not recommended for heavy use of system resources. |
| Check the monitoring information continuously by calling the StartMonitoring | CheckCaptureStatus API every five seconds. Calling StartMonitoring without calling ActivateAntiCapture results in an error. |
| StopMonitoring | The CheckCaptureStatus API, which is called every 5 seconds, stops. Calling DeactivateAntiCapture before calling StopMonitoring results in an error. |

## Example

This is an example of applying `PallyConLib.js` to the `Video.js` player.

```js
<script type="text/javascript" src="PallyConCE.js"></script>
<script src="video.js"></script>
<script src="dash.all.min.js"></script>
<script src="videojs-dash.js"></script>

<script type="text/javascript">
    // PallyConLib.js event listener
    window.addEventListener("message", function (event) {
        if (event.source != window)
            return;

        if (event.data.type && (event.data.type == "PallyCon")) {
            if (event.data.isError) {
                if (typeof player != 'undefined') {
                    player.pause();
                }
                window.alert('[PallyCon] Extension Error : ' + event.data.msg);
            } else {
                console.log('[PallyCon] Extension Message : ' + event.data.msg);
            }
        }
    }, false);

    // 1. When the page loads, check if the Anti capture program is installed.
    function CheckInstall() {
        pal.CheckInstall();
    }

    var player;
    function playPlayer() {

        // 2. Enable Anti capture while the player is preparing or before playing.
        pal.ActivateAntiCapture();

        player = videojs('videoPlayer');
        player.ready(function () {
            player.src('DASH URL Source');
        }

        player.on('play', function () {
            // 3. When playback starts, begin continuous monitoring.
            pal.StartMonitoring();
        });

        player.on('pause', function () {
            // 4. When playback stops, end continuous monitoring.
            pal.StopMonitoring();
        });
        
        player.on('error', function () {
              // 5. If there is a problem with the player during playback, stop continuous monitoring and disable anticapture.
             pal.StopMonitoring();
             pal.DeactivateAntiCapture();
         });

        player.play().then(function (value) {
            console.log("[PallyCon] Successfully created player instance");
        });
    }
</script>
```

## Countermeasures against Issues

### Various Recording Programs

#### PC Screen Recorders

There are many recording programs, and new ones are coming out. In addition, client games or related programs may support recording.

For example, if you install Windows native XBox client, Steam program, NVIDIA / AMD driver, etc., the recording program will be installed together. In this case, the recording program or the related service should be terminated.

#### Chrome Extensions

There are many Chrome extensions support recording. If these extensions are installed in Chrome, the anticapture program will raise an event.

Even if the user doesn't record, if the screen capture extension is installed, the anti-capture program will trigger the event, so you can delete or disable the extension.

> Please report to us if you find a new recording program or if there is a recording program that is not registered in Anti-Capture program during the test.

### Security Issue

#### Obfuscation

Web applications have many security vulnerabilities due to code exposure and debugging. Therefore, once development is complete, basic protection is required by obfuscating code, and it is recommended to obfuscate files that call PallyConLib.js API.

> Recommendation: https://obfuscator.io/

***
