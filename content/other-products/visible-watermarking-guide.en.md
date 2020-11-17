---
linktitle: PallyCon Visible Watermarking Guide
weight: 3

# Page metadata.
title: PallyCon Visible Watermarking Guide
date: "2018-09-09T00:00:00Z"
lastmod: "2018-09-09T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: true  # Show table of contents? true/false
type: book  # Do not modify.

menu:
  visiblewatermarking:
    weight: 3
    parent: Other Products
---

## Overview

`PallyCon Visible Watermarking` is a watermark solution for HTML5 players to identify the user when video content is leaked illegally.

`PallyCon Visible Watermarking` displays user information as an overlay on top of the HTML5 player. It also includes anti-debugging and obfuscation features to prevent users from deleting or tampering with watermarks using browser developer tools.

## Dependency

`PallyCon Visible Watermarking` product is based on VideoJS player by default.

> If you need to integrate with HTML5 player other than VideoJS, it can be provided as SDK for porting `PallyCon Visible Watermarking` product directly to the player. Please contact us for more details.

## Quick Start

`PallyCon Visible Watermarking` comes in a combination of VideoJs so you can use both the basic features of VideoJS player and Visible Watermark.

1. Link the `video.js` file in the src folder of the downloaded zip file to the web page.
```html
<script src="video.js" />
```

2. Set the class of video tag as 'video-js', the same way as VideoJS player.
```html
<video id="my-video-dash"
  class="video-js"
  playsinline
  controls
  preload="auto"
  width="1200"
  height="650" />
```

3. You can also use Video.js manually instead of `<video>` via data-setup as shown below.
```js
let player = videojs('my-player');
```

4. When the created player object is ready, set the visible watermark using the watermarkSetData event.
```js
var parameter = {
  siteId: "DEMO",
  fontsize: "small",
  opacity: 0.5,
  interval: 5,
  peakduration: 2,
  msg: ["inka", "inka@inka.co.kr"]
};
player.ready( function() {
  player.trigger("watermarkSetData", parameter);
}
```

## Product Authentication

`PallyCon Visible Watermarking` product requires customer authentication.  
To do this, you need to set the `site ID` issued when signing up on the PallyCon site.  
If the ID is not correct, `Failed setting` callback function will be passed, and the watermark will not work properly.  
When authentication fails for the entered siteId, the watermark message is fixed as 'PallyCon VW Trial'.

```js
object.siteId = "DEMO";
watermark.setWatermarkData(object);
```

### Service Domain Registration

For the product authentication, the domain of the web service to which the product will be applied must be registered. Log in to the PallyCon console site and enter the domain information in the `Service URL` field in the `Site Settings` > `Service Settings` screen.

- When applied to the default domain and all sub-domains: *.test.com
- Applicable only to a specific sub-domain: 123.test.com

## Watermarking Parameter

* siteId
  * The site id issued when signing up on PallyCon service
  * The watermark does not work without proper site ID
* fontsize (default: *medium*)
  * Size of watermark text
  * Value: xsmall, small, medium, large, xlarge (one of them)
* opacity (default: *0.8*)
  * Maximum opacity of watermark
  * Value: a float number between 0 to 1.0
* interval (default: *60*))
  * Interval of watermark display (second)
  * Value: integer
* peakduration (default: *2*)
  * Duration of watermark display (second)
  * Value: integer
* positiontype (default: *random: 85*)
  * Type of watermark position
  * Value: fixed, margin, random, edge
    * fixed
      * fixed position
      * Value: lefttop, righttop, center, leftbottom, rightbottom
    * margin
      * Specify the location you want to show yourself
      * Value: left:number;right:number;top:number;bottom:number
      * ex: left:10;top:10
      * 10 means a percentage number 0 to 100 integer
      * When inputting both left and right, only left is applied, when inputting top and bottom, only top is applied 
    * random
      * Ratio of the area where watermark is to be displayed (percent)
      * Value: integer number between 1 to 99
    * edge
      * Ratio of the area where the watermark will not be displayed (percent)
      * Value: integer number between 1 to 99
