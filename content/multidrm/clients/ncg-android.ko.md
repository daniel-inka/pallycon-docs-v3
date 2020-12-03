---
title: NCG Android SDK 가이드
linktitle: NCG Android SDK
summary: 본 문서는 NCG Android SDK에 포함된 라이브러리와 샘플 프로젝트의 사용법을 설명합니다.
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
lastmod: "2020-09-16T00:00:00Z"
draft: false

menu:
  multidrm:
    parent: 클라이언트 연동
    weight: 60

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 60
---

## 개요

PallyCon NCG Android SDK는 안드로이드용 미디어 서비스 앱을 개발할 때 잉카엔트웍스의 NCG(Netsync Content Guard) DRM을 쉽게 적용할 수 있게 해주는 제품입니다. 본 문서는 SDK에 포함된 라이브러리와 샘플 프로젝트의 사용법에 대해 설명합니다.

NCG DRM 클라이언트와 연동되는 PallyCon 멀티 DRM 서비스에 대한 설명은 [라이선스 토큰 가이드](../../license/license-token)를 참고하시기 바랍니다.
SDK 사용과 관련한 기술 문의는 [헬프데스크](https://pallycon.zendesk.com) 사이트를 이용해 주시기 바랍니다.

> **NOTE** :
> SDK 제품은 PallyCon 클라우드 상용 서비스 가입 시 신청할 수 있으며, 신청한 SDK는 PallyCon 콘솔 사이트의 `다운로드` 페이지에서 다운로드 받을 수 있습니다.

***

## 동영상 튜토리얼

SDK에 포함된 샘플 프로젝트를 사용하여 DRM 콘텐츠를 재생하는 튜토리얼 영상입니다.

{{< youtube iKzem0auGJI >}}

> 최적의 재생을 위해 화면 품질을 '1080p'로 선택하고 자막(한글 또는 영문)을 선택하여 재생하시기 바랍니다.

## 개발 환경 설정

> 본 문서는 아래와 같은 환경을 기준으로 작성되었습니다.
> - **Android Developer Tools v22.3.0-887826**
> - **Android SDK 3.0**

PallyCon NCG Android SDK를 개발 중인 프로젝트에 적용하려면, SDK의 라이브러리 파일들을 해당 프로젝트에 복사 및 설정해 주어야 합니다.
아래와 같은 단계를 통해 SDK를 프로젝트에 적용할 수 있습니다.

1. SDK에 포함된 `so` 파일들을 개발 프로젝트의 `libs/armeabi` 폴더에 복사합니다.

2. `ncg2sdk.jar` 파일과 `sqlcipher.jar` 파일을 `libs` 폴더에 복사합니다.

3. 프로젝트 설정 화면의 `Java Build Path` 에 `ncg2sdk.jar` 파일을 추가합니다.

4. 프로젝트 설정 화면의 `Order and Export` 탭에서 `ncg2sdk.jar`를 체크합니다.

> > **NOTE** : 프로젝트 내 `libs` 폴더안에 또 다른 폴더를 생성하여 jar파일을 관리하는 경우에는 App 실행 시 FATAL 에러가 발생할 가능성이 있습니다.
> 이러한 경우에는 `Order and Export` 탭에서 `ncg2sdk.jar` 를 체크해 줍니다.

### AndroidManifest.xml 권한 설정

PallyCon NCG Android SDK를 적용할 어플리케이션은 기본적으로 다음 권한들이 요구됩니다.

|Item   | Description |
|:----- | :----- |
|READ_LOGS | LOG를 읽을 수 있는 권한 |
|INTERNET     | 네트워크 사용 권한 |
|WRITE_EXTERNAL_STORAGE| 외부 storage를 쓸 수 있는 권한 |
|READ_PHONE_STATE | 기기의 상태를 읽을 수 있는 권한 |
|MOUNT_UNMOUNT_FILESYSTEMS | 파일 시스템 편집 권한 |

## 라이브러리 초기화

NCG DRM Core 객체는 싱글톤으로, APP이 구동되는 동안 하나의 객체만이 생성됩니다.

```java
public class DemoLibrary {
    static Ncg2Agent g_ncgAgent = Ncg2SdkFactory.getNcgAgentInstance();
    public final static Ncg2Agent getNcgAgent() {
        return g_ncgAgent;
    }
    ...
}
```

`Ncg2Agent` 객체를 사용하기 위해서는 `init()` 메소드를 통한 초기화 작업을 수행하여야 합니다. 일반적으로 `Ncg2Agent`의 `init()` 메소드는 앱이 시작되는 시점 (`android.app.Application`의 `onCreate` 메소드)에서 호출해 줍니다. `Ncg2Agent` 객체가 초기화되지 않았거나, 이미 릴리즈 된 상태에서 내부 메소드들이 호출되면 오류가 발생되므로 주의해야 합니다.

| Item   | Description |
|:------ | :----- |
| NotSupportOffline | 오프라인 환경 지원 안함 |
| OfflineSupport | 오프라인 환경 지원 |

오프라인 지원 정책을 `OfflineSupport`로 설정하는 경우 오프라인에서 연속으로 실행되는 횟수를 제한할 수 있는 방법을 제공합니다. 기본으로 셋팅된 실행 제한 횟수는 `10`이며 이 값은 `OfflineSupportPolicy` enum 클래스의 `setCountOfExecutionLimit` 메소드를 통해 설정 가능합니다.

```java
public class DemoApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        initializeNcgAgent();
    }

    void initializeNcgAgent() {
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

PallyCon NCG Android SDK 내부에서 HTTP 통신 제어를 위해서는 앱에서 `HttpRequestCallback`을 구현하여 초기화 이후 Callback을 설정해야 합니다. `HttpRequestCallback` 콜백 객체를 설정해주기 위해서는 `Ncg2Agent.setHttpRequestCallback` 메소드를 사용합니다.

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

## 라이선스 관련

### 라이선스 확인

DRM이 적용된 콘텐츠를 재생하려면 라이선스 서버로부터 DRM 라이선스를 발급받아야 합니다. 라이선스 파일은 유효 기간,  TV-out 허용 여부 등 콘텐츠의 권한이 포함되어 있습니다. 라이선스 확인을 위해 PallyCon NCG Android SDK에서 제공하는 함수는 다음과 같습니다.

```java
interface Ncg2Agent{
    // 입력된 경로 및 URL에 위치한 파일이 NCG 파일 인지를 확인한다.
    public boolean isNcgContent(String strFileOrURL) throws Ncg2Exception;

    // 입력된 경로 및 URL에 위치한 파일의 라이선스가 유효한지 확인한다.
    public boolean isLicenseValid(String strFileOrURL) throws Ncg2Exception;

    // 콘텐츠의 라이선스 유효성을 확인한다.
    public LicenseValidation checkLicenseValid(String path) throws Ncg2Exception;
}
```

`checkLicenseValid` 메소드에서 반환되는 `LicenseValidation` enum 타입의 값은 다음과 같습니다.

|Item   | Description |
|:----- | :----- |
|ValidLicense | 유효한 라이선스 |
|NotExistLicense     | 라이선스가 없거나 유효하지 않음 |
|ExternalDeviceDisallowed| 외부출력 장치가 금지된 라이선스 |
|RootedDeviceDisallowed | 루팅된 단말에서 금지된 라이선스 |
|ExpiredLicense | 기간이 만료된 라이선스 |
|DeviceTimeModified | 디바이스 시간이 변경된 것을 감지한 경우 |
|OfflineNotSupported | 오프라인 환경을 지원하지 않는 정책으로 초기화를 시도하는 과정에서, 서버와 접속이 실패되어 오프라인으로 감지된 경우 |
|OfflineStatusTooLong | 오프라인으로 실행된 횟수가 제한을 초과해 온라인 접속이 필요한 상태. 자세한 내용은 아래 노트에서 확인 |
|NotAuthorizedAppID | 실행되고 있는 App이 서버에서 인증되지 않은 App이므로 초기화가 실패된 경우 |
|ScreenRecorderDetected | 스크린 레코더 앱이 감지된 경우 |

Sample Project에서는 아래와 같은 순서로 라이선스의 유효성을 확인합니다.

```java
    if( DemoLibrary.getNcgAgent().isNcgContent(path) == false ) {
        return true;
    }

    Ncg2Agent.LicenseValidation validation = DemoLibrary.getNcgAgent().checkLicenseValid(path);
        if( validation == LicenseValidation.ValidLicense ) {
            // 라이선스 유효 처리 루틴
            ...

        } else if( validation == LicenseValidation.NotExistLicense ) {
            // 라이선스가 없거나 유효하지 않음
            ...

            // 라이선스 서버에 라이선스 요청
            DemoLibrary.getNcgAgent().acquireLicenseByPath( path, DemoLibrary.getUserID(), DemoLibrary.getOrderID() );
            if( DemoLibrary.getNcgAgent().checkLicenseValid(path) == LicenseValidation.ValidLicense ) {
                // 서버에서 유효한 라이선스를 받음
            } else {
                // 서버에서 라이선스를 받지 못함
            }
        } else if( validation == LicenseValidation.ExternalDeviceDisallowed) {
            // HDMI 장치같은 External Device가 연결되어서 재생을 허용하지 않을 경우
            ...
        } else if( validation == LicenseValidation.RootedDeviceDisallowed) {
            // Rooting 단말에서 허용되지 않는 라이선스인 경우.
            ...
        } else if(validation == LicenseValidation.ExpiredLicense){
            // 라이선스가 기간이 만료된 경우
            ...
        } else if(validation == LicenseValidation.ScreenRecorderDetected ){
            // 스크린레코더 앱이 감지된 경우 앱 이름과 패키지 이름을
            // 아래 코드처럼 확인할 수 있다
            HashMap<String,String> data = validation.getExtraData();
            String appName = data.get("AppName");
            String packageName = data.get("AppPackageName");
            ...
        }
```

> **NOTE** :
> 사용자가 고의적으로 디바이스 시간을 바꾸는 경우를 검출하기 위해, 무제한 라이선스를 제외한 나머지 종류의 라이선스 타입에서는 내부적으로 오프라인 상태로 실행된 횟수가  `setCountOfExecutionLimit` 메소드에 의해 설정된 값 이상이 되면 `OfflineStatusTooLong` 에러가 발생합니다.
> `OfflineStatusTooLong` 에러는 `init()` 메소드 호출 시 오프라인 지원(OfflineSupport 파라미터)옵션이 설정된 경우에만 동작합니다.

### 라이선스 발급 및 갱신

라이선스가 없거나 기간이 만료되었을 경우에 해당 콘텐츠에 대한 라이선스를 발급받아야 합니다. 라이선스를 발급받는 시점은 실제 사용 전 아무 때나 가능하지만 SDK Sample App Project에서는 재생 시점에 라이선스 발급을 시도합니다. 라이선스 발급 시점은 개발하는 App 시나리오에 따라 달라지겠지만, 일반적으로 콘텐츠 재생 시점에 라이선스를 요청합니다. 라이선스 발급을 위해 PallyCon NCG Android SDK에서 지원하는 함수는 다음과 같습니다.

```java
interface Ncg2Agent{
    // 입력된 경로와 UserID, OrderID로 라이선스를 획득한다.
    public void acquireLicenseByPath(String path, String userId, String orderId) throws Ncg2Exception;

    // 입력된 경로와 UserID, OrderID로 라이선스를 획득하며, temporary를 설정하여 일회용 라이선스 획득이 가능하다.
    public abstract void acquireLicenseByPath( String path, String userID, String orderID, boolean temporary ) throws Ncg2Exception;

    // Content ID를 이용하여 라이선스를 획득한다.
    public void acquireLicenseByCID(String cid, String userID, String orderID, String acquisitionURL) throws Ncg2Exception;

    // Content ID를 이용하여 라이선스를 획득한다.
    public void acquireLicenseByCID(String cid, String userID, String siteID, String orderID, String acquisitionURL) throws Ncg2Exception;

    // Content ID를 이용하여 라이선스를 획득하며, temporary를 설정하여 일회용 라이선스 획득이 가능하다.
    public abstract void acquireLicenseByCID(String cid, String userID, String siteID, String orderID, String acquisitionURL, boolean temporary ) throws Ncg2Exception;
}
```

> **NOTE** :
> 원격지 콘텐츠(PD/HLS)의 경우 네트워크 환경이 불안정한 경우 라이선스 발급 및 콘텐츠 재생이 원활하지 않을 수 있습니다.
> 다운로드된 콘텐츠의 경우, 라이선스 발급이 완료된 후에는 오프라인 환경에서 재생할 수 있습니다. 따라서 오프라인 지원을 위해서는 재생 시점이 아닌 콘텐츠 다운로드가 완료되는 시점(네트워크가 연결되어 있는)에 라이선스를 발급해야 합니다.

### 라이선스 삭제

APP 시나리오에 따라 이미 발급된 라이선스를 삭제해야 할 수도 있습니다. 일회용 라이선스를 폐기하는 시점은 실제 사용 후 아무때나 가능하지만, SDK Sample App Project에서는 재생 종료 시점에 라이선스 삭제를 시도합니다. PallyCon NCG Android SDK는 다음과 같은 라이선스 삭제 관련 함수를 제공합니다.

```java
interface Ncg2Agent{
    // 발급받은 라이선스의 모든 CID를 통해 라이선스를 삭제한다.
    public void removeLicenseAllCID() throws Ncg2Exception;

    // 발급받은 모든 일회용 라이선스를 삭제한다.
    public abstract void removeAllTemporaryLicense() throws Ncg2Exception;

    // CID를 통해 라이선스를 삭제한다.
    public void removeLicenseByCID(strContentsID) throws Ncg2Exception;

    // 파일 경로에 있는 콘텐츠의 라이선스를 삭제한다.
    public void removeLicenseByPath(strFilename) throws Ncg2Exception;
}
```

## 콘텐츠 재생

PallyCon NCG Android SDK를 이용하여 라이선스까지 정상적으로 발급을 받았다면, `Ncg2LocalWebServer` 인터페이스를 이용하여 내부에 있는 로컬 웹 서버를 제어할 수 있습니다. DRM 콘텐츠를 재생할 수 있도록 PallyCon 콘텐츠의 경로를 지정하면 로컬 웹서버의 가상 URL이 반환되며, 이후 Android에서 제공하는 MediaPlayer 혹은 써드파티 플레이어로 해당 URL을 재생할 수 있습니다. 아래는 `Ncg2LocalWebServer` 에서 제공하는 가상 URL 반환 함수입니다.

```java
interface Ncg2LocalWebServer{
    // Local File의 경우
    public String addLocalFilePathForPlayback(Activity activity, String url, long fileSize) throws Ncg2InvalidLicenseException, Ncg2Exception;

    // PD 콘텐츠의 경우
    public String addProgressiveDownloadUrlForPlayback(Activity activity, String url) throws Ncg2InvalidLicenseException, Ncg2Exception;

    // HLS의 경우
    public String addHttpLiveStreamUrlForPlayback(Activity activity, String url) throws Ncg2InvalidLicenseException, Ncg2Exception;

    // HLS (live)의 경우
    public String addHttpLiveStreamUrlForPlayback(Activity activity, String url, boolean isLiveHLS) throws Ncg2InvalidLicenseException, Ncg2Exception;

    // HLS의 경우
    // addHttpLiveStreamUrlForPlayback()와는 다르게 m3u8 경로에 해당되는 key파일을 미리 체크하지 않는다. 
    // 라이선스를 미리 체크하지 않으므로 HLS 키파일의 라이선스는 반드시 유효해야 한다. 그렇지 않다면 재생오류가 발생될 것이다.
    public String addHttpLiveStreamUrlForPlaybackWithoutChecking(Activity activity, String url) throws Ncg2InvalidLicenseException, Ncg2Exception;

    // HLS의 경우
    // CID와 SiteID를 직접 넣어서 라이선스는 검증하되 m3u8 파일와 key 파일에 접근하지 않도록 처리한다.
    public String addHttpLiveStreamUrlForPlayback(Activity activity, String url, String cid, String siteID) throws Ncg2InvalidLicenseException, Ncg2Exception;

    // HLS (live)의 경우
    // CID와 SiteID, Live 유무를 직접 넣어서 라이선스는 검증하되 m3u8 파일와 key 파일에 접근하지 않도록 처리한다.
    public String addHttpLiveStreamUrlForPlayback(Activity activity, String url, String cid, String siteID, boolean isLiveHLS) throws Ncg2InvalidLicenseException, Ncg2Exception;
}
```

아래 코드를 통해 PallyCon 콘텐츠로부터 가상 URL를 어떻게 얻을 수 있는지 확인할 수 있습니다.

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
            Toast.makeText(MediaPlayerActivity.this, "[onCreate] Error Occured. : " + e.getMessage(), Toast.LENGTH_LONG).show();
            return;
        }
    }
}
```

> **NOTE :**
> Android 3.0 이상부터 Android Media Player에서 HLS를 지원하지만 기기에 따라 지원되지 않는 경우도 있습니다.

아래 코드를 통해 어떻게 가상 URL를 복호화하고 이용할 수 있는지 확인할 수 있습니다.

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

### 로컬 웹서버 콜백

`Ncg2LocalWebServer`는 내부적으로 프록시형태의 웹서버를 이용하게 되는데, 이 웹서버에서 SDK 외부로 이벤트를 알리기 위해 `WebServerListener` 인터페이스를 이용합니다. 아래 코드에서와 같이 `Ncg2LocalWebServer`의 통지나 오류에 대하여 처리할 수 있습니다.

> **NOTE** :
> `onCheckPlayerStatus()` 메소드는 적절한 플레이어의 상태를 확인하여 반환해줘야 합니다.

```java
private Ncg2LocalWebServer.WebServerListener mWebServerCallback = new Ncg2LocalWebServer.WebServerListener(){

    @Override
    public void onNotification(int notificationCode) {
        //통지코드는 대부분 무시되어야 된다.
    }

    @Override
    public void onError(int errorCode, String errorMessage) {
        //적절한 사용자 오류 메시지를 보여줘야 한다.
    }

    @Override
    public PlayerState onCheckPlayerStatus(String uri) {
        if (mIsPrepared) {
            //플레이어가 재생이 가능한 일때만 PlayerState.ReadyToPlay를 반환해야 한다.
            return PlayerState.ReadyToPlay;
        } else {
            // PlayerState.Fail이 반환된 경우에는 재생이 차단되어 진다.
            return PlayerState.Fail;
        }
    }
};
```

> **NOTE** :
> 해커가 가상 URL로 접속하여 데이터를 다운로드 받는 경우를 막기 위해 Player의 상태를 체크하며, 가상 URL로 접속은 했지만 실제로 재생 중이 아닐때는 복호화를 차단합니다.

### 파일 복호화

PallyCon NCG Android SDK에서는 NCG 파일을 복호화해 원본을 추출하는 기능도 제공합니다. `Ncg2Agent`에서 `NcgFile` 객체를 반환 받은 후 `open->read` 순으로 진행합니다. 아래는 `NcgFile`에 대한 코드입니다.

```java
interface Ncg2Agent{
    //NcgFile객체를 반환한다.
    public NcgFile createNcgFile();

    interface NcgFile{
        //NCG 파일을 Open한다.
        public void open(String path) throws Ncg2InvalidLicenseException, Ncg2InvalidNcgFileException, Ncg2Exception;
        //라이선스를 셋팅 후 NCG파일을 Open한다.
        public void open(String path, boolean prepare) throws Ncg2InvalidLicenseException, Ncg2InvalidNcgFileException, Ncg2Exception;
        //open 이후 상태에서 해당 메소드를 호출하여 복호화가 가능한 상태로 만든다. 
        public void prepare() throws Ncg2InvalidLicenseException, Ncg2InvalidNcgFileException, Ncg2Exception;
        //Open 된 파일을 닫아준다.
        public void close();
        //NCG 파일로부터 콘텐츠데이터를 읽어들인다.
        public long read(byte[] buff, long sizeToRead) throws Ncg2Exception;
        //읽어들일 파일포인터의 위치를 이동시킨다.
        public void seek(long offset, SeekMethod seekMethod) throws Ncg2Exception;
        //현재의 파일포인터 위치를 반환한다.
        public long getCurrentFilePointer() throws Ncg2Exception;
        //NCG 파일의 헤더 크기를 반환한다.
        public int getHeaderSize() throws Ncg2Exception;
        //NCG 파일용 InputStream을 반환한다.
        public InputStream getInputStream() throws Ncg2Exception;
    }
}
```

아래는 `NcgFile` 객체를 이용하여 원본 콘텐츠를 만드는 샘플 코드입니다.

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
        }else {
            Log.d(DemoLibrary.TAG, "Decryption failed");
            Toast.makeText(mainActivity, "[unpackNcgFiles] decryption failed", Toast.LENGTH_LONG).show();
        }
    } catch (Ncg2Exception e) {
}
```

> **NOTE** :
> 복호화된 데이터를 파일로 저장하면 원본 파일 노출의 위험이 있으므로, 필요 시 메모리 상에서만 복호화하여 사용하는 것을 권고합니다.

## 릴리즈

`Ncg2Agent` 객체의 사용이 모두 끝나면 `release()` 메소드를 호출하여 릴리즈를 시켜야 합니다. `release()` 메소드는 앱이 종료되는 시점(명시적으로 사용자가 종료한 경우)에 호출합니다. Sample App에서는 `OnBackPressed()` 메소드에서 다이얼로그를 생성하여 `release()` 메소드를 호출합니다.

```java
    case DIALOG_EXIT :
            builder.setMessage( getString( R.string.confirm_end ));
            builder.setPositiveButton( getString( R.string.yes ),	new DialogInterface.OnClickListener() {
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
> Application의 `onTerminate` 메소드에서 `Ncg2Agent.release` 메소드를 호출하는 경우도 있으나 `onTerminate` 메소드는 명시적으로 호출되지 않는 경우가 많기 때문에 `onTerminate`에서 `Ncg2Agent.release` 메소드를 호출하는 것은 권장하지 않습니다.

## 오류 처리

PallyCon NCG Android SDK의 오류처리 메커니즘은 예외(Exception)을 통해 구현됩니다.

기본 class의 Exception인 `Ncg2Exception` 클래스가 있으며, 해당 클래스를 상속받고 있는 별도의 여러 예외 클래스들이 존재합니다. `Ncg2Exception` 클래스는 PallyCon NCG SDK의 기본 예외 클래스로서 내부적으로 `ErrorCode` 및 `Error Message`를 멤버로 포함하고 있습니다.
에러코드가 설정된 경우 `getErrorCode()` 메소드를 통해 에러코드를 확인할 수 있으며, 에러코드가 설정되지 않은 경우에는 `-1`이 반환됩니다. `Error Message`는 `getMessage()` 메소드를 통해 확인할 수 있습니다. 아래는 `Ncg2Player`의 `setDataSource()` 메소드를 이용하는 경우에 `Ncg2Exception` 예외를 catch 하는 예제 코드입니다.

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
