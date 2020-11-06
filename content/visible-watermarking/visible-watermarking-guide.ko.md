---
linktitle: PallyCon Visible Watermarking 가이드
weight: 2

# Page metadata.
title: PallyCon Visible Watermarking 가이드
date: "2018-09-09T00:00:00Z"
lastmod: "2018-09-09T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: true  # Show table of contents? true/false
type: book  # Do not modify.

menu:
  visiblewatermarking:
    weight: 2
    parent: Visible Watermarking
---

## 개요

`PallyCon Visible Watermarking` 제품은 비디오 콘텐츠가 불법 유출된 경우에 유출한 사용자를 식별하기 위한 HTML5 플레이어용 워터마크 솔루션입니다.

`PallyCon Visible Watermarking`은 HTML5 플레이어 위에 오버레이 형태로 사용자 정보를 표시합니다. 또한 사용자가 브라우저 개발자 도구를 이용하여 워터마크를 삭제하거나 임의 변경하지 못하도록 디버깅 방지 및 난독화 기능이 포함되어 있습니다.

## Dependency

`PallyCon Visible Watermarking` 제품은 기본적으로 VideoJS 플레이어를 기반으로 구현되어 있습니다.

> VideoJS 외의 HTML5 플레이어와 연동이 필요한 경우, 해당 플레이어에 직접 `PallyCon Visible Watermarking` 제품을 포팅하기 위한 SDK 형태로도 제공 가능합니다. (별도 문의)

## Quick Start

`PallyCon Visible Watermarking`은 VideoJs에 결합된 형태로 제공되기 때문에 VideoJs의 기본 기능과 Visible Watermarking 기능을 동시에 사용할 수 있습니다.

1. 다운로드 받은 압축 파일의 src 폴더에 있는 `video.js` 파일을 웹 페이지에 링크합니다.
```html
<script src="video.js" />
```

2. 플레이어의 사용 방법은 Video.js와 동일하며 `<video>`의 class를 'video-js'로 설정합니다.
```html
<video id="my-video-dash"
  class="video-js"
  playsinline
  controls
  preload="auto"
  width="1200"
  height="650" />
```

3. 아래와 같이 data-setup을 통해 `<video>` 대신 수동으로 Video.js를 사용할 수도 있습니다.
```js
let player = videojs('my-player');
```

4. 생성된 player 객체가 준비 상태가 되면, watermarkSetData 이벤트를 사용하여 Visible Watermark를 설정합니다.
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

## 제품 인증

`PallyCon Visible Watermarking` 제품을 사용하려면 고객 인증이 필요합니다. 이를 위해 PallyCon 사이트 가입 시 발급받은 `site ID`를 설정해야 합니다.
설정 방법은 아래 코드와 같습니다. 입력하지 않을 경우 등록된 `FailedSetting` 콜백 함수가 호출되며, 워터마크가 정상 동작 하지 않습니다.  
입력한 siteId 인증 실패 시, 워터마크 메시지가 `PallyCon VW Trial`로 고정 됩니다.

```js
object.siteId = "DEMO";
watermark.setWatermarkData(object);
```

### 서비스 도메인 등록

제품 인증을 위해서 추가적으로 해당 제품이 적용될 웹서비스의 도메인을 등록해야 합니다. PallyCon 콘솔 사이트에 로그인해 `사이트 설정` > `서비스 설정` 화면에서 `서비스 URL` 항목에 다음과 같이 도메인 정보를 입력합니다.

- 기본 도메인 및 모든 서브 도메인에 적용 시: `*.test.com` 형식으로 입력
- 특정 서브 도메인에만 적용 시: `123.test.com` 형식으로 입력

## Watermark Parameter

* siteId
  * PallyCon 서비스 가입 시 발급되는 site id
  * 입력하지 않을 경우 워터마크가 동작하지 않음
* fontsize (default: *medium*)
  * 폰트 크기
  * 값: xsmall, small, medium, large, xlarge 중 하나
* opacity (default: *0.8*)
  * 워터마크 최대 밝기값
  * 값: 0 ~ 1.0 사이의 실수
* interval (default: *60*))
  * watermark 표시 간격 (second)
  * 값: 정수
* peakduration(default: *2*)
  * watermark 표시 시간 (second)
  * 값: 정수
