---
title: NCG Android SDK Guide
linktitle: NCG Android SDK
summary: This document describes how to use the libraries and sample projects included in the SDK.
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
lastmod: "2020-09-16T00:00:00Z"
draft: false

menu:
  multidrm:
    parent: Client Integration
    weight: 60

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 60
---

## Overview

PallyCon NCG Android SDK makes it easy to apply INKA's NCG(Netsync Content Guard) DRM when developing media service apps for Android. This document describes how to use the libraries and sample project included in the SDK.

Details of PallyCon Multi DRM service integration are provided in [License Token Guide](../../license/license-token) guides. For technical questions about using the SDK, please visit our [Helpdesk site](https://pallycon.zendesk.com).

> SDK products can be requested when subscribing to PallyCon cloud commercial service. Then SDK can be downloaded from the `Download` page of PallyCon Console site.

## Tutorial Video

This video is a tutorial for playing DRM content using the sample project included in the SDK.

{{< youtube iKzem0auGJI >}}

> For optimal playback, select '1080p' as the video quality and enable subtitle (Korean or English) before starting playback.

## Development Environment Setting

> **NOTE** :
> This document is prepared in accordance with the following environment.
> - **Android Developer Tools v22.3.0-887826**
> - **Android SDK 3.0**

To apply PallyCon NCG Android SDK to your project, you need to copy the `jar` and `so` files to the library folder (libs) of Application project. As shown in demo project, you can set project according to the following steps:

1. Copy `so` files to `libs/armeabi` folder.

2. Copy `ncg2sdk.jar` file to `libs` folder.

3. Add `ncg2sdk.jar` file to `Java Build Path`.

4. Check `ncg2sdk.jar` on `Order and Export` tab.

> **NOTE** :
> In case of managing `jar` files by generating another folder in `libs` folder within project, FATAL error may occur when running App.
> In this case, check `ncg2sdk.jar` on `Order and Export` tab.

### AndroidManifest.xml Permission Setting

For the use of PallyCon Android SDK, the below permissions are required:

|Item     | Description |
|:------- | :----- |
|READ_LOGS | Permission to read LOG |
|INTERNET     | Permission to use network |
|WRITE_EXTERNAL_STORAGE| Permission to use external storage |
|READ_PHONE_STATE | Permission to read the status of device |
|MOUNT_UNMOUNT_FILESYSTEMS | Permission to edit file system |

## Initialization

Only one NCG Core object is generated as a single tone.

```java
public class DemoLibrary {
    static Ncg2Agent g_ncgAgent = Ncg2SdkFactory.getNcgAgentInstance();
    public final static Ncg2Agent getNcgAgent() {
        return g_ncgAgent;
    }
    ...
}
```

In order to use `Ncg2Agent` object, it should be initialized by `init()` method. In general, `init()` method of `Ncg2Agent` may be called when the app starts(`onCreate` of `android.app.Application`).

|Item  | Description |
|:---- | :----- |
|NotSupportOffline | Not support offline mode |
|OfflineSupport | Support offline mode |

If you set the offline policy to `OfflineSupport` then you can limit the count of app execution.
The default limit count is `10` and the value can be changed by calling the `setCountOfExecutionLimit` method in `OfflineSupportPolicy` enum class.

```java
public class DemoApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        initializeNcgAgent();
    }
    private void initializeNcgAgent() {
        try {
            DemoLibrary.getNcgAgent().setHttpRequestCallback(mHttpRequestCallback);
            Ncg2Agent.OfflineSupportPolicy policy = Ncg2Agent.OfflineSupportPolicy.OfflineSupport;
            policy.setCountOfExecutionLimit(20);
            DemoLibrary.getNcgAgent().init(this, policy);
            DemoLibrary.getNcgAgent().enableLog();
        } catch (Ncg2Exception e) {
            e.printStackTrace();
            String errorMsg = "Exception in init() : " + e.getMessage();
            Log.e(DemoLibrary.TAG, errorMsg);
            Toast.makeText(this, errorMsg, Toast.LENGTH_LONG).show();
        }
    }
    ...
}
```

Controlling HTTP communication inside PallyCon NCG Android SDK requires implementing `HttpRequestCallback` in App and then initializing it before setting callback. You need to call `Ncg2Agent.setHttpRequestCallback` method to set `HttpRequestCallback` object.

```java
public class DemoApplication extends Application{
    private Ncg2Agent.HttpRequestCallback mHttpRequestCallback = new Ncg2Agent.HttpRequestCallback() {

        @Override
        public String sendRequest(String url, String param){
            ...
        }
        @Override
        public byte[] sendRequestResponseBytes(String url, String param) throws NcgHttpRequestException {
            ...
        }
        @Override
        public byte[] sendRequest(String url, String param, int begin, int end) throws NcgHttpRequestException {
            ...
        }
    }
    private void initializeNcgAgent() {
        ...
        DemoLibrary.getNcgAgent().setHttpRequestCallback(mHttpRequestCallback);
        ...
    }
 }
```

## License Management

### Checking License

A DRM license is required to play DRM-protected content. Licensed data contains the rights for content including usage period and whether TV-out is permitted, etc. So the service app should acquire DRM license from license server before playing the content. The below functions are provided to check the license data:

```java
interface Ncg2Agent{
    // Confirm whether a file situated in the entered path and URL is NCG file.
    public boolean isNcgContent(String strFileOrURL) throws Ncg2Exception;

    // Confirm the license of file situated in the entered path and URL for validity.
    public boolean isLicenseValid(String strFileOrURL) throws Ncg2Exception;

    // Confirm the license of content for validity.
    public LicenseValidation checkLicenseValid(String path) throws Ncg2Exception;
}
```

The `LicenseValidation` enum type returned from `checkLicenseValid` method is as follows:

|Item   | Description |
|:----- | :----- |
|ValidLicense | The license is valid. |
|NotExistLicense  | No license or invalid license |
|ExternalDeviceDisallowed| License with external output device disallowed |
|RootedDeviceDisallowed | License disallowed the use of rooted device |
|ExpiredLicense | The license is expired. |
|DeviceTimeModified | Detected device time manipulation |
|OfflineNotSupported | Failed to access server and detected offline while initializing under the policy of not supporting offline mode |
|OfflineStatusTooLong | Online connection is needed because of too many counts of execution offline. For more information, please refer to the following note. |
|NotAuthorizedAppID | Failed to initialize since the executing App is not authenticated in server. |
|ScreenRecorderDetected | Detected a screen recording app |

In Sample Project, confirm license for validity in the following order:

```java
if( DemoLibrary.getNcgAgent().isNcgContent(path) == false ) {
    return true;
}

Ncg2Agent.LicenseValidation validation = DemoLibrary.getNcgAgent().checkLicenseValid(path);

if( validation == LicenseValidation.ValidLicense ) {
    // Routine to process license for validity
    ...
} else if( validation == LicenseValidation.NotExistLicense ) {
    // No license or invalid license
    ...
    // Request license to license server
    DemoLibrary.getNcgAgent().acquireLicenseByPath( path, DemoLibrary.getUserID(), DemoLibrary.getOrderID() );
    if( DemoLibrary.getNcgAgent().checkLicenseValid(path) == LicenseValidation.ValidLicense ) {
        // Acquire valid license from server
    } else {
        // Not acquire license from server
    }
} else if( validation == LicenseValidation.ExternalDeviceDisallowed) {
    // Not allow to play to the connection to external device such as HDMI device
    ...
} else if( validation == LicenseValidation.RootedDeviceDisallowed) {
    // License not allowed in Rooting terminal.
    ...
} else if(validation == LicenseValidation.ExpiredLicense){
    // Expired license
    ...
} else if(validation == LicenseValidation.ScreenRecorderDetected {
    // You can check the package name and the appName of screen recorder app.
    HashMap<String,String> data = validation.getExtraData();
    String appName = data.get("AppName");
    String packageName = data.get("AppPackageName");
    ...
}
```

> **NOTE** :
> To detect the device time manipulation, check if it executes more than limit value set by `setCountOfExecutionLimit` method in the internal offline mode, OfflineStatusTooLong value is returned. `OfflineStatusTooLong` value is returned only when it is set as `OfflineSupport` parameter option at `init()` method called.

### Issuance and Renewal of License

DRM license may be acquired at any time before playing the content; however, SDK Sample App Project tries to issue license at the start of playback. The timing to issue license depends on App scenario. The below functions are provided in PallyCon NCG Android SDK to issue license:

```java
interface Ncg2Agent{
    // Acquire license via the entered path with UserID and OrderID.
    public void acquireLicenseByPath(String path, String userId, String orderId) throws Ncg2Exception;

    // Acquire license via the entered path with UserID and OrderID, there can be acquired temporary license by setting a temporary.
    public abstract void acquireLicenseByPath( String path, String userID, String orderID, boolean temporary ) throws Ncg2Exception;

    // Acquire license using Content ID.
    public void acquireLicenseByCID(String cid, String userID, String orderID, String acquisitionURL) throws Ncg2Exception;
    // Acquire license using Content ID.
    public void acquireLicenseByCID(String cid, String userID, String siteID, String orderID, String acquisitionURL) throws Ncg2Exception;

    // Acquire license using Content ID, there can be acquired temporary license by setting a temporary.
    public abstract void acquireLicenseByCID(String cid, String userID, String siteID, String orderID, String acquisitionURL, boolean temporary ) throws Ncg2Exception;
}
```

> **NOTE** :
> To support offline scenario, it is needed to acquire license when the content is downloaded online.

### Removing License

One-time license may be removed at any time after use. In the SDK Sample, license is removed when the video play is stopped. The below functions are provided in PallyCon NCG Android SDK:

```java
interface Ncg2Agent{
    // Delete license via all CIDs of the issued licenses.
    public void removeLicenseAllCID() throws Ncg2Exception;

    // Delete temporary license.
    public abstract void removeAllTemporaryLicense() throws Ncg2Exception;

    // Delete license via CID.
    public void removeLicenseByCID(strContentsID) throws Ncg2Exception;

    // Delete license of the content in the file path.
    public void removeLicenseByPath(strFilename) throws Ncg2Exception;
}
```

## Playing Content

To play content after acquiring license, you can control the local web server by using `Ncg2LocalWebServer` interface. When you specify the path of DRM content in the playback API, the virtual URL of the local web server is returned, and then you can play the URL with MediaPlayer or third party player. Below is the virtual URL functions provided by `Ncg2LocalWebServer`.

```java
interface Ncg2LocalWebServer{
    // In case of Local File,
    public String addLocalFilePathForPlayback(Activity activity, String url, long fileSize) throws Ncg2InvalidLicenseException, Ncg2Exception;

    // In case of PD contents,
    public String addProgressiveDownloadUrlForPlayback(Activity activity, String url) throws Ncg2InvalidLicenseException, Ncg2Exception;

    // In case of HLS,
    public String addHttpLiveStreamUrlForPlayback(Activity activity, String url) throws Ncg2InvalidLicenseException, Ncg2Exception;

    // In case of HLS (live),
    public String addHttpLiveStreamUrlForPlayback(Activity activity, String url, boolean isLiveHLS) throws Ncg2InvalidLicenseException, Ncg2Exception;

    // In case of HLS,
    // unlike addHttpLiveStreamUrlForPlayback(), do not prior check key file equivalent to m3u8 path.
    public String addHttpLiveStreamUrlForPlaybackWithoutChecking(Activity activity, String url) throws Ncg2InvalidLicenseException, Ncg2Exception;

    // In case of HLS,
    // You can call this method to add HLS url without accessing the URLs of m3u8 and key file.
    public String addHttpLiveStreamUrlForPlayback(Activity activity, String url, String cid) throws Ncg2InvalidLicenseException, Ncg2Exception;

    // In case of HLS (live),
    // You can call this method to add HLS url without accessing the URLs of m3u8 and key file.
    public String addHttpLiveStreamUrlForPlayback(Activity activity, String url, String cid, boolean isLiveHLS) throws Ncg2InvalidLicenseException, Ncg2Exception;
}
```

The following code shows how to obtain virtual URL from PallyCon contents.

```java
public class PlayerActivity extends Activity {
    ...
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        Ncg2Agent ncgAgent = Ncg2SdkFactory.getNcgAgentInstance();
        Ncg2LocalWebServer ncgLocalWebServer = ncgAgent.getLocalWebServer();
        ncgLocalWebServer.setWebServerListener(mWebServerCallback);
        ncgLocalWebServer.clearPlaybackUrls();
        try {
            if( mNcgFilePath.contains(".m3u8") ) {
                // HLS Playback
                mPlaybackURL = ncgLocalWebServer.addHttpLiveStreamUrlForPlayback(mNcgFilePath);
            } else if(mNcgFilePath.startsWith("http://")) {
                // PD Playback
                mPlaybackURL = ncgLocalWebServer.addProgressiveDownloadUrlForPlayback(mNcgFilePath);
            } else {
                // Local Playback
                mPlaybackURL = ncgLocalWebServer.addLocalFilePathForPlayback(mNcgFilePath, mNcgFileSize);
            }
        } catch (Ncg2Exception e) {
            e.printStackTrace();
            Toast.makeText(MediaPlayerActivity.this, "[onCreate] Error Occurred. : " + e.getMessage(), Toast.LENGTH_LONG).show();
            return;
        }
    }
}
```

The following code shows how to decrypt and use virtual URL.

```java
try {
    mPlayer.setDataSource(mPlaybackURL);
    mPlayer.prepareAsync();
} catch (IllegalArgumentException e) {
    e.printStackTrace();
    errorMsg = e.getMessage();
} catch (IllegalStateException e) {
    e.printStackTrace();
    errorMsg = e.getMessage();
} catch (IOException e) {
    e.printStackTrace();
    errorMsg = e.getMessage();
}
```

### LocalWebserver Callback

PallyCon NCG Android SDK utilizes local web server in a proxy server. The event data from the web server can be received via `WebServerListener` interface. As shown in the following codes, you can process notice or error of `Ncg2LocalWebserver`.

> **NOTE** :
> You need to check and return the status of player by using `onCheckPlayerStatus()` method.

```java
private Ncg2LocalWebServer.WebServerListener mWebServerCallback = new Ncg2LocalWebServer.WebServerListener(){
    @Override
    public void onNotification(int notificationCode) {
        //Most of notice codes can be ignored. ⧸⧸⧸
    }

    @Override
    public void onError(int errorCode, String errorMessage) {
        //Show the applicable user error message.
    }

    @Override
    public PlayerState onCheckPlayerStatus(String uri) {
        if (mIsPrepared) {
            // Return PlayerState.ReadyToPlay only when the player can start play.
            return PlayerState.ReadyToPlay;
        } else {
            // Block playing when PlayerState.Fail is returned.
            return PlayerState.Fail;
        }
    }
};
```

> **NOTE** :
> In order to prevent the case that a hacker accesses content data via virtual URL, the state of player should be monitored.
> If the content is accessed via virtual URL but it is not playing, the app should stop the process.

### File Decryption

PallyCon NCG Android SDK provides the function to also decrypt NCG DRM files. `Ncg2Agent` can return `NcgFile` object to open and read decrypted data from NCG file. The SDK provides the following functions for NCG file decryption:

```java
interface Ncg2Agent{
    //Return NcgFile object.
    public NcgFile createNcgFile();

    interface NcgFile{
        //Open NCG file.
        public void open(String path) throws Ncg2InvalidLicenseException, Ncg2InvalidNcgFileException, Ncg2Exception;
        //Set license and open NCG file.
        public void open(String path, boolean prepare) throws Ncg2InvalidLicenseException, Ncg2InvalidNcgFileException, Ncg2Exception;
        //Call the applicable method in a status after open and make it a status to enable decrypting. 
        public void prepare() throws Ncg2InvalidLicenseException, Ncg2InvalidNcgFileException, Ncg2Exception;
        //Close the opened file.
        public void close();
        //Read content data from NCG file.
        public long read(byte[] buff, long sizeToRead) throws Ncg2Exception;
        //Move the location of file pointer to read.
        public void seek(long offset, SeekMethod seekMethod) throws Ncg2Exception;
        //Return the current location of file pointer.
        public long getCurrentFilePointer() throws Ncg2Exception;
        //Return the size of NCG file header.
        public int getHeaderSize() throws Ncg2Exception;
        //Return InputStream for NCG file.
        public InputStream getInputStream() throws Ncg2Exception;
    }
}
```

The following is a sample code to get decrypted content using `NcgFile` object.

```java
try {
    String ncgFilePath = mFilePath;
    ncgFile = DemoLibrary.getNcgAgent().createNcgFile();
    ncgFile.open(ncgFilePath);
    ncgFile.seek(0, SeekMethod.End);

    // the original file's size will be used for checking success of decryption.
    long contentFileSize = ncgFile.getCurrentFilePointer();
    ncgFile.seek(0, SeekMethod.Begin);
    int pos = mFilePath.lastIndexOf("/");

    // removes ".ncg" in the path of NCG file
    String unpackFilePath = ncgFilePath.substring(0, pos+1) + "unpackFile.mp4";
    fileOutStream = new BufferedOutputStream(new FileOutputStream(unpackFilePath));
    long totalReadBytes = 0;
    while( true ) {
        long readBytes = ncgFile.read(buffer, 1024);
        if( readBytes <= 0 ) {
            break;
        }
        fileOutStream.write(buffer, 0, (int)readBytes);
        totalReadBytes += readBytes;
    }
    if( totalReadBytes == contentFileSize ) {
        Log.d(DemoLibrary.TAG, "Decryption succeeded");
    } else {
        Log.d(DemoLibrary.TAG, "Decryption failed");
        Toast.makeText(mainActivity, "[unpackNcgFiles] decryption failed", Toast.LENGTH_LONG).show();
    }
} catch (Ncg2Exception e) {

}
```

> **NOTE** :
> It is recommended to decrypt and use the data only in memory, because there is a risk to expose original file if you save decrypted data to a file.

## Releasing Object

After `Ncg2Agent` object ends its use, `release()` method should be called for object release. The `release()` method should be called when the app is closed. (i.e., when a user explicitly terminates the app). SDK sample app shows a dialog in `OnBackPressed()` method to call `release()` method.

```java
case DIALOG_EXIT :
    builder.setMessage( getString( R.string.confirm_end ));
    builder.setPositiveButton( getString( R.string.yes ), new DialogInterface.OnClickListener() {
        @Override public void onClick(DialogInterface dialog, int which) {
            ...
            finish();
            DemoLibrary.getNcgAgent().release();
        }
    });
    builder.setNegativeButton( R.string.no, null );
    dialog = builder.create();
    break;
```

> **NOTE** :
> There is a case to call `Ncg2Agent.release` method in `onTerminate` method of the app. However, `onTerminate` method is not always called explicitly so it is not recommended to call `Ncg2Agent.release` method in `onTerminate`.

## Processing Error

Errors in PallyCon NCG Android SDK is processed through exception. There is `Ncg2Exception` class which is a basic exception class; and then there exist a variety of separate exception classes which inherit the basic class. 

`NcgException` class is a basic exception class of PallyCon NCG Android SDK, which has internally `ErrorCode` and `Error Message` as its members.
If `ErrorCode` is set, `ErrorCode` can be confirmed with `getErrorCode()` method; if `ErrorCode` is not set, -1 is returned.
`Error Message` can be confirmed with `getMessage()` method.
The following is an example code to catch `Ncg2Exception` exception when calling for the case to use `setDataSource()` method of Ncg2Player.

```java
try {
    mPlayer.setDataSource(mNcgFilePath, mNcgFileSize);
    mPlayer.prepareAsync();
} catch (Ncg2Exception e) {
    e.printStackTrace();
    errorMsg = e.getMessage();
} catch (IllegalArgumentException e) {
    e.printStackTrace();
    errorMsg = e.getMessage();
} catch (IllegalStateException e) {
    e.printStackTrace();
    errorMsg = e.getMessage();
} catch (IOException e) {
    e.printStackTrace();
    errorMsg = e.getMessage();
}
```
