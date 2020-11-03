---
linktitle: Wowza Interation
weight: 5

# Page metadata.
title: Wowza Integration Guide
date: "2018-09-09T00:00:00Z"
lastmod: "2018-09-09T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: true  # Show table of contents? true/false
type: book  # Do not modify.

menu:
  multidrm:
    weight: 5
    parent: Content Packaging
---

![wowza-logo](/docs/images/wowza-streaming-engine-logo.png)

## Overview

PallyCon Wowza Integration SDK is an extension module of Wowza Streaming Engine that supports streaming service with DASH (CENC) and HLS (FPS or NCG) content by packaging original MP4 video or live stream in real time.

### Streaming protocol and DRM support

* DASH-CENC : Widevine Modular, PlayReady DRM
* HLS-AES : FairPlay Streaming DRM
* HLS-AES : NCG DRM

PallyCon Wowza Integration SDK supports Wowza Streaming Engine 4.5 or later and recommends interworking of storage and CDN service through Wowza Media Cache to improve streaming performance.

For more information on the Wowza Streaming Engine and a demonstration of the PallyCon Wowza Interaction SDK, please see the links below.

* Wowza Streaming Engine website : <https://www.wowza.com/products/streaming-engine>
* Wowza real time packaging demo : <https://www.pallycon.com/multi-drm-demo/>

This document describes how to apply the PallyCon Wowza Integration SDK after installing the Wowza Streaming Engine.

> In streaming service using Wowza, CID issuance of contents is done through packaging callback page. Please contact us for the related specification.

## Integration Workflow {#workflow}

![wowza-integration](/docs/images/wowza-integration-workflow.png)

1. Upload original content
 - Upload original MP4 content to storage associated with Wowza streaming server. For adaptive streaming, upload resolution-specific MP4 files and SMIL files.
 - If you are servicing live stream, set the live source for the Wowza application separately created for live stream.

2. Request for content playback
 - The client (HTML5 player or mobile application) requests streaming playback via a DASH (mpd) or HLS (m3u8) streaming URL.

3. Request for package key info
 - Request the key information needed for real-time packaging to the PallyCon cloud server. When requesting key information, MP4 file name or live stream name is transmitted for CID generation.
 - For adaptive streaming, key information is requested based on the SMIL file name.

4. CID Issuance
 - If there is no key information in the PallyCon Key DB, the packaging callback is called to get the CID of the content.

5. Realtime Packaging
 - Package the original content in DASH or HLS format using the key information received from PallyCon cloud.

6. DRM License Issuance
 - The client asks PallyCon cloud server for the key needed to play the encrypted streaming content, and PallyCon cloud server sends the key information to the client verified through license management API.

## Setting Wowza Integration Module {#settings}

### Creating Wowza Application

Install the Wowza Streaming Engine on a server for live streaming.  Create the Wowza streaming application on the Applications tab after connecting to http://Wowza-server:8088/ on your browser. You can choose VoD or live applications. If you use both methods, you have to create and configure each application separately.

> Note: If you are applying both Multi DRM (PlayReady, Widevine, FPS) and NCG DRM, you must create and configure each Wowza application separately.

### Setting Libraries

Copy the files in the /lib folder of the unpacked PallyCon Wowza Interaction SDK to the [WOWZA_HOME]/lib/ folder.

> PallyCon Wowza Integration SDK file can be requested from PallyCon Console site when applying for commercial service and can be downloaded from the service information page after the request is approved.

```
- PallyConMultiDrmModule.jar
- NetsyncModule.jar
- not-yet-commons-ssl-0.3.9.jar
- commons-logging-1.1.1.jar
- httpclient-4.2.5.jar
- httpcore-4.2.4.jar
```

### Setting Packager Key

Copy the pallycon_pack.key file in the /conf folder of the unpacked PallyCon Wowza Integration SDK to the [/WOWZA_HOME]/conf/[APPLICATION] folder.

### Configuring Wowza Module

Add PallyCon module related setting in /[WOWZA_HOME]/conf/[APPLICATION]/Application.xml. Wowza application for Multi DRM (PlayReady, Widevine, FPS) and NCG DRM application should be separately created and set up as below.

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
    <!-- Settings for MPEG-DASH(Widevine, PlayReady), HLS(FairPlayStream) -->
    <Module>
        <Name>MultiDrmModule</Name>
        <Description>Wowza MultiDrmModule</Description>
        <Class>com.inka.wms.multidrmmodule.MultiDrmModule</Class>
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
        <Name>SiteID</Name>
        <Value>[SITEID]</Value>
    </Property>
    <Property>
        <Name>AccessKey</Name>
        <Value>[ACCESSKEY]</Value>
    </Property>
    <Property>
        <Name>PrivateKeyPath</Name>
        <Value>/conf/[APPLICATION]/pallycon_pack.key</Value>
    </Property>
    <!-- Settings for MPEG-DASH(Widevine, PlayReady), HLS(FairPlayStream) -->
    <!-- playready license manager-->
    <Property>
        <Name>LicenseManagerUrl</Name>
        <Value>https://license.pallycon.com/ri/playready/licenseManager.do</Value>
    </Property>
    <!-- multidrm pack manager-->
    <Property>
    <Name>PackManagerUrl</Name>
        <Value>https://license.pallycon.com/ri/packageManager.do</Value>
    </Property>
</Properties>
```

#### Add module for NCG DRM

```xml
<Modules>
    ...
    <!-- Settings for NCG(HLS) -->
    <Module>
        <Name>NetsyncModule</Name>
        <Description>inka Module</Description>
        <Class>com.inka.wms.module.NetsyncModule</Class>
    </Module>
</Modules>
```

#### Add property for NCG DRM

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
        <Name>SiteID</Name>
        <Value>[SITEID]</Value>
    </Property>
    <Property>
        <Name>AccessKey</Name>
        <Value>[ACCESSKEY]</Value>
    </Property>
    <Property>
        <Name>PrivateKeyPath</Name>
        <Value>/conf/[APPLICATION]/pallycon_pack.key</Value>
    </Property>
    <!-- Settings for NCG(HLS) -->
    <!-- hls pack manager-->
    <Property>
        <Name>HLSKeyManagerUrl</Name>
        <Value>https://license.pallycon.com/ri/packageManager.do</Value>
    </Property>
    <!-- packager-->
    <Property>
        <Name>HLSKeyPackUrl</Name>
        <Value>https://license.pallycon.com/ri/hlskeyPackager.do</Value>
    </Property>
</Properties>
```

##### Input Value Description

|Name|Value|
|---|---|
|SITEID|Enter the service site ID (4 bytes) issued by PallyCon cloud|
|ACCESSKEY|Access Key value issued when creating PallyCon Cloud service site|
|APPLICATION|Name of Wowza application|

### Restarting Application and Applying HTTPS

Restart the Wowza Application after completing the settings and it will work with the set information.

> Note: HTTPS must be applied to all streaming-related URLs, such as DASH (.mpd) or HLS (.m3u8) URLs. (enforced by browser)

***
