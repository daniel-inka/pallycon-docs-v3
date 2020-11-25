---
title: HTML5 플레이어 연동 가이드
linktitle: HTML5 플레이어 연동
summary: 본 문서는 서비스 사이트의 웹 페이지에서 HTML5 플레이어를 이용해 멀티DRM(PlayReady, Widevine, FairPlay Streaming)으로 보호된 스트리밍 콘텐츠(DASH 또는 HLS)를 재생하는 방법을 설명합니다.
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
lastmod: "2020-09-16T00:00:00Z"
draft: false
featured: true

menu:
  multidrm:
    parent: 클라이언트 연동
    weight: 10

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 10
---

## 개요

본 문서는 서비스 사이트의 웹 페이지에서 HTML5 플레이어를 이용해 멀티DRM(PlayReady, Widevine, FairPlay Streaming)으로 보호된 스트리밍 콘텐츠(DASH 또는 HLS)를 재생하는 방법을 설명합니다.

1. MPEG-DASH CENC 콘텐츠
 - Common Encryption 규격에 따라 암호화되어 PlayReady와 Widevine DRM으로 보호되는 DASH 방식의 스트리밍 콘텐츠입니다. 사용자의 브라우저에 따라 PlayReady(IE11, Edge) 또는 Widevine(Chrome, FireFox) DRM이 적용됩니다.

2. HLS FPS(FairPlay Streaming) 콘텐츠
 - SAMPLE AES 방식으로 암호화되어 FairPlay Streaming을 통해 보호되는 HTTP Live Streaming 콘텐츠입니다. 맥 OS X(10.10 이상)에서 구동되는 사파리 브라우저에 적용됩니다.

HTML5 플레이어에서 멀티DRM 라이선스를 발급받기 위해서는 PallyCon 클라우드와 고객사 서비스 사이트와의 연동을 통해 사용자 인증을 해야 하며, 라이선스 발급을 위한 멀티DRM 연동은 [멀티DRM 네이티브 연동 가이드](../multidrm-native-integration/) 문서를 참고하시기 바랍니다.

## 기본 연동 방식

HTML5 플레이어의 PallyCon 멀티DRM 연동은 각 플레이어에서 제공하는 DRM 관련 API를 통해 PallyCon 라이선스 발급에 필요한 데이터(Custom Data 또는 토큰)를 Custom HTTP Header에 설정하는 방식으로 구현됩니다.

### Widevine / PlayReady 설정

Widevine과 PlayReady DRM은 다음과 같이 라이선스 요청 URL과 Custom HTTP Header를 설정하여 연동할 수 있습니다. MPEG-DASH CENC를 지원하는 브라우저에서 실행되는 경우에, 각각의 해당 DRM 설정이 적용되어 DASH 콘텐츠를 재생하게 됩니다.

### FairPlay Streaming 설정

FairPlay Streaming 연동을 위해서는 라이선스 요청 주소와 Custom HTTP Header 정보 외에 추가적인 API가 사용됩니다. 애플로부터 발급받는 FPS Deployment Package의 CERT 파일을 설정하기 위해 certificateURL 값을 설정하며, prepareContentId와 prepareCertificate API를 처리해 주어야 합니다.

> 본 문서에서 설명되지 않은 플레이어의 경우에도, 멀티 DRM 관련 Custom Header를 설정할 수 있는 플레이어라면 연동이 가능합니다. 아래 입력 값 설명과 플레이어 별 샘플 코드를 참고하시기 바랍니다.

### 입력 값 설명

각 플레이어 별 연동 API 코드에 사용된 입력 값들에 대한 설명은 아래를 참고하시기 바랍니다.

