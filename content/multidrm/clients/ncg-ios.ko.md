---
title: "NCG iOS SDK 가이드"
date: 2018-08-28T16:12:37+09:00
draft: false
weight: 60
---

## 개요

PallyCon NCG iOS SDK는 iOS용 미디어 서비스 앱을 개발할 때 잉카엔트웍스의 NCG(Netsync Content Guard) DRM을 쉽게 적용할 수 있게 해주는 제품입니다. 본 문서는 SDK에 포함된 라이브러리와 샘플 프로젝트의 사용법에 대해 설명합니다.

NCG DRM 클라이언트와 연동되는 PallyCon 멀티 DRM 서비스에 대한 설명은 [라이선스 토큰 가이드]({{%ref "license-token.ko.md"%}})를 참고하시기 바랍니다.
SDK 사용과 관련한 기술 문의는 [헬프데스크](https://pallycon.zendesk.com) 사이트를 이용해 주시기 바랍니다.

> **NOTE** :
> SDK 제품은 PallyCon 클라우드 상용 서비스 가입 시 신청할 수 있으며, 신청한 SDK는 PallyCon 콘솔 사이트의 `다운로드` 페이지에서 다운로드 받을 수 있습니다.

## 지원 환경

- iOS 9.0 이상
- ARC(Automatic Reference Counting) 적용 필요
- 본 SDK는 `Xcode 9.4`에서 테스트 되었으며, 시뮬레이터에서 동작하지 않습니다.

## 재생 시나리오

NCG iOS SDK에서 지원하는 재생 시나리오와 콘텐츠 유형은 다음과 같습니다.

- 로컬 다운로드 재생 : mp4 동영상을 암호화한 NCG 콘텐츠 (*.mp4.ncg 파일)
- 프로그래시브 다운로드 : mp4 동영상을 암호화한 NCG 콘텐츠 (*.mp4.ncg 파일)
- HLS 스트리밍 : NCG DRM으로 보호된 HLS(HTTP Live Streaming) 콘텐츠

### 로컬 다운로드 재생

- NCG로 암호화된 MP4 콘텐츠를 로컬 스토리지에 다운로드 받은 후 재생하는 시나리오입니다.

### 프로그래시브 다운로드

- NCG로 암호화된 MP4 콘텐츠를 원격 서버에서 스트리밍 방식으로 직접 재생하는 시나리오입니다.

### HLS 스트리밍

- NCG DRM으로 보호된 HLS 콘텐츠를 스트리밍으로 재생하는 시나리오입니다.

## 동영상 튜토리얼

SDK에 포함된 샘플 프로젝트를 사용하여 DRM 콘텐츠를 재생하는 튜토리얼 영상입니다.

{{< youtube v7OlrFK2JpU >}}

> 최적의 재생을 위해 화면 품질을 '1080p'로 선택하고 자막(한글 또는 영문)을 선택하여 재생하시기 바랍니다.

## 프로젝트 설정

다음과 같은 과정으로 NCG iOS SDK를 프로젝트에 추가할 수 있습니다.

1. NCG iOS SDK zip 파일을 다운로드 받아 압축을 풉니다.
2. `lib` 폴더의 `iOSNcgSDK.framework` 라이브러리를 프로젝트에 추가합니다.
 - 프로젝트 TARGETS 항목의 `General` 탭에 `Linked Frameworks and Libraries`에 `iOSNcgSDK.framework` 를 추가합니다.
 - 프로젝트 TARGETS 항목의 `General` 탭에 `Linked Frameworks and Libraries`에 `iOSNcgSDK.framework`에서 참조하는 `libz.tbd`, `libstdc++.tbd`를 `+` 눌러 추가합니다.
 - 프로젝트 TARGETS 항목의 `Build Settings` 탭에 `Framework Search Paths` 프레임웍 경로를 넣습니다.
3. 프로젝트 TARGETS 항목의 `Build Settings` 탭 `Enable Bitcode`를 `No`로 설정합니다.

## SDK 워크플로우

1. SDK를 초기화합니다.
2. 재생할 DRM 콘텐츠의 라이선스를 확인합니다.
 - 만약 라이선스가 있다면 유효한지 확인합니다.
 - 라이선스가 없다면 라이선스 서버에 라이선스를 요청해 발급 받습니다.
3. 콘텐츠를 재생합니다.
 - 유효한 라이선스를 이용해 DRM 콘텐츠를 재생합니다.

## Objective-C를 이용한 개발

NCG iOS SDK는 `Objective-C`로 개발되었으므로, 구현하는 어플리케이션이 `Objective-C` 기반이라면 추가적인 처리 없이 사용 가능합니다.

### Objective-C 샘플 코드

아래는 NCG iOS SDK를 이용하여 NCG 콘텐츠를 재생하는 간단한 `Objective-C` 코드입니다.

```objectivec
// ViewController.h
#import <UIKit/UIKit.h>
#import <AVFoundation/AVFoundation.h>
#import <AVKit/AVKit.h>
#import <iOSNcgSDK/iOSNcgSDK.h>

@interface ViewController : UIViewController<WebServerDelegate>

@property (strong, nonatomic) Ncg2Agent * ncg2agent;
@property (strong, nonatomic) AVPlayerViewController *playerViewController;

@end
```

```objectivec
// ViewController.m
#import "ViewController.h"

#define USER_ID @"User id"
#define CONTENT_URL @"NCG Content URL or Path"
#define OPTIONAL_ID @"NCG Content Optional id"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    NSError * error = nil;
    // 1. Ncg2Agent Instance
    self.ncg2agent = [Ncg2Agent sharedInstance];
    // 2. initialize
    [self.ncg2agent initialize:OfflineSupportYes rodbPath:nil deviceId:nil error:&error];
    // 3. content license acquisition  
    [self.ncg2agent acquireLicenseByPath:CONTENT_URL userID:USER_ID orderID:OPTIONAL_ID isTemporary:YES error:&error];
    // 4. local web server delegate and start
    [self.ncg2agent.getLocalWebServerInstance setWebServerDelegate:self];
    [self.ncg2agent.getLocalWebServerInstance startWebserver:&error];
    // 5. Get a playback URL
    NSString* playUrl = [self.ncg2agent.getLocalWebServerInstance addProgressiveDownloadUrlForPlayback:CONTENT error:&error];
    if(error != nil) {
        NSLog(@"Error : %ld", (long)error.code);
    }
    
    // AV Player
    self.playerViewController = [[AVPlayerViewController alloc] init];
    AVPlayer *player = [AVPlayer playerWithURL:[NSURL URLWithString:playUrl]];
    self.playerViewController.player = player;
    [self.playerViewController.view setFrame:CGRectMake(0, 0, self.view.frame.size.width, self.view.frame.size.height)];
    [self.view addSubview:self.playerViewController.view];
    [player play];
}

// Local Web Server Protocol
- (PlayerState)onCheckPlayerStatus:(NSString *)uri {
	// if you have not been prepared you should return 'PlayerStateFail' 
	// instead of 'PlayerStateReadyToPlay'
    return PlayerStateReadyToPlay;
}

- (void)onError:(int)errorCode errorMessage:(NSString *)errorMessage {
    NSLog(@"Error %@", errorMessage);
}

- (void)onNotification:(int)notificationCode notifyMessage:(NSString *)notifyMessage {
    NSLog(@"Error %@", notifyMessage);
}

@end
```

## Swift를 이용한 개발

본 항목에서는 Swift 환경에서 NCG iOS SDK를 참조하는 방법과 SDK Sample-Swift에 대해 설명합니다.

### Objective-C Bridging Header

NCG iOS SDK는 `Objective-C`로 구현되어 있으므로, Swift 프로젝트에서는 Bridging header를 생성해야 NCG iOS SDK에 접근할수 있습니다.

> **NOTE:**
> 
> * 애플 문서 [Swift and Objective-C in the Same Project](https://developer.apple.com/library/content/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html)에 `Importing Objective-C into Swift` 항목을 참고하시기 바랍니다.

NCG SDK 라이브러리를 연결할 Bridging Header.h 파일은 `Objective-C` 파일을 통해 가능합니다.

1. 프로젝트 메뉴에서 `File`->`New`->`File...` 선택하여 `iOS` `Cocoa Touch Class` 또는 `Objective-C File` 를 선택하고 `Next` 버튼을 클릭합니다. 문서에서는 `Cocoa Touch Class` 생성을 기준으로 설명합니다.

2. `Class` 이름을 선정하고 `Language` 에서 `Objective-C`를 선택하고 `Next` 버튼을 클릭합니다.

3. Objective-C bridging header를 생성할것인지 물어보는데 `Create Bridging Header`를 클릭하여 생성합니다.

4. Objective-C 파일과 `Project Name-Swift-Bridging-Header.h` 파일이 생성됩니다. `Project Name-Swift-Bridging-Header.h` 파일을 열어 NCG SDK 라이브러리를 `#import` 하면 NCG SDK 라이브러리를 사용할 수 있습니다.

```objectivec
// NCG PallyCon SDK
#import <iOSNcgSDK/iOSNcgSDK.h>
```

> NOTE:  
> - 만약 NCG SDK에 접근이 되지 않는다면 `Build Settings` -> `Objective-C Bridgin Header`에 `Project Name-Swift-Bridging-Header.h` 파일 경로가 정확히 입력되어 있는지 확인합니다.
> - `Project Name-Swift-Bridging-Header.h` 파일 경로가 입력되지 않았다면 직접 입력합니다.

### Swift 샘플 코드

아래는 NCG iOS SDK를 이용하여 NCG 콘텐츠를 재생하는 간단한 Swift 샘플코드입니다.

```swift
class ViewController: UIViewController {
	override func viewDidLoad() {
		super.viewDidLoad()
		
		let UserID = "User ID"
		let ContentURL = "NCG Content URL";
		let OptionalID = "NCG Content Optional ID";
		let playerViewController = AVPlayerViewController()
		do {
			// 1. NCG SDK 객체 생성하고 초기화합니다.
			try (Ncg2Agent.sharedInstance() as! Ncg2Agent).initialize(OfflineSupportYes, rodbPath: nil, deviceId: nil)
			// 2. 재생하고자 하는 컨텐츠의 라이선스를 획득합니다.
			try (Ncg2Agent.sharedInstance() as! Ncg2Agent).acquireLicense(byPath: ContentURL, userID: UserID, orderID: OptionalID, isTemporary: true)
			// 3. 로컬 웹서버의 정보를 받을 delegate를 지정하고 WebServerDelegate 프로토콜을 구현해야 합니다.
			(Ncg2Agent.sharedInstance() as! Ncg2Agent).getLocalWebServerInstance().setWebServerDelegate(self  as  WebServerDelegate)
			// 4. 로컬 웹서버를 start 합니다.
			try (Ncg2Agent.sharedInstance() as! Ncg2Agent).getLocalWebServerInstance().start()
			// 5. 재생할 로컬 웹서버 주소를 획득합니다.
			let playUrl = try (Ncg2Agent.sharedInstance() as! Ncg2Agent).getLocalWebServerInstance().addHttpLiveStreamUrl(forPlayback: ContentURL)
			present(playerViewController, animated: true, completion: nil)
			// 6. Player에 재생 URL을 설정합니다.
			let player = AVPlayer(url: URL(string: playUrl)!)
			playerViewController.player = player
			player.play()
		} catch {
			print("\(error)")
		}
	}
}

// WebServerDeleagate
extension  ViewController: WebServerDelegate {
	func onNotification(_ notificationCode: Int32, notifyMessage: String!) {
		print("\(notificationCode):\(notifyMessage)")
	}
	func onError(_ errorCode: Int32, errorMessage: String!) {
		print("\(errorCode):\(errorMessage)")
	}
	func onCheckPlayerStatus(_ uri: String!) -> PlayerState {
		// if you have not been prepared you should return 'PlayerStateFail' 
		// instead of 'PlayerStateReadyToPlay'
		return  PlayerStateReadyToPlay
	}
}
```

## 상세 개발 가이드 및 API 레퍼런스

보다 상세한 구현 가이드와 API 레퍼런스는 SDK에 포함된 문서를 참고하시기 바랍니다.

***