* positiontype (default: *random: 85*)
  * watermark 표시 위치 유형
  * 값: fixed, margin, random, edge
    * fixed
      * 고정 위치
      * 값: lefttop, righttop, center, leftbottom, rightbottom
    * margin
      * 사용자가 직접 보여주고 싶은 위치를 지정
      * 값: left:정수;right:정수;top:정수;bottom:정수
      * 예: left:10;top:10
      * 10은 퍼센트 수치를 의미 0~100 정수
      * left, right 둘다 입력시 left만 적용, top, bottom 입력시 top만 적용      
    * random
      * 영상 전체 영역 중, watermark가 표시될 영역의 비율 (percent)
      * 값: 1 ~ 99 사이의 정수
    * edge
      * 영상 전체 영역 중, watermark가 표시되지 않을 영역의 비율 (percent)
      * 값: 1 ~ 99 사이의 정수
* msg 
   * watermark 메시지
   * 문자열 배열 형태로 설정 가능 (길이 제한 없음)
      * 예: ["123456789ABCDEF", "GHIJKLMNOPQR"]
   * 단일 문자열로 설정 가능. 32자를 초과하는 문자는 줄바꿈됨
      * 예: "123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ"<br>
            "123456789ABCDEFGHIJKLMNOPQRSTUVW<br>XYZ"
   * 수동으로 줄바꿈을 하려면 문자열 중간에 '\<br\>' 문자를 추가하면 줄바꿈
      * 예: "123456789\<br\>ABCDEFGHIJKLMNOPQRSTUVWXYZ"<br>
            "123456789<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ"

VisibleWatermark의 설정 데이터는 Rest API를 통해 얻어오는 방법과 직접 parameter를 설정하는 2가지 방법이 있습니다.

### Rest API

Rest API 방식은 parameter로 입력된 url을 이용하여 Visible Watermark를 설정하는 방법입니다. 'watermarkSetData' 이벤트를 통해 url 정보를 전달합니다.

> 사용자는 watermark에 보여질 메시지 등을 설정하기 위한 서버가 필요합니다.

```js
var parameter = {
  url: "http://www.testserver.com/restapi",
  userid: "abcdefg",
  contentid: "qwerty"
};
player.trigger("watermarkSetData", parameter);
```

POST 방식을 이용하여 userid와 contentid를 url주소로 key:value 형태로 전달합니다.
  
> rest api 서버에서 필요한 데이터로 변경이 가능합니다.
  
서버에서는 watermark parameter를 아래와 같이 JSON 형태로 전달합니다.

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

아래와 같이 직접 Visible Watermark 파라미터 설정이 가능합니다.

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

재생 중에 사용자가 워터마크 정보를 조작할 경우, 플레이어는 강제적으로 중지됩니다.
만약 PallyCon Visible Watermark를 조작하거나 할 경우 아래와 같은 방법으로 callback 받을 수 있습니다.

```js
player.on("force_stop", () => {
    console.log('The user manipulated Watermark information during playback, video playback will be forcibly stopped.');
});
```

## Watermark Setting Failed
PallyCon Visible Watermark의 설정하는 과정에서 오류가 발생할 경우, 워터마크가 정상 동작하지 않습니다.
사용자 인증이 실패할 경우 다음과 같이 callback 받을 수 있습니다.

```js
player.on("failed_setting", () => {
    console.log(''Watermark setting failed. In this case, it is recommended to stop playing the video.');
});
```

## Trouble Shooting

* 보안
  * 문제  
  VisibleWatermark SDK를 호출하는 함수 및 클래스를 조작하여 워터마크 위변조가 가능합니다.

  * 해결
    1. 즉시실행함수를 이용하여 VisibleWatermark SDK를 호출하는 클래스 등을 모듈화합니다.
    2. [uglyfy-js](https://www.npmjs.com/package/uglify-js), [JavaScript Obfuscator Tool](https://obfuscator.io/) 등을 이용하여 VisibleWatermark SDK를 호출하는 클래스 등을 보호할 수 있습니다.

* iOS
  * 문제  
  iPhone에서는 HTML5 player 대신 Native Player가 작동되며 VisibleWatermark SDK가 동작하지 않습니다.

  * 해결
    1. video tag안에 *playsinline* 옵션을 추가합니다.
    ```js
    <video id="my-player" class="video-js" playsinline controls preload="auto" width="1200" height="650" />
    ```
    2. 전체화면 버튼을 삭제합니다.
    ```js
    player.ready( function() {
      var fullScreenControl = document.getElementsByClassName("vjs-fullscreen-control")[0];
      if ( iOS 일 경우 ) {
        fullScreenControl.parentNode.removeChild(fullScreenControl);
      }
    }
    ```
    
* Picture in Picture(PIP)
  * 문제  
  PIP 모드에서는 VisibleWatermark를 지원하지 않습니다. 따라서 사용자가 PIP 모드를 선택할 경우 재생이 취소되는 기능이 적용되어 있습니다.

  * 해결  
  사용자가 PIP 모드를 선택하지 못하도록 `<video>` 에 'disablePictureInPicture' 를 설정합니다.

***