|Name |Value |Remarks |
|---|---|---|
| DASH_MANIFEST_URL | CENC 규격으로 패키징된 MPEG-DASH 콘텐츠의 manifest(mpd) 파일 URL | Widevine, PlayReady 공통으로 사용 |
| SERVER_CERTIFICATE | Widevine server에서 발급 된 인증서 | chrome58 버젼 이상의 브라우저에서 widevine 컨텐츠 재생 시 사용. 없을 경우 서버로 인증서를 요청 후 라이센스를 요청하게 됨.|
| PALLYCON_LA_URL | PallyCon 클라우드 서비스의 라이선스 요청용 URL. `https://license.pallycon.com/ri/licenseManager.do` | 동일한 주소로 Widevine, PlayReady, FairPlay 각각 설정. PlayReady는 생략 가능하며, 생략 시 DASH 패키징 과정에서 pssh에 설정된 URL이 사용됨. |
| CUSTOM_HEADER_NAME | LA URL로 라이선스 요청과 함께 전달될 Custom HTTP Header의 이름. `pallycon-customdata-v2` 사용 | 구 버전 연동 규격의 경우 `pallycon-customdata` 사용 |
| CUSTOM_HEADER_VALUE | 사용자 인증 방식에 따라 custom data 또는 token 값을 사용<br /> 1) base64 encoding ([PallyCon Custom Data v2 Format](../multidrm-native-integration/#pallycon-custom-data-v2) 참조)<br /> 2) 라이선스 토큰 문자열 ([라이선스 토큰 가이드](../../license/license-token) 참조) | DRM 유형에 따라 값이 다름 |
| HLS_PLAYLIST_URL | FPS 규격으로 패키징된 HLS 콘텐츠의 playlist(m3u8) 파일 URL |  |
| PALLYCON_FPS_CERT_URL | FPS Certification 파일의 다운로드 URL `https://license.pallycon.com/ri/fpsKeyManager.do?siteId='Site_ID'` |Site_ID는 PallyCon에서 발급된 서비스 사이트 ID(4byte)를 입력 |

## Shaka 플레이어 연동 {#shaka}

{{< figure library="true" src="shaka-logo.png" >}}

Shaka 플레이어는 HLS와 MPEG-DASH를 지원하는 HTML5 플레이어입니다. Widevine과 PlayReady를 통한 CENC 콘텐츠 재생과 FairPlay HLS 재생을 지원합니다. Shaka 플레이어에 대한 자세한 내용은 아래 링크들을 참조하시기 바랍니다.

- Shaka 플레이어 github : <https://github.com/google/shaka-player>
- Shaka 플레이어 온라인 가이드 : <https://shaka-player-demo.appspot.com/docs/api/index.html>
- Shaka 플레이어 DRM Setup 가이드 : <https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html>

각각의 DRM 유형에 따른 Shaka 플레이어의 API 설정은 다음과 같습니다.

### API 코드 (Widevine, PlayReady)

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

### API 코드 (FairPlay)

> 주의: 아래 FairPlay 연동 코드는 ShakaPlayer 2.5.x 버전에서만 동작하며, ShakaPlayer 3.x 버전은 아직 PallyCon 멀티 DRM과의 FPS 연동이 지원되지 않습니다.

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

### 예제 (Widevine, PlayReady 설정)

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

### 커스텀 에러 처리

PallyCon 클라우드에서 HTML5 플레이어의 라이선스 요청을 처리하는 중에 에러가 발생하는 경우, 해당 에러 코드와 내용을 플레이어에서 확인할 수 있습니다. Shaka 플레이어 설정에서는 에러 처리를 위해 registerResponseFilter API를 사용합니다.

#### 에러 처리 예제

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

registerResponseFilter는 Widevine과 PlayReady 모두에 적용되며, 실제 서비스에 적용 시에는 위 예제 코드를 활용하여 서비스에 적합한 방식으로 에러 처리를 해 주시기 바랍니다.

에러 코드 목록과 해당 메시지 내용은 [멀티 DRM 라이선스 에러코드](../../license/license-errorcode)를 참조하시기 바랍니다.

## Bitmovin 플레이어 연동 {#bitmovin}

{{< figure library="true" src="bitmovin-logo.png" >}}

Bitmovin 플레이어는 HLS와 MPEG-DASH를 지원하는 HTML5 플레이어입니다. Bitmovin 플레이어에 대한 자세한 내용은 아래 링크들을 참조하시기 바랍니다.

- Bitmovin 플레이어 홈페이지 : <https://bitmovin.com/html5-player/>
- Bitmovin 플레이어 온라인 가이드 : <https://bitmovin.com/docs/player>
- Bitmovin 플레이어 DRM Setup 가이드 : <https://bitmovin.com/docs/player/tutorials/setup-the-bitmovin-player-with-multiple-drm-solutions>

각각의 DRM 유형에 따른 Bitmovin 플레이어의 JSON API 설정은 다음과 같습니다. (BitMovin Player API v8 기준)

### API 코드 (Widevine, PlayReady)

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

### API 코드 (FairPlay)

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

### 통합 예제 (Widevine, PlayReady, FairPlay Streaming 모두 설정)

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
          'pallycon-customdata-v2': 'eyJkYXRhIjoibWJLV1NjSUNIU25WMjZQWFB4cGlDSmNsOHdJMHV1Qlc1cnFqU0hzV1BRSTM4ek1iZTcyZ3Q5TllZRXprRmFjUU5hVU8yRGZUb2l0YzZndXZ5RzJOWlE9PSIsInNpdGVfaWQiOiJERU1PIiwiZHJtX3R5cGUiOiJXaWRldmluZSJ9'
        }
      },
      playready: {
        LA_URL: 'https://license.pallycon.com/ri/licenseManager.do',
        headers: {
          'pallycon-customdata-v2' : 'eyJkYXRhIjoibWJLV1NjSUNIU25WMjZQWFB4cGlDSmNsOHdJMHV1Qlc1cnFqU0hzV1BRSTM4ek1iZTcyZ3Q5TllZRXprRmFjUU5hVU8yRGZUb2l0YzZndXZ5RzJOWlE9PSIsInNpdGVfaWQiOiJERU1PIiwiZHJtX3R5cGUiOiJQbGF5UmVhZHkifQ=='
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

### 커스텀 에러 처리

PallyCon 클라우드에서 HTML5 플레이어의 라이선스 요청을 처리하는 중에 에러가 발생하는 경우, 해당 에러 코드와 내용을 플레이어에서 확인할 수 있습니다. Bitmovin 플레이어 설정에서는 에러 처리를 위해 prepareLicense API를 사용합니다.

#### 에러 처리 예제 (Widevine의 경우)

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

위 예제는 Widevine 라이선스 응답에 대한 처리를 보여주고 있으며, 필요 시 PlayReady와 FairPlay Streaming 항목의 prepareLicense API도 동일하게 처리해 주어야 합니다. 실제 서비스에 적용 시에는 위 예제 코드를 활용하여 서비스에 적합한 방식으로 에러 처리를 해 주시기 바랍니다.

에러 코드 목록과 해당 메시지 내용은 [멀티 DRM 라이선스 에러코드](../../license/license-errorcode)를 참조하시기 바랍니다.

## Viblast 플레이어 연동 {#viblast}

{{< figure library="true" src="viblast-player-logo.png" >}}

Viblast 플레이어는 HLS와 MPEG-DASH를 지원하며 P2P 방식의 콘텐츠 전송 기능을 지원하는 HTML5 플레이어입니다. Widevine과 PlayReady를 통한 CENC 콘텐츠 재생을 지원하지만, **아직 FPS는 지원하지 않습니다.** (향후 지원 예정) Viblast 플레이어에 대한 자세한 내용은 아래 링크들을 참조하시기 바랍니다.

- Viblast 플레이어 홈페이지 : <https://viblast.com/player/>
- Viblast 플레이어 온라인 가이드 : <https://docs.viblast.com/player/>
- Viblast 플레이어 DRM 연동 가이드 : <https://docs.viblast.com/player/drm-protection>

각각의 DRM 유형에 따른 Viblast 플레이어의 API 설정은 다음과 같습니다.

### API 코드 (Widevine, PlayReady)

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

### 통합 예제 (Widevine, PlayReady 모두 설정)

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

### 커스텀 에러 처리

PallyCon 클라우드에서 HTML5 플레이어의 라이선스 요청을 처리하는 중에 에러가 발생하는 경우, 해당 에러 코드와 내용을 플레이어에서 확인할 수 있습니다. Viblast 플레이어 설정에서는 에러 처리를 위해 'load' Event Listener API를 사용합니다.

#### 에러 처리 예제

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

Load Event Listener는 Widevine과 PlayReady 모두에 적용되며, 실제 서비스에 적용 시에는 위 예제 코드를 활용하여 서비스에 적합한 방식으로 에러 처리를 해 주시기 바랍니다.

에러 코드 목록과 해당 메시지 내용은 [멀티 DRM 라이선스 에러코드](../../license/license-errorcode)를 참조하시기 바랍니다.

## THEOplayer 플레이어 연동 {#theo}

{{< figure library="true" src="theoplayer-logo.png" >}}

THEOplayer는 Web, 모바일 앱, 스마트TV 등의 환경에서 HLS와 MPEG-DASH 재생을 지원하는 크로스플랫폼 비디오 플레이어입니다. Widevine과 PlayReady를 통한 DASH CENC 콘텐츠 보호와 FairPlay Streaming을 통한 HLS 콘텐츠 보호를 지원합니다. THEOplayer에 대한 자세한 내용은 아래 링크들을 참조하시기 바랍니다.

- THEOplayer 홈페이지 : <https://theoplayer.com/>
- THEOplayer 개발자 문서 : <https://support.theoplayer.com/hc/en-us>
- THEOplayer DRM 연동 API : <https://support.theoplayer.com/hc/en-us/articles/214350425#ContentProtectionDescription>

각각의 DRM 유형에 따른 THEOplayer의 API 설정은 다음과 같습니다.

### API 코드 (Widevine, PlayReady)

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

### API 코드 (FairPlay))

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

### 통합 예제 (Widevine, PlayReady, FPS 모두 설정)

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

### 커스텀 에러 처리

PallyCon 클라우드에서 HTML5 플레이어의 라이선스 요청을 처리하는 중에 에러가 발생하는 경우, 해당 에러 코드와 내용을 플레이어에서 확인할 수 있습니다. THEOplayer 설정에서는 에러 처리를 위해 'contentprotectionerror' Event Listener API를 사용합니다.

#### 에러 처리 예제

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

Event Listener는 Widevine과 PlayReady 모두에 적용되며, 실제 서비스에 적용 시에는 위 예제 코드를 활용하여 서비스에 적합한 방식으로 에러 처리를 해 주시기 바랍니다.

에러 코드 목록과 해당 메시지 내용은 [멀티 DRM 라이선스 에러코드](../../license/license-errorcode)를 참조하시기 바랍니다.

## VideoJS 플레이어 연동 {#videojs}

{{< figure library="true" src="videojs-logo.png" >}}

VideoJS 플레이어는 HLS와 MPEG-DASH를 지원하는 HTML5 플레이어입니다. VideoJS 플레이어에 대한 자세한 내용은 아래 링크들을 참조하시기 바랍니다.

- VideoJS 플레이어 홈페이지 : <https://videojs.com/>
- VideoJS 플레이어 온라인 가이드 : <https://docs.videojs.com/>

VideoJS 플레이어의 멀티DRM 연동을 위해서는 아래와 같은 플러그인을 추가로 사용해야 합니다.

- VideoJS DASH 플러그인 (Dash.js 필요) : https://github.com/videojs/videojs-contrib-dash
- VideoJS FairPlay 플러그인 : {{% button href="/docs/files/videojs-fairplay-support.zip" icon="fas fa-download" %}}videojs-fairplay-support.js 다운로드{{% /button %}}

각각의 DRM 유형에 따른 VideoJS 플레이어의 JSON API 설정은 다음과 같습니다.

### API 코드 (Widevine, PlayReady)

> 아래 코드는 Dash.js와 VideoJS DASH 플러그인 적용 후 사용 가능합니다.

```js
let player = videojs('my-video');
player.ready(function(){
    player.src({
        'src': 'DASH_MANIFEST_URL',
        'type': 'application/dash+xml',
        'keySystemOptions': [
            {
                'name': 'com.widevine.alpha',
                'options':{
                    'serverURL' : 'PALLYCON_LA_URL',
                    'httpRequestHeaders' : {
                        'CUSTOM_HEADER_NAME' : 'CUSTOM_HEADER_VALUE',
                    }
                }
            },
            {
                'name': 'com.microsoft.playready',
                'options':{
                    'serverURL' : 'PALLYCON_LA_URL',
                    'httpRequestHeaders' : {
                        'CUSTOM_HEADER_NAME' : 'CUSTOM_HEADER_VALUE',
                    }
                }
            }
        ]
    });
})
player.play();
```

### API 코드 (FairPlay)

> 아래 코드는 위에서 다운받은 `videojs-fairplay-support.js` 플러그인 적용 후 사용 가능합니다.

```js
let player = videojs('my-video');
player.ready(function(){
    pallyCon.fairplay({
        license_url : 'PALLYCON_LA_URL',
        player: player,
        pallycon_custom_data : 'CUSTOM_HEADER_VALUE',
        certificate_uri: 'PALLYCON_FPS_CERT_URL',
        content_url: 'HLS_PLAYLIST_URL'
    });
});
player.play();
```
