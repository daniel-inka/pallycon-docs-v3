---
title: HTML5 Player Integration Guide
linktitle: HTML5 Player Integration
summary: This document explains how to play streaming content (DASH or HLS) protected with multi-DRM (PlayReady, Widevine, FairPlay Streaming) using the HTML5 player from the web page of the service site.
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

## Overview

This document explains how to play streaming content (DASH or HLS) protected with multi-DRM (PlayReady, Widevine, FairPlay Streaming) using the HTML5 player from the web page of the service site.

1. MPEG-DASH CENC content
 - DASH streaming content protected by PlayReady and Widevine DRM encrypted under the Common Encryption standard. Depending on your browser, PlayReady (IE11, Edge) or Widevine (Chrome, FireFox) DRM is applied.

2. HLS FPS(FairPlay Streaming) content
 - HTTP Live Streaming content encrypted with Sample AES and protected by FairPlay Streaming. Applies to Safari browsers running on Mac OS X (10.10 or later).

In order to issue multi-DRM licenses in HTML5 player, user authentication must be performed through the integration with PallyCon cloud and customer service site. Please check [Multi-DRM Native Integration](../multidrm-native-integration/) for license issuance integration.

## Basic integration method

To integrate HTML5 Player with PallyCon Multi DRM service, you need to set the Custom Data or Token for PallyCon license issuance in the Custom HTTP Header through the DRM related API provided by the player.

### Widevine / PlayReady settings

Widevine and PlayReady DRM can be configured by setting the license request URL and custom HTTP header as follows. When running in a browser that supports MPEG-DASH CENC, each corresponding DRM setting will be applied to play DASH content.

### FairPlay Streaming settings

 For FairPlay Streaming integration, additional APIs are used in addition to the license request address and Custom HTTP Header information. To set the CERT file for the FPS deployment package that is issued by Apple, set the certificateURL value and use the prepareContentId and prepareCertificate APIs as described in the code below.

> For players that are not described in this document, it is possible to link multiple DRM-related custom headers to players. Please refer to the input value description below and the player-specific sample code.

### Input Value Description

Please refer to the description below for the input values ​​used in each player-specific API code.

