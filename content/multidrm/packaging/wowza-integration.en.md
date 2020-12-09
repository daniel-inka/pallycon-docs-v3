---
linktitle: Wowza Integration
weight: 50

# Page metadata.
title: Wowza Integration Guide
summary: PallyCon Wowza Integration SDK is an extension module of Wowza Streaming Engine that supports streaming service with DASH (CENC) and HLS (FPS) content.
date: "2018-09-09T00:00:00Z"
lastmod: "2020-10-23T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: true  # Show table of contents? true/false
type: book  # Do not modify.

menu:
  multidrm:
    weight: 50
    parent: Content Packaging
---

 {{< figure library="true" numbered="false" src="wowza-streaming-engine-logo.png" >}}

## Overview

PallyCon Wowza Integration SDK is an extension module of Wowza Streaming Engine that supports streaming service with DASH (CENC) and HLS (FPS) content by packaging original MP4 video or live stream in real time.

### Streaming protocol and DRM support

* DASH-CENC : Widevine Modular, PlayReady DRM
* HLS-AES : FairPlay Streaming DRM

> Unlike previous versions that use callback packaging, the latest version of the CPIX-based Wowza integration SDK does not support NCG DRM packaging. Please contact us if you need this feature.

PallyCon Wowza Integration SDK supports Wowza Streaming Engine 4.8 or later and recommends integration of storage and CDN service through Wowza Media Cache to improve streaming performance.

For more information on the Wowza Streaming Engine and a demonstration of the PallyCon Wowza Interaction SDK, please see the links below.

* Wowza Streaming Engine website : <https://www.wowza.com/products/streaming-engine>
* Wowza real time packaging demo : <https://www.pallycon.com/multi-drm-demo/>

This document describes how to apply the PallyCon Wowza Integration SDK after installing the Wowza Streaming Engine.

## Integration Workflow {#workflow}

1. Upload original content
 - Upload original MP4 content to storage associated with Wowza streaming server. For adaptive streaming, upload resolution-specific MP4 files and SMIL files.
 - If you are servicing live stream, set the live source for the Wowza application separately created for live stream.

2. Request for content playback
 - The client (HTML5 player or mobile application) requests streaming playback via a DASH (mpd) or HLS (m3u8) streaming URL.

3. Request for package key info
 - Request the key information needed for real-time packaging to the PallyCon cloud server. When requesting key information, MP4 file name or live stream name is transmitted for CID generation.
 - For adaptive streaming, key information is requested based on the SMIL file name.

4. Realtime Packaging
 - Package the original content in DASH or HLS format using the key information received from PallyCon cloud.

5. DRM License Issuance
 - The client asks PallyCon cloud server for the key needed to play the encrypted streaming content, and PallyCon cloud server sends the key information to the client verified through license management API.

## Setting Wowza Integration Module {#settings}

### Requirement

- JAVA version : jdk 9+

### Creating Wowza Application

Install the Wowza Streaming Engine on a server for live streaming.  Create the Wowza streaming application on the Applications tab after connecting to http://Wowza-server:8088/ on your browser. You can choose VoD or live applications. If you use both methods, you have to create and configure each application separately.

### Setting Libraries

Copy the files in the /lib folder of the unpacked PallyCon Wowza Interaction SDK to the [WOWZA_HOME]/lib/ folder.

> PallyCon Wowza Integration SDK file can be requested from PallyCon Console site when applying for commercial service and can be downloaded from the service information page after the request is approved.

```
- pallycon-cpix-common-2.0.0-jar-with-dependencies.jar
- pallycon-wowza-cpix-1.0.0.jar
- protobuf-java-2.6.1.jar
```

### Configuring Wowza Module

Add PallyCon module related setting in /[WOWZA_HOME]/conf/[APPLICATION]/Application.xml. 

#### Add property for FairPlay

Configure Wowza settings as below by referring to the Wowza guide. (https://www.wowza.com/docs/how-to-configure-apple-hls-packetization-cupertinostreaming)

```xml
...
<LiveStreamPacketizer>
        <Property>
	           <Name>cupertinoChunkDurationTarget</Name>
	            <Value>10000</Value>
	            <Type>Integer</Type>
        </Property>
        <Property>
	            <Name>cupertinoMaxChunkCount</Name>
	            <Value>10</Value>
	            <Type>Integer</Type>
        </Property>
        <Property>
	            <Name>cupertinoPlaylistChunkCount</Name>
	            <Value>3</Value>
	            <Type>Integer</Type>
        </Property>
        <Property>
	            <Name>cupertinoRepeaterChunkCount</Name>
	            <Value>-1</Value>
	            <Type>Integer</Type>
        </Property>
        <Property>
	            <Name>cupertinoCalculateChunkIDBasedOnTimecode</Name>
	            <Value>false</Value>
	            <Type>Boolean</Type>
        </Property>
</LiveStreamPacketizer>
```

#### Add HTTPStreamer property for FairPlay

```xml
 ...
 <HTTPStreamer>
    <Properties>
        <Property>
            <Name>cupertinoExtXVersion</Name>
            <Value>5</Value>
            <Type>Integer</Type>
        </Property>
    </Properties>
</HTTPStreamer>
```

#### Configure Session ID Option for FairPlay 

By default, Wowza Streaming Engine adds a streaming session ID to the encryption URI value in the HLS chunklist as shown below. 

```
e.g. sdk://content-id?wowzasessionid=30273096
```

For PallyCon FairPlay integration, you need to change the Wowza setting so that the session ID is not added to that value. Using the following guide, set the `cupertinoAppendQueryParamsToEncUrl` property to `false`.

- https://www.wowza.com/docs/how-to-control-streaming-session-id-appended-to-encryption-urls-in-chunklist-responses-cupertinoappendqueryparamstoencurl

#### Add module for Multi-DRM

```xml
<Modules>
    ...
    <Module>
        <!-- add property -->
        <!-- Settings for MPEG-DASH(Widevine, PlayReady), HLS(FairPlayStream) -->
        <Name>DrmModule</Name>
            <Descript>Multi DRM CPIX Module</Descript>
            <Class>com.pallycon.wowza.DrmModule</Class>
        <Description></Description>
	</Module>
</Modules>
```

#### Add property for Multi-DRM

```xml
<Properties>
    ...
    <!-- add property -->
    <Property>
        <Name>cupertinoEncryptionAPIBased</Name>
        <Value>true</Value>
        <Type>Boolean</Type>
    </Property>
    <Property>
        <Name>KmsUrl</Name>
        <!-- pallycon kms v1 url-->
        <Value>https://kms.pallycon.com/cpix/pallycon/getKey</Value>
    </Property>
     <Property>
     <!-- true : live key rotation / false : single key -->
        <Name>KeyRotation</Name>
        <Value>false</Value>
    </Property>
    <Property>
        <Name>PallyConEncToken</Name>
        <Value>{pallycon kms token}</Value>
    </Property>
</Properties>
```

### Restarting Application and Applying HTTPS

Restart the Wowza Application after completing the settings and it will work with the set information.

> Note: HTTPS must be applied to all streaming-related URLs, such as DASH (.mpd) or HLS (.m3u8) URLs. (enforced by browser)
