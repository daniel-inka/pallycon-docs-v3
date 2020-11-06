---
title: "NCG iOS SDK Guide"
date: 2018-08-28T16:12:37+09:00
draft: false
weight: 60
---

## Overview

PallyCon NCG iOS SDK makes it easy to apply INKA's NCG(Netsync Content Guard) DRM when developing media service apps for iOS. This document describes how to use the libraries and sample project included in the SDK.

Details of PallyCon Multi DRM service integration are provided in [License Token Guide]({{%ref "license-token.en.md"%}}) guides. For technical questions about using the SDK, please visit our [Helpdesk site](https://pallycon.zendesk.com).

> SDK products can be requested when subscribing to PallyCon cloud commercial service. Then SDK can be downloaded from the `Download` page of PallyCon Console site.

## Supported environment

- iOS 9.0 or later
- ARC(Automatic Reference Counting) required
- This SDK has been tested in `Xcode 9.4` and will not work on the simulator.

## Playback Scenario

The playback scenarios and content types supported by the NCG iOS SDK are:

- Local download : MP4 video encrypted by NCG DRM (*.mp4.ncg file)
- Progressive Download : MP4 video encrypted by NCG DRM (*.mp4.ncg file)
- HLS Streaming : HLS(HTTP Live Streaming) content protected by NCG DRM

### Local Download

- MP4 content encrypted by NCG is downloaded to local storage and played back.

### Progressive Download

- MP4 content encrypted by NCG is streamed directly from a remote server.

### HLS Streaming

- HLS content protected by NCG is streamed from a remote server.

## Tutorial Video

This video is a tutorial for playing DRM content using the sample project included in the SDK.

{{< youtube v7OlrFK2JpU >}}

> For optimal playback, select '1080p' as the video quality and enable subtitle (Korean or English) before starting playback.

## Project Settings

You can add the NCG iOS SDK to your project by following these steps:

1. Download and unzip the NCG iOS SDK zip file.
2. Add the `iOSNcgSDK.framework` library in the `lib` folder to your project.
 - Add `iOSNcgSDK.framework` to `Linked Frameworks and Libraries` in the `General` tab of the Project TARGETS section.
 - Add `libz.tbd` and `libstdc++.Tbd` that are referenced in `iOSNcgSDK.framework` to `Linked Frameworks and Libraries` by clicking on the `General` tab of project TARGETS.
 - Put the `Framework Search Paths` framework path in the `Build Settings` tab of the project TARGETS section.
3. Set `Enable Bitcode` to `No` in the `Build Settings` tab of project TARGETS section.

## SDK Workflow

1. Initialize SDK
2. Check DRM license of content
 - If license data already exist, validate the license
 - If there is no license data, acquire license from PallyCon server
3. Playback content
 - Play DRM content using the valid license

## Development using Objective-C

NCG iOS SDK was developed as `Objective-C`, so if your application is based on `Objective-C`, it can be used without additional processing.

### Objective-C Sample Code

Below is a simple `Objective-C` code that plays NCG content using the NCG iOS SDK.

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

## Development using Swift

This section describes how to reference the NCG iOS SDK in the Swift environment and the SDK Sample-Swift.

### Objective-C Bridging Header

The NCG iOS SDK is implemented in `Objective-C`, so in the Swift project, you need to create a Bridging header to access the NCG iOS SDK.

> **NOTE:**
>
> Please refer to the `Importing Objective-C into Swift` section in the Apple document [Swift and Objective-C in the Same Project](https://developer.apple.com/library/content/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html).

The Bridging Header.h file to connect the NCG SDK library is available through the `Objective-C` file.

1. Select `iOS` `Cocoa Touch Class` or `Objective-C File` from the project menu and select `File`-> `New`-> `File ...` and click the `Next` button. The documentation is based on `Cocoa Touch Class` generation.

2. Select the `Class` name, select `Objective-C` from the `Language` and click the `Next` button.

3. Click on `Create Bridging Header` to create an Objective-C bridging header.

4. Objective-C files and `Project Name-Swift-Bridging-Header.h` files will be created. You can use the NCG SDK library by opening `Project Name-Swift-Bridging-Header.h` and `# import` the NCG SDK library.

```objectivec
// NCG PallyCon SDK
#import <iOSNcgSDK/iOSNcgSDK.h>
```

> NOTE:
> - If the NCG SDK is not accessible, make sure that the file path `Project Name-Swift-Bridging-Header.h` is correctly entered in `Build Settings` -> `Objective-C Bridging Header`.
> - If the file path of `Project Name-Swift-Bridging-Header.h` is not entered, enter it manually.

### Swift Sample Code

Below is a simple Swift sample code to play NCG content using the NCG iOS SDK.

```swift
class ViewController: UIViewController {
	override func viewDidLoad() {
		super.viewDidLoad()
		
		let UserID = "User ID"
		let ContentURL = "NCG Content URL";
		let OptionalID = "NCG Content Optional ID";
		let playerViewController = AVPlayerViewController()
		do {
			// 1. Create and initialize an NCG SDK object.
			try (Ncg2Agent.sharedInstance() as! Ncg2Agent).initialize(OfflineSupportYes, rodbPath: nil, deviceId: nil)
			// 2. Obtain the license for the content you want to play.
			try (Ncg2Agent.sharedInstance() as! Ncg2Agent).acquireLicense(byPath: ContentURL, userID: UserID, orderID: OptionalID, isTemporary: true)
			// 3. You must specify a delegate to receive information from the local Web server and implement the WebServerDelegate protocol.
			(Ncg2Agent.sharedInstance() as! Ncg2Agent).getLocalWebServerInstance().setWebServerDelegate(self  as  WebServerDelegate)
			// 4. Start the local web server.
			try (Ncg2Agent.sharedInstance() as! Ncg2Agent).getLocalWebServerInstance().start()
			// 5. Obtain the local web server address to play.
			let playUrl = try (Ncg2Agent.sharedInstance() as! Ncg2Agent).getLocalWebServerInstance().addHttpLiveStreamUrl(forPlayback: ContentURL)
			present(playerViewController, animated: true, completion: nil)
			// 6. Sets the playback URL in the Player.
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

## Detailed Development Guide and API Reference

For more detailed implementation guides and API references, please refer to the documentation included with the SDK.

***