|Name |Value |Remarks |
|---|---|---|
| DASH_MANIFEST_URL | Manifest(mpd) URL of MPEG-DASH content which is encrypted by CENC spec | common for Widevine and PlayReady |
| SERVER_CERTIFICATE | Certificate data which is issued by Widevine server. | Used for CENC with Widevine DRM running on Chrome 58 or later version. If this data is missing, the certificate will be requested to PallyCon Cloud before license acquisition.|
| PALLYCON_LA_URL | License acquisition URL of PallyCon Cloud. `https://license.pallycon.com/ri/licenseManager.do` | same for Widevine and PlayReady. LA URL for PlayReady is optional, and the URL set in pssh is used if it is omitted in setting. |
| CUSTOM_HEADER_NAME | Name of Custom HTTP Header which is sent to LA URL with license request. use `pallycon-customdata-v2` | For old version spec, use `pallycon-customdata` |
| CUSTOM_HEADER_VALUE | Custom data or License token <br> 1) base64 encoding ([PallyCon Custom Data v2 Format](../multidrm-native-integration/#pallycon-custom-data-v2)) <br> 2) License token string ([License Token Guide]({{%ref "license-token.en.md"%}})) | . |
| HLS_PLAYLIST_URL | Manifest(m3u8) file URL of HLS content which is packaged by FPS spec. |  |
| PALLYCON_FPS_CERT_URL | Download URL of FPS certification file `https://license.pallycon.com/ri/fpsKeyManager.do?siteId='Site_ID'` | Input Site_ID which is issued by PallyCon Cloud service. (4byte) |

## Shaka Player Integration {#shaka}

![shaka](/docs/images/shaka-logo.png)

Shaka player is an HTML5 player that supports HLS and MPEG-DASH. It supports CENC content playback via Widevine and PlayReady, and HLS content via FairPlay. For more information about Shaka player, please see the links below.

- Shaka Player Github : https://github.com/google/shaka-player

- Shaka Player Online Guide : https://shaka-player-demo.appspot.com/docs/api/index.html

- Shaka Player DRM Setup Guide : https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html

Shaka player's API settings for each type of DRM are as below:

### API Code (Widevine, PlayReady)

```js
    shaka.polyfill.installAll();
    var video = document.getElementById('player');
    var player = new shaka.Player(video);
    window.player = player;

    player.load('DASH_MANIFEST_URL').then(function(){}).catch(onError);

    var serverCertificate = 'SERVER_CERTIFICATE';

    player.configure({
        drm:{
            servers:{
                'com.widevine.alpha': 'PALLYCON_LA_URL',
                'com.microsoft.playready': 'PALLYCON_LA_URL'
            },
            advanced: {
                'com.widevine.alpha': {
                    serverCertificate: serverCertificate
                }
            }
        }
    });

    player.getNetworkingEngine().registerRequestFilter(function(type, request) {
        if (type == shaka.net.NetworkingEngine.RequestType.LICENSE) {
            request.headers['CUSTOM_HEADER_NAME'] = 'CUSTOM_HEADER_VALUE';
        }
    });

```

### API Code (FairPlay)

> Note: The FairPlay integration code below only works with ShakaPlayer 2.5.x, and ShakaPlayer 3.x version does not yet support FPS integration with PallyCon multi-DRM.

```js
async function getFairplayCert() {
    let cert;
    const fpsCertUrl = 'PALLYCON_FPS_CERT_URL';
    const responseData = await $.ajax({
        url: fpsCertUrl,
        type: "get",
        async: false
    });
    cert = base64DecodeUint8Array(responseData);
    return cert;
}

//////

const fairplayCert = await getFairplayCert();

player.configure({
    drm: {
        servers: {
            'com.apple.fps.1_0': 'PALLYCON_LA_URL'
        },
        advanced: {
            'com.apple.fps.1_0': {
                serverCertificate: fairplayCert
            }
        },
        initDataTransform: initData => {
            const skdUri = shaka.util.StringUtils.fromBytesAutoDetect(initData);
            const contentId = skdUri.substring(skdUri.indexOf('skd://') + 6);
            const cert = player.drmInfo().serverCertificate;
            return shaka.util.FairPlayUtils.initDataTransform(initData, contentId, cert);
        }
    }
});

player.getNetworkingEngine().registerRequestFilter((type, request) => {
    if (type != shaka.net.NetworkingEngine.RequestType.LICENSE) {
        return;
    }

    const originalPayload = new Uint8Array(request.body);
    const base64Payload = shaka.util.Uint8ArrayUtils.toBase64(originalPayload);
    const params = 'spc=' + encodeURIComponent(base64Payload);

    request.headers['Content-Type'] = 'application/x-www-form-urlencoded';
    request.uri = licenseUrl + "?" + params;
    request.headers['CUSTOM_HEADER_NAME'] = 'CUSTOM_HEADER_VALUE';
});

player.getNetworkingEngine().registerResponseFilter((type, response) => {
    if (type != shaka.net.NetworkingEngine.RequestType.LICENSE) {
        return;
    }

    let responseText = shaka.util.StringUtils.fromUTF8(response.data);
    // Trim whitespace.
    responseText = responseText.trim();
    // Decode the base64-encoded data into the format the browser expects.
    response.data = shaka.util.Uint8ArrayUtils.fromBase64(responseText).buffer;
});
```

### Example (for Widevine and PlayReady)

```js
function base64DecodeUint8Array(input) {
  var raw = window.atob(input);
  var rawLength = raw.length;
  var array = new Uint8Array(new ArrayBuffer(rawLength));

  for(i = 0; i < rawLength; i++){
    array[i] = raw.charCodeAt(i);
  }

  return array;
}

shaka.polyfill.installAll();

var video = document.getElementById('player');
var player = new shaka.Player(video);
window.player = player;

player.load('https://pallycon.cloudfront.net/tears_of_steel_720p/dash/stream.mpd').then(function(){}).catch(onError);

var base64Cert = "CsECCAMSEBcFuRfMEgSGiwYzOi93KowYgrSCkgUijgIwggEKAoIBAQCZ7Vs7Mn2rXiTvw7YqlbWYUgrVvMs3UD4GRbgU2Ha430BRBEGtjOOtsRu4jE5yWl5KngeVKR1YWEAjp+GvDjipEnk5MAhhC28VjIeMfiG/+/7qd+EBnh5XgeikX0YmPRTmDoBYqGB63OBPrIRXsTeo1nzN6zNwXZg6IftO7L1KEMpHSQykfqpdQ4IY3brxyt4zkvE9b/tkQv0x4b9AsMYE0cS6TJUgpL+X7r1gkpr87vVbuvVk4tDnbNfFXHOggrmWEguDWe3OJHBwgmgNb2fG2CxKxfMTRJCnTuw3r0svAQxZ6ChD4lgvC2ufXbD8Xm7fZPvTCLRxG88SUAGcn1oJAgMBAAE6FGxpY2Vuc2Uud2lkZXZpbmUuY29tEoADrjRzFLWoNSl/JxOI+3u4y1J30kmCPN3R2jC5MzlRHrPMveoEuUS5J8EhNG79verJ1BORfm7BdqEEOEYKUDvBlSubpOTOD8S/wgqYCKqvS/zRnB3PzfV0zKwo0bQQQWz53ogEMBy9szTK/NDUCXhCOmQuVGE98K/PlspKkknYVeQrOnA+8XZ/apvTbWv4K+drvwy6T95Z0qvMdv62Qke4XEMfvKUiZrYZ/DaXlUP8qcu9u/r6DhpV51Wjx7zmVflkb1gquc9wqgi5efhn9joLK3/bNixbxOzVVdhbyqnFk8ODyFfUnaq3fkC3hR3f0kmYgI41sljnXXjqwMoW9wRzBMINk+3k6P8cbxfmJD4/Paj8FwmHDsRfuoI6Jj8M76H3CTsZCZKDJjM3BQQ6Kb2m+bQ0LMjfVDyxoRgvfF//M/EEkPrKWyU2C3YBXpxaBquO4C8A0ujVmGEEqsxN1HX9lu6c5OMm8huDxwWFd7OHMs3avGpr7RP7DUnTikXrh6X0";
var serverCertificate = base64DecodeUint8Array(base64Cert);

player.configure({
    drm:{
        servers:{
            'com.widevine.alpha': 'https://license.pallycon.com/ri/licenseManager.do',
            'com.microsoft.playready': 'https://license.pallycon.com/ri/licenseManager.do'
        },
        advanced: {
            'com.widevine.alpha': {
                serverCertificate: serverCertificate
            }
        }
    }
});

player.getNetworkingEngine().registerRequestFilter(function(type, request) {
    if (type == shaka.net.NetworkingEngine.RequestType.LICENSE) {
      if (is_chrome_or_firefox) {
        request.headers['pallycon-customdata-v2'] = 'WM92HmV/aEtHgkIeKbAnZbDhdUUQEnPkGd8aadwfa0NA3KAxht3lJCdOvVWZgMIpZgT+xLdToGnzSSONYoY2Ps/FDovXJfryqydNngwXJ0l+x1UUbFV26mWhGhZ+Irw0/tt8F7zupCHwqqLjyGZXbkEMzWOZfcaY8Cb5omfR6+kL0ZrygUomv1H3keFiOS2s';
      } else {
        request.headers['pallycon-customdata-v2'] = 'WM92HmV/aEtHgkIeKbAnZbRl52BofvWtsPYVWbYMbOpAYSb+yJzTF97QBF1Szbq/rG9eQ6la+5nPV9vGXI6ZUGrM6hhfyyJInOB3tOCoFJhFkgn55rSC47Nbgno32fN8MKT1EV1daXzER1qV1EAE50SfXWlib29kVo+futrK/JwwzOw7Ujx4N+UmUf0TLROM';
      }
    }
});
...
```

### Handling Error Response

 If the PallyCon cloud encounters an error while processing license request, you can get the error code and message in the player. Shaka player provides registerResponseFilter API for error handling.

#### Example

```js
player.getNetworkingEngine().registerResponseFilter(function(type, response) {
  // Alias some utilities provided by the library.
  if (type == shaka.net.NetworkingEngine.RequestType.LICENSE) {
    console.log(response.data.byteLength);
    var responseText = arrayBufferToString(response.data);
    console.log("response : " + responseText);
    if (responseText.indexOf('errorCode') > 0) {
      // this alert should be properly parsed and displayed for commercial use
      var errorCode = JSON.parse(responseText).errorCode;
      if("8002" != errorCode){
        alert("PallyCon Error : " + JSON.parse(responseText).message + "(" + JSON.parse(responseText).errorCode+ ")");
      }else{
        var message = JSON.parse(responseText).message;
        alert("Error : " + JSON.parse(message).MESSAGE + "(" + JSON.parse(message).ERROR + ")");
      }
    }
  }
});
```

registerResponseFilter is applied to both Widevine and PlayReady. When applying to your service site, please use the above example code as reference to deal with errors in a way that is appropriate for the service.

For the list of error codes and corresponding message contents, please refer to the [License Error Code]({{%ref "license-errorcode.en.md"%}}) page.

## Bitmovin Player Integration {#bitmovin}

![bitmovin](/docs/images/bitmovin-logo.png)

Bitmovin player is an HTML5 player that supports HLS and MPEG-DASH. For more information on the Bitmovin player, please refer to the links below.

- Bitmovin Homepage : https://bitmovin.com/html5-player/

- Bitmovin Player Online Guide : https://bitmovin.com/docs/player

- Bitmovin Player DRM Setup Guide : https://bitmovin.com/docs/player/tutorials/setup-the-bitmovin-player-with-multiple-drm-solutions

Bitmovin player's JSON API settings for each type of DRM are as below: (Based on Bitmovin Player API v8)

### API Code (Widevine, PlayReady)

```js
var conf = {
  ...
  source: {
    dash: 'DASH_MANIFEST_URL',
    drm: {
      widevine: {
        LA_URL: 'PALLYCON_LA_URL',
        headers: {
          'CUSTOM_HEADER_NAME' : 'CUSTOM_HEADER_VALUE'
        }
      },
      playready: {
        LA_URL: 'PALLYCON_LA_URL',
        headers: {
          'CUSTOM_HEADER_NAME' : 'CUSTOM_HEADER_VALUE'
        }
      }
    }
  }
};
```

#### API Code (FairPlay)

```js
var conf = {
  ...
  source: {
    hls: 'HLS_PLAYLIST_URL',
    drm: {
      fairplay: {
        LA_URL: 'PALLYCON_LA_URL',
        certificateURL: 'PALLYCON_FPS_CERT_URL',
        headers: {
          'CUSTOM_HEADER_NAME' : 'CUSTOM_HEADER_VALUE'
        },
        prepareContentId: function(contentId) {
          return contentId.substring(contentId.indexOf('skd://')+6);
        },
        prepareCertificate: function(rawResponse) {
          var responseText = String.fromCharCode.apply(null, new Uint8Array(rawResponse));
          var raw = window.atob(responseText);
          var rawLength = raw.length;
          var certificate = new Uint8Array(new ArrayBuffer(rawLength));

          for(var i = 0; i < rawLength; i++)
            certificate[i] = raw.charCodeAt(i);

          return certificate;
        },
        useUint16InitData: true
      }
    }
  }
};
```

### Example (sets Widevine, PlayReady and FairPlay Streaming)

```js
var conf = {
  ...
  source: {
    dash: 'https://d28giv01x4pn7o.cloudfront.net/tears_of_steel_720p/stream.mpd'
    hls: 'https://pallycon.cloudfront.net/tears_of_steel_720p/hls/master.m3u8',
    drm: {
      widevine: {
        LA_URL: 'https://license.pallycon.com/ri/licenseManager.do',
        headers: {
          'pallycon-customdata-v2' : 'eyJkYXRhIjoibWJLV1NjSUNIU25WMjZQWFB4cGlDSmNsOHdJMHV1Qlc1cnFqU0hzV1BRSTM4ek1iZTcyZ3Q5TllZRXprRmFjUU5hVU8yRGZUb2l0YzZndXZ5RzJOWlE9PSIsInNpdGVfaWQiOiJERU1PIiwiZHJtX3R5cGUiOiJXaWRldmluZSJ9'
        }
      },
      playready: {
        LA_URL: 'https://license.pallycon.com/ri/licenseManager.do',
        headers: {
          'pallycon-customdata-v2' :  'eyJkYXRhIjoibWJLV1NjSUNIU25WMjZQWFB4cGlDSmNsOHdJMHV1Qlc1cnFqU0hzV1BRSTM4ek1iZTcyZ3Q5TllZRXprRmFjUU5hVU8yRGZUb2l0YzZndXZ5RzJOWlE9PSIsInNpdGVfaWQiOiJERU1PIiwiZHJtX3R5cGUiOiJQbGF5UmVhZHkifQ=='
        }
      },
      fairplay: {
        LA_URL: 'https://license.pallycon.com/ri/licenseManager.do',
        certificateURL: 'https://license.pallycon.com/ri/fpsKeyManager.do?siteId=STID',
        headers: {
          'pallycon-customdata-v2' : 'eyJkYXRhIjoibWJLV1NjSUNIU25WMjZQWFB4cGlDSmNsOHdJMHV1Qlc1cnFqU0hzV1BRSTM4ek1iZTcyZ3Q5TllZRXprRmFjUU5hVU8yRGZUb2l0YzZndXZ5RzJOWlE9PSIsInNpdGVfaWQiOiJERU1PIiwiZHJtX3R5cGUiOiJGYWlyUGxheSJ9'
        },
        prepareContentId: function(contentId) {
          return contentId.substring(contentId.indexOf('skd://')+6);
        },
        prepareCertificate: function(rawResponse) {
          var responseText = String.fromCharCode.apply(null, new Uint8Array(rawResponse));
          var raw = window.atob(responseText);
          var rawLength = raw.length;
          var certificate = new Uint8Array(new ArrayBuffer(rawLength));

          for(var i = 0; i < rawLength; i++)
            certificate[i] = raw.charCodeAt(i);

          return certificate;
        },
        useUint16InitData: true
      }
    }
  }
};
```

### Handling Error Response 

If the PallyCon cloud encounters an error while processing license request, you can get the error code and message in the player. Bitmovin player provides prepareLicense API for error handling.

#### Example (Widevine)

```js
var conf = {
  ...
  source: {
    dash: 'https://d28giv01x4pn7o.cloudfront.net/tears_of_steel_720p/stream.mpd',
    drm: {
      widevine: {
        LA_URL: 'https://license.pallycon.com/ri/licenseManager.do',
        headers: {
          'pallycon-customdata-v2' : 'eyJkYXRhIjoibWJLV1NjSUNIU25WMjZQWFB4cGlDSmNsOHdJMHV1Qlc1cnFqU0hzV1BRSTM4ek1iZTcyZ3Q5TllZRXprRmFjUU5hVU8yRGZUb2l0YzZndXZ5RzJOWlE9PSIsInNpdGVfaWQiOiJERU1PIiwiZHJtX3R5cGUiOiJXaWRldmluZSJ9'
        },
        prepareLicense: function(licenseObj) {
          var license = {
            license: licenseObj.license
          };
          try {
            var pallyconObj = JSON.parse(String.fromCharCode.apply(null, licenseObj.license));
            if (pallyconObj && pallyconObj.errorCode && pallyconObj.message) {
              // this response is error, not valid widevine license
              console.log('ri error code : ' + pallyconObj.errorCode + ', message :', pallyconObj.message);

              var fullErrorMsg;
              if (parseInt(pallyconObj.errorCode) == 8002) {
                // getting the error from callback page
                var errorObj = JSON.parse(pallyconObj.message);
                if (errorObj && errorObj.ERROR && errorObj.MESSAGE) {
                  fullErrorMsg = 'RI errorCode : ' + pallyconObj.errorCode + '\nCallback ERROR: ' + errorObj.ERROR + '\nCallback MESSAGE: ' + errorObj.MESSAGE;
                }
              } else {
                // this error is from PallyCon RI
                fullErrorMsg = 'RI errorCode: ' + pallyconObj.errorCode + '\nRI message: ' + pallyconObj.message;
              }
              alert(fullErrorMsg);
            } 
          } catch (e) {
            // this is valid license
            return license;
          }  
      },
      ...
    }
  }
};
```

The above example shows the processing for the Widevine license response, and if necessary, apply the same process for the prepareLicense API of the PlayReady and FairPlay Streaming as well. When applying to your service site, please use the above example code as reference to deal with errors in a way that is appropriate for the service.

For the list of error codes and corresponding message contents, please refer to the [License error code]({{%ref "license-errorcode.en.md"%}}).

## Viblast Player Integration {#viblast}

![viblast](/docs/images/viblast-player-logo.png)

Viblast player is an HTML5 player that supports HLS and MPEG-DASH and supports P2P content delivery. It supports CENC content playback via Widevine and PlayReady, but **does not yet support FPS.** (coming soon) For more information about Viblast player, please see the links below.

- Viblast Homepage : https://viblast.com/player/

- Viblast Player Online Guide : https://docs.viblast.com/player/

- Viblast Player DRM Integration : https://docs.viblast.com/player/drm-protection

Viblast player's API settings for each type of DRM are as below:

### API Code (Widevine, PlayReady)

```js
viblast('#player').setup({
  ...
  stream: 'DASH_MANIFEST_URL',
  widevine: {
    'licensing-server': 'PALLYCON_LA_URL'
  },
  playready: {
    'licensing-server': 'PALLYCON_LA_URL'
  },
  xhrBeforeSend: function(ev) {
    if (ev.method !== "POST") return;
    ev.xhr.setRequestHeader('CUSTOM_HEADER_NAME', 'CUSTOM_HEADER_VALUE');
  }
});
```

### Example (for Widevine and PlayReady)

```js
viblast('#player').setup({
  ...
  stream: 'https://d28giv01x4pn7o.cloudfront.net/tears_of_steel_720p/stream.mpd',
  widevine: {
    'licensing-server': 'https://license.pallycon.com/ri/licenseManager.do'
  },
  playready: {
    'licensing-server': 'https://license.pallycon.com/ri/licenseManager.do'
  },
  xhrBeforeSend: function(ev) {
    // filter out non-drm requests
    if (ev.method !== "POST") return;

    if (is_chrome_or_firefox) {
      // need to check browser and set appropriate custom data
      ev.xhr.setRequestHeader('pallycon-customdata-v2', 'eyJkYXRhIjoibWJLV1NjSUNIU25WMjZQWFB4cGlDSmNsOHdJMHV1Qlc1cnFqU0hzV1BRSTM4ek1iZTcyZ3Q5TllZRXprRmFjUU5hVU8yRGZUb2l0YzZndXZ5RzJOWlE9PSIsInNpdGVfaWQiOiJERU1PIiwiZHJtX3R5cGUiOiJXaWRldmluZSJ9');
    }
    else {
      ev.xhr.setRequestHeader('pallycon-customdata-v2', 'eyJkYXRhIjoibWJLV1NjSUNIU25WMjZQWFB4cGlDSmNsOHdJMHV1Qlc1cnFqU0hzV1BRSTM4ek1iZTcyZ3Q5TllZRXprRmFjUU5hVU8yRGZUb2l0YzZndXZ5RzJOWlE9PSIsInNpdGVfaWQiOiJERU1PIiwiZHJtX3R5cGUiOiJQbGF5UmVhZHkifQ==');
    }
  },
  ...
});
```

### Handling Error Response

If the PallyCon cloud encounters an error while processing license request, you can get the error code and message in the player. Viblast player provides 'load' Event Listener API for error handling.

#### Example

```js
var videoCheck = 0;
viblast('#player').setup({
  stream: 'https://d28giv01x4pn7o.cloudfront.net/tears_of_steel_720p/stream.mpd',
  widevine: {
    'licensing-server': 'https://license.pallycon.com/ri/licenseManager.do'
  },
  xhrBeforeSend: function(ev) {
    // filter out non-drm requests
    if (ev.method !== "POST") return;

    if (is_chrome_or_firefox) {
      ev.xhr.setRequestHeader('pallycon-customdata-v2', 'eyJkYXRhIjoibWJLV1NjSUNIU25WMjZQWFB4cGlDSmNsOHdJMHV1Qlc1cnFqU0hzV1BRSTM4ek1iZTcyZ3Q5TllZRXprRmFjUU5hVU8yRGZUb2l0YzZndXZ5RzJOWlE9PSIsInNpdGVfaWQiOiJERU1PIiwiZHJtX3R5cGUiOiJXaWRldmluZSJ9');
    }
    else {
      ev.xhr.setRequestHeader('pallycon-customdata-v2', 'eyJkYXRhIjoibWJLV1NjSUNIU25WMjZQWFB4cGlDSmNsOHdJMHV1Qlc1cnFqU0hzV1BRSTM4ek1iZTcyZ3Q5TllZRXprRmFjUU5hVU8yRGZUb2l0YzZndXZ5RzJOWlE9PSIsInNpdGVfaWQiOiJERU1PIiwiZHJtX3R5cGUiOiJQbGF5UmVhZHkifQ==');
    }
    
    ev.xhr.addEventListener('load', function() {
      if( 1 > videoCheck){
        var responseText = String.fromCharCode.apply(null, new Uint8Array(ev.xhr.response)); // assumes utf8
        if (responseText.indexOf('errorCode') > 0) {
          // this alert should be properly parsed and displayed for commercial use
          var errorCode = JSON.parse(responseText).errorCode;
          if("8002" != errorCode){
            alert("PallyCon Error : " + JSON.parse(responseText).message + "(" + JSON.parse(responseText).errorCode+ ")");
            //window.alert('No Rights. Server Response ' + responseText);
          } else {
            var message = JSON.parse(responseText).message;
            alert("Error : " + JSON.parse(message).MESSAGE + "(" + JSON.parse(message).ERROR + ")");
          }
        }
        videoCheck += 1;
      }
    });
  },
  ...
});
```

Load Event Listener is applied to both Widevine and PlayReady. When applying to your service site, please use the above example code as reference to deal with errors in a way that is appropriate for the service.

For the list of error codes and corresponding message contents, please refer to the [Error code]({{%ref "license-errorcode.en.md"%}}) page.

## THEOplayer Integration {#theo}

![theoplayer](/docs/images/theoplayer-logo.png)

THEOplayer is a cross-platform video player that supports HLS and MPEG-DASH playback in web, mobile apps, and smart TV environments. DASH CENC content protection via Widevine and PlayReady and HLS content protection through FairPlay Streaming. For more information about THEOplayer, please see the links below.

- THEOplayer Homepage : <https://theoplayer.com/>

- THEOplayer Developer Site : <https://support.theoplayer.com/hc/en-us>

- THEOplayer DRM API : <https://support.theoplayer.com/hc/en-us/articles/214350425#ContentProtectionDescription>

THEOplayer's API settings for each type of DRM are as below:

### API Code (Widevine, PlayReady)

```js
var CPDescriptionDASH = {
    playready : {
        headers : {
            'CUSTOM_HEADER_NAME' : 'CUSTOM_HEADER_VALUE'
        },
        licenseAcquisitionURL : 'PALLYCON_LA_URL'
    },
    widevine : {
        headers : {
            'CUSTOM_HEADER_NAME' : 'CUSTOM_HEADER_VALUE'
        },
        licenseAcquisitionURL : 'PALLYCON_LA_URL'
    }
};

player.source = {
    sources : {
        // DASH
        src : 'DASH_MANIFEST_URL',
        type : 'application/dash+xml',
        contentProtection : CPDescriptionDASH
    }
};
```

### API Code (FairPlay))

```js
var CPDescriptionFPS = {
    fairplay : {
        headers : {
            'CUSTOM_HEADER_NAME' : 'CUSTOM_HEADER_VALUE'
        },
        licenseAcquisitionURL : 'PALLYCON_LA_URL',
        certificateURL : 'PALLYCON_FPS_CERT_URL'
    }
};

player.source = {
    sources : {
        // FPS
        src : 'HLS_PLAYLIST_URL',
        type : 'application/x-mpegurl',
        contentProtection : CPDescriptionFPS
    }
};
```

### Example (for Widevine, PlayReady and FairPlay)

```js
var CPDescriptionDASH = {
    playready : {
        headers : {
            'pallycon-customdata-v2' :
            'eyJkYXRhIjoibWJLV1NjSUNIU25WMjZQWFB4cGlDQUJVbnZsOGxCOWRNZDhHZ3dhbjZqRDFwNFZoQk51VlhQY2RMaG9nNTNYbngyTjFaalwvQ3BxZ3RUS2k5Rzd3MUZ3PT0iLCJzaXRlX2lkIjoiREVNTyIsImRybV90eXBlIjoiUGxheVJlYWR5In0='
        },
        licenseAcquisitionURL : 'https://license.pallycon.com/ri/licenseManager.do'
    },
    widevine : {
        headers : {
            'pallycon-customdata-v2' : 'eyJkYXRhIjoibWJLV1NjSUNIU25WMjZQWFB4cGlDUHYyMXNiNVVJK3Z1SmhHUko4SCtoNzQwR0MrQnJHRnhramUwWTFTcld3bE9EUDVkTlVLaHo2cVA5NWt6cFU3TVE9PSIsInNpdGVfaWQiOiJERU1PIiwiZHJtX3R5cGUiOiJXaWRldmluZSJ9'
        },
        licenseAcquisitionURL : 'https://license.pallycon.com/ri/licenseManager.do'
    }
};

player.source = {
    sources : {
        // DASH
        src : 'https://d28giv01x4pn7o.cloudfront.net/bunny/stream.mpd',
        type : 'application/dash+xml',
        contentProtection : CPDescriptionDASH
    }
};

var CPDescriptionFPS = {
    fairplay : {
        headers : {
            'pallycon-customdata-v2' : 
            'eyJ0aW1lc3RhbXAiOiIyMDE5LTEyLTExVDA1OjM3OjQ1WiIsImhhc2giOiJIMzduejU1cTJBeGNYclRSSktDV3BOV3hNM3l2eGdVU0lcLzJkNVlOYTFqTT0iLCJ0b2tlbiI6IlNEQXdsK0MwZWVIakpjSGNqamI1YlE9PSIsInNpdGVfaWQiOiJIVkxVIiwiZHJtX3R5cGUiOiJmYWlycGxheSIsInVzZXJfaWQiOiJ0ZXN0LXVzZXIxIiwiY2lkIjoidGVzdC12aWRlby0wMSJ9'
        },
        licenseAcquisitionURL : 'https://license.pallycon.com/ri/licenseManager.do',
        certificateURL : 'https://license.pallycon.com/ri/fpsKeyManager.do?siteId=STID'
    }
};

player.source = {
    sources : {
        // FPS
        src : 'https://pallycon.cloudfront.net/tears_of_steel_720p/hls/master.m3u8',
        type : 'application/x-mpegurl',
        contentProtection : CPDescriptionFPS
    }
};
```

### Handling Error Response

If the PallyCon cloud encounters an error while processing license request, you can get the error code and message in the player. THEOplayer provides 'contentprotectionerror' Event Listener API for error handling.

#### Example

```js
player.addEventListener("contentprotectionerror", function(data){
    console.log( "error : " + data.error);
    console.log( "licenseAcquisitionMessage : " + data.licenseAcquisitionMessage );
    console.log( "licenseAcquisitionURL : " + data.licenseAcquisitionURL );
    console.log( "mediaTrackType : " + data.mediaTrackType );
    console.log( "status  : " + data.status );
    console.log( "statusText  : " + data.statusText );
    console.log( "type  : " + data.type );
);
```

Event Listener is applied to both Widevine and PlayReady. When applying to your service site, please use the above example code as reference to deal with errors in a way that is appropriate for the service.

For the list of error codes and corresponding message contents, please refer to the [Error code]({{%ref "license-errorcode.en.md"%}}) page.

## VideoJS Player integration {#videojs}

![videojs](/docs/images/videojs-logo.png)

VideoJS player is an HTML5 player that supports HLS and MPEG-DASH. For more information on the VideoJS player, please refer to the links below.

- VideoJS player homepage : <https://videojs.com/>
- VideoJS player online guide : <https://docs.videojs.com/>

You need to use the following plug-ins for multi-DRM integration of VideoJS player.

- VideoJS DASH plugin (requires Dash.js) : https://github.com/videojs/videojs-contrib-dash
- VideoJS FairPlay plugin : {{% button href="/docs/files/videojs-fairplay-support.zip" icon="fas fa-download" %}}Download videojs-fairplay-support.js{{% /button %}}

VideoJS player's API settings for each type of DRM are:

### API code (Widevine, PlayReady)

> The following code is available after applying the Dash.js and VideoJS DASH plug-in.

```js
let player = videojs('my-video');
player.ready(function(){
    player.src({
        'src': DASH_MANIFEST_URL,
        'type': 'application/dash+xml',
        'keySystemOptions': [
            {
                'name': 'com.widevine.alpha',
                'options':{
                    'serverURL' : PALLYCON_LA_URL,
                    'httpRequestHeaders' : {
                        CUSTOM_HEADER_NAME : CUSTOM_HEADER_VALUE,
                    }
                }
            },
            {
                'name': 'com.microsoft.playready',
                'options':{
                    'serverURL' : PALLYCON_LA_URL,
                    'httpRequestHeaders' : {
                        CUSTOM_HEADER_NAME : CUSTOM_HEADER_VALUE,
                    }
                }
            }
        ]
    });
})
player.play();
```

### API code (FairPlay)

> The following code is available after applying the `videojs-fairplay-support.js` plug-in downloaded from above.

```js
let player = videojs('my-video');
player.ready(function(){
    pallyCon.fairplay({
        license_url : PALLYCON_LA_URL,
        player: player,
        pallycon_custom_data : CUSTOM_HEADER_VALUE,
        certificate_uri: PALLYCON_FPS_CERT_URL,
        content_url: HLS_PLAYLIST_URL
    });
});
player.play();
```

***