* msg 
   * Message to display
   * Can be set up as a string array (unlimited characters)
     * Example: ["123456789ABCDEF", "GHIJKLMNOPQR"]
   * Can be set to a single string. Characters longer than 32 are wrapped
     * Example: "123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ"<br>
               "123456789ABCDEFGHIJKLMNOPQRSTUVW<br>XYZ"
   * Can be set wrap a string. add the character '\<br\>' in the middle of the string
     * Example: "123456789\<br\>ABCDEFGHIJKLMNOPQRSTUVWXYZ"<br>
               "123456789<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ"

There are two ways to get the setting data of VisibleWatermark, through the Rest API and by setting the parameters directly.

### Rest API

Rest API method is to set visible watermark using url entered as parameter. It passes url information through 'watermarkSetData' event.

> You need a server to set up the messages that will be displayed on the watermark.

```js
var parameter = {
  url: "http://www.testserver.com/restapi",
  userid: "abcdefg",
  contentid: "qwerty"
};

player.trigger("watermarkSetData", parameter);
```

By using POST method, userid and contentid are passed as url address in key:value format.
  
> Rest data can be changed from the rest api server.
  
The server sends the watermark parameter in JSON format as shown below.

```json
{
  "siteId":"DEMO",
  "fontsize":"small",
  "interval":30,
  "peakduration":2,
  "positiontype":{ "fixed":"center" },
  "msg":["inka","inka@inka.co.kr"]
}
```

### Manual Setting

You can set the visible watermark parameter directly as shown below.

```js
var parameter = {
  "siteId":"DEMO",
  fontsize: "small",
  interval: 30,
  peakduration: 2,
  positiontype: { edge: 80 },
  msg: ["inka", "inka@inka.co.kr"]
};

player.ready( function() {
  player.trigger("watermarkSetData", parameter);
}
```

## Player Dispose

If a user manipulates watermark information during playback, video playback will be forcibly stopped.
In that case, you can receive callback in the following way.

```js
player.on("force_stop", () => {
    console.log('The user manipulated Watermark information during playback, video playback will be forcibly stopped.');
});
```

## Watermark Setting Failed

If an error occurs during the setting of PallyCon Visible Watermark, the watermark does not work properly.
If user authentication fails, you can receive callback as follows.

```js
player.on("failed_setting", () => {
    console.log('Watermark setting failed. In this case, it is recommended to stop playing the video.');
});
```

## Trouble Shooting

* Security of watermark
  * Problem 
  Watermark can be tempered by manipulating function and class that call the VisibleWatermarkingSDK.

  * Solution
    1. Use immediately-invoked function to modularize classes that call the VisibleWatermarkingSDK.
    2. You can protect the classes that call the VisibleWatermarkingSDK bu using [uglyfy-js](https://www.npmjs.com/package/uglify-js) or [JavaScript Obfuscator Tool](https://obfuscator.io/).

* iOS
  * Problem
  On iOS browser, Native Player works instead of HTML5 player and VisibleWatermarkingSDK does not work.

  * Solution
    1. Add *playsinline* option inside the video tag.
    ```js
    <video id="my-player" class="video-js" playsinline controls preload="auto" width="1200" height="650" />
    ```
    2. Delete the fullscreen button.
    ```js
    player.ready( function() {
      var fullScreenControl = document.getElementsByClassName("vjs-fullscreen-control")[0];
      if (check if it's iOS) {
        fullScreenControl.parentNode.removeChild(fullScreenControl);
      }
    }
    ```
    
* Picture in Picture(PIP)
  * Problem  
  Visible watermarking is not supported in PIP mode. Therefore, playback will be canceled when the user selects PIP mode.

  * Solution 
  Set 'disablePictureInPicture' in `<video>` to prevent user from selecting PIP mode.

***
