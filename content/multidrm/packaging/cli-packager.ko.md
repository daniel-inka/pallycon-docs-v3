---
# Course title, summary, and position.
linktitle: CLI 패키져 가이드
weight: 20

# Page metadata.
title: CLI 패키져 가이드
summary: 본 문서는 PallyCon 멀티 DRM 클라우드 서비스에 사용되는 CLI(Command Line Interface) 기반 콘텐츠 패키징 툴의 기본 개념과 사용 방법을 설명합니다.
date: "2018-09-09T00:00:00Z"
lastmod: "2020-10-21T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: true  # Show table of contents? true/false
type: book  # Do not modify.

# Add menu entry to sidebar.
# - name: Declare this menu item as a parent with ID `name`.
# - weight: Position of link in menu.
menu:
  multidrm:
    weight: 20
    parent: 콘텐츠 패키징
---

## 개요

본 문서는 PallyCon 멀티 DRM 클라우드 서비스에 사용되는 CLI(Command Line Interface) 기반 콘텐츠 패키징 툴의 기본 개념과 사용 방법을 설명합니다.

콘텐츠 패키징은 아래와 같이 다섯 가지 유형이 있습니다.

1. NCG : 잉카엔트웍스의 자체 규격인 NCG(Netsync Content Guard) DRM으로 콘텐츠를 암호화하여 다운로드 또는 Progressive Download용 콘텐츠로 패키징하는 방식.
2. CMAF : Common Media Application Format 방식 출력으로 Widevine, PlayReady, FPS DRM 적용.
 - IE/엣지 브라우저의 CBC 모드 미지원으로 인해 PlayReady DRM 사용에는 제한 있음
3. DASH : MPEG-DASH CENC 규격으로 스트리밍 콘텐츠를 패키징하여 PlayReady와 Widevine Modular DRM을 적용하는 방식.
4. HLS : HLS-AES 스트리밍 콘텐츠를 패키징하여 FPS(FairPlay Streaming) DRM을 적용하는 방식.
5. HLS-NCG : HLS-AES 규격의 clear key 콘텐츠를 NCG DRM을 통해 보호하는 방식.

> 본 문서는 최신 버전의 CLI 패키져를 기준으로 작성되었습니다. 아래 버튼을 클릭해 최신 버전 CLI 패키져를 다운로드할 수 있습니다.

{{% button href="/docs/files/PallyCon-Packager-Cloud-v3.6.3.zip" icon="fas fa-download" %}}PallyCon CLI 패키져 다운로드{{% /button %}}

## 연동 구조 및 설치 환경

{{< figure library="true" numbered="true" src="cli-packager-flow1.png" title="패키징 플로우" >}}

PallyCon CLI 패키져는 PallyCon 멀티 DRM 클라우드 서버와 연동되어 동작 합니다. PallyCon 클라우드 서버는 서비스 사이트 별 콘텐츠 키 정보를 관리하며, 클라이언트에서 DRM 라이선스 정보 요청 시 CID에 연결된 키(CEK) 정보를 찾아서 라이선스를 발급합니다. 

- 지원 OS: 64비트 기반의 Windows, Linux(Ubuntu, CentOS) 지원
- 리눅스 환경의 경우 gcc와 g++ 라이브러리 6.0 버전 이상이 설치되어 있어야 합니다.
- 패키징 하는 디스크에 콘텐츠 크기의 최소 2배 이상의 여유 공간이 있어야 정상적으로 패키징이 진행됩니다.
- 입출력 파일 명으로는 영문, 숫자 조합만 허용됩니다.

PallyCon CLI 패키져는 구글의 Shaka Packager를 기반으로 개발되었습니다. Shaka Packager에 대한 상세 설명과 문서, 소스 코드 등은 해당 [Github 페이지](https://github.com/google/shaka-packager)를 참고하시기 바랍니다.

## 패키저 실행 인자

> 아래 옵션에 Shaka Packager의 명령어 옵션들을 추가로 사용할 수 있습니다.

### 실행 명령 형식

```
./PallyConPackager --site_id value --access_key value --content_id value -i value -o value (--dash || --hls || --ncg || --hls_ncg)
```

### 명령어 옵션 목록

| <div style="width:150px">Name</div> |<div style="width:60px">Type</div> |<div style="width:60px">Required</div> | Description |
| :--- | :---- | :--- | :--------------|
| `--site_id` | string | Y | PallyCon 서비스 사이트 ID (4바이트) |
| `--access_key` | string | Y | 서비스 사이트에 발급되는 인증 키 <br>PallyCon 콘솔 사이트에서 확인 |
| `--content_id` | string | Y | 패키징 대상 콘텐츠의 고유 ID. 고객사 CMS에서 관리하는 ID 값을 입력하며, 이후 클라이언트 연동 단계에서 동일한 CID를 사용해야 함. (최대 200 바이트) |
| `--cmaf` | bool | Y | CMAF(Widevine, PlayReady, FPS) 패키징 수행 |
| `--dash` | bool | Y | DASH-CENC(Widevine, PlayReady) 패키징 수행 |
| `--hls` | bool | Y | HLS-AES (FPS) 패키징 수행 |
| `--ncg` | bool | Y | NCG 패키징 수행 |
| `--hls_ncg` | bool | Y | HLS-AES (NCG) 패키징 수행, NCG DRM을 이용하여 키를 보호하는 HLS-AES 콘텐츠 생성 |
| `-i (--input_file)` | string | Y | 원본 파일명, 파일을 2개 이상 입력 시 Adaptive Streaming으로 패키징 <br> [추가 옵션] name : 트랙명 설정 (:name=<값>) lang : 오디오 트랙 언어 설정 (:lang=<값>) video_bitrate : 비디오 트랙 bandwidth 설정 (:video_bitrate:<값>)  |
| `-o (--output_dir)` | string | N | Output 폴더명<br>기본 값: 현재 위치에 output 디렉터리 생성 (폴더 및 파일 덮어쓰기를 허용 할 경우 -f 명령어 추가) |
| `--config_file` | string | N | Site ID, Access key와 같이 고정된 값들을 저장한 파일 주소. Json, XML 규격을 지원하며 기본은 Json (파일 확장자가 xml일 경우 xml 파싱 적용) |
| `--clear_lead` | string | N | 암호화 비활성화 구간, 기본 값: 0 (초 단위) |
| `--skip_audio_encryption` | bool | N | 오디오 트랙 암호화 여부. 기본 값: false(오디오트랙 암호화) |
| `--multi_key` | bool | N | 트랙 별로 각각 다른 키로 암호화 (멀티키 패키징)  |
| `--max_sd_height` | number | N | SD 트랙으로 패키징될 최대 해상도 설정. 기본값: 480 |
| `--max_hd_height` | number | N | HD 트랙으로 패키징될 최대 해상도 설정. 기본값: 1080 |
| `--max_uhd1_height` | number | N | UHD 트랙으로 패키징될 최대 해상도 설정. 기본값: 2160 |
| `--fragment_duration` | string | N | Fragment 간격 (초 단위) |
| `--segment_duration` | string | N | Segment 간격 (초 단위) |
| `--on_demand` | bool | N | DASH 패키징 Profile을 on-demand로 설정<br>미입력 또는 'N'으로 설정 시 live profile로 패키징 |
| `--output_single_file` | bool | N | HLS 패키징 출력을 fMP4 파일 형태로 출력 |
| `--mpd_filename` | string | N | DASH manifest (.mpd) 파일명 |
| `--m3u8_filename` | string | N | HLS master manifest (.m3u8) 파일명 |
| `--subtitle` | string | N | 자막 파일명 <br> [추가 옵션] name : 트랙명 설정 (:name=<값>) lang : 자막 언어 설정 (:lang=<값>) |
| `--generate_tracktype_manifests` | bool | N | 트랙별 매니페스트(플레이리스트) 파일 생성. SD부터 UHD 트랙까지 포함된 어댑티브 스트림의 경우, 'SD_ONLY', 'SD_HD', 'SD_UHD' 세 가지 매니페스트가 생성됨. |
| `--enable_average_bandwidth_mpd` | bool | N | MPD 파일 내 각 트랙 별 대역폭을 최대 값 대신 평균 값으로 적용 (기본: false) |
| `--stop_indicator` | bool | N | 패키징 진행 상태 표시기 숨김 |
| `--quiet` | bool | N | 패키징 로그 숨김 |

### 외부 키 사용 시 필요한 옵션

PallyCon 키 서버에서 생성한 암호 키를 사용하지 않고, 서비스 사이트에서 별도로 관리하는 키를 이용해 패키징하는 경우 사용하는 옵션입니다.

> 외부 키 사용 시에는 `--site_id`와 `--access_key` 옵션이 사용되지 않습니다.

| <div style="width:150px">Name</div> | <div style="width:60px">Type</div> | <div style="width:60px">Required</div> | Description |
| :--- | :---- | :--- | :--------------|
| `--enable_raw_key_encryption` | bool | Y | External key 사용 여부 |
| `--provider` | string | N | Widevine PSSH 생성을 위한 DRM provider 문자열<br>기본 값: inkaentworks |
| `--license_url` | string | N | 라이선스 발급 URL <br>기본 값: https://license.pallycon.com/ri/licenseManager.do |
| `--keys` | string | Y | 암호화 key 와 key ID 쌍 (HEX) |
| `--ncg_cek` | string | Y (NCG DRM 패키징 시) | 32바이트 NCG 암호화 키 (HEX) |
| `--iv` | string | N | 16바이트 초기화 벡터 (HEX) |
| `--pssh` | string | Y | PlayReady, Widevine PSSH 데이터 수동 입력 |

## 에러 코드

### 결과 포맷

```xml
<?xml version="1.0" encoding="UTF-8"?> 
<PallyconPackager>
    <RESULT>0</RESULT>
</PallyconPackager>
```

> RESULT: 성공이면 '0', 에러가 발생한 경우는 '0'이 아닌 에러 코드 값

### 에러 코드 설명

| Error Code | Description |
|:------- | :------------- |
| 0 | 성공 |
| 1101 | 실행 명령어 뒤에 인자 값들이 하나도 전달되지 않았습니다. |
| 1102 | 잘못된 인자 값이 입력되었습니다. (INFO 참조 바랍니다.) |
| 1103 | 실행 명령어 다음의 인자 값의 개수가 맞지 않습니다. |
| 1201 | 파일이 지정된 경로에 위치하지 않습니다. |
| 1202 | 파일에 접근 할 수 없습니다. (권한 / 파일이름 문제) |
| 1203 | 파일/폴더의 경로가 너무 길어서 생성에 실패 하였습니다. |
| 1204 | 파일/폴더의 이름이 너무 길어서 생성에 실패 하였습니다. |
| 1205 | 저장 용량이 부족하여 파일/폴더 생성에 실패하였습니다. |
| 1206 | 파일의 해당 위치로 이동을 실패하였습니다. |
| 1207 | 파일의 크기를 얻어오는데 실패하였습니다. |
| 2001 | 서버로 요청을 보내는 중 오류가 발생하였습니다. |
| 2002 | 서버로부터 오류가 반환되었습니다. (INFO 참조 바랍니다.) |
| 2003 | 잘못된 블록 사이즈입니다. |
| 2004 | 개인 키가 없습니다. |
| 2005 | 잘못된 개인 키 입니다. |

## 예제

> 입력 값 중 Site ID와 Access Key는 PallyCon 서비스 가입 후 콘솔 사이트에서 확인할 수 있습니다.

### HLS-NCG 패키징

NCG DRM으로 clear key를 보호하는 HLS-AES128 스트림 패키징입니다.

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> --hls_ncg -i <input file> -o <output directory>
```

### HLS 패키징

FairPlay Streaming DRM으로 보호되는 HLS 스트림 패키징입니다.

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> --hls -i <input file> -o <output directory>
```

### DASH 패키징

PlayReady, Widevine DRM으로 보호되는 DASH 스트림 패키징입니다.

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> --dash -i <input file> -o <output directory>
```

### CMAF 패키징

PlayReady, Widevine, FPS DRM으로 보호되는 CMAF 방식 스트림 패키징입니다.

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> --cmaf -i <input file> -o <output directory>
```

### NCG 패키징

다운로드 또는 Progressive Download 시나리오에 사용되는 NCG DRM으로 암호화된 MP4 파일 패키징입니다.

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> --ncg -i <input file> -o <output directory>
```

### Adaptive-Streaming 패키징

DASH 또는 HLS 패키징 시, 여러 해상도의 콘텐츠를 입력해 Adaptvie Streaming 형태로 패키징하는 방법입니다.

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> --dash --hls ​-i <input file1> <input file2> <input file3> -o <output directory>
```

### 외부 키 입력 (NCG, HLS-NCG)

PallyCon 키 서버에서 생성하는 암호 키 대신, 서비스 사이트가 직접 관리하는 키를 이용해 NCG DRM으로 패키징하는 방법입니다.

```
./PallyConPackager --site_id <site id> ​--content_id <content id> --enable_raw_key_encryption --ncg_cek <32bytes key> --ncg --hls_ncg -i <input file> -o <output directory>
```

### 외부 키 입력 (DASH, HLS)

PallyCon 키 서버에서 생성하는 암호 키 대신, 서비스 사이트가 직접 관리하는 키를 이용해 멀티 DRM으로 패키징하는 방법입니다.

```
./PallyConPackager ​--content_id <content id> --dash --hls --enable_raw_key_encryption --keys <key pair (e.g. label=:key_id=<16 bytes key id>:key=<16 bytes key>)> -i <input file> -o <output directory>
```

### 설정 파일 사용

입력 파라미터 중에서 고정된 설정 값들을 별도의 설정 파일로 저장해 사용하는 방법입니다.

```
./PallyConPackager ​--config_file <configuration file path> -i <input file> -o <output directory> --content_id <content_id>
```

#### 설정 파일 예제

-  config.txt (Default = Json) 
    ```json 
    {
        "site_id": "your site id",
        "access-key": "your access key"
    }
    ```

- config.xml 
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <config> 
    <site-id>your site id</site-id> 
    <access-key>your access key</access-key>
    </config>
    ```

### 자막 패키징 

> VTT 포맷의 외부 자막만 지원됩니다.

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> -i <input file> -o <output directory> --hls ​--subtitle <subtitle file path1>:lang=en <subtitle file path2>:lang=ko:name=Korean <subtitle file path3>:lang=fr:name=French ... 
```

### 라이브 패키징 (DASH 또는 HLS)

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id>​  -o <output directory> ​--dash(or --hls) -i <input stream (e.g. udp://127.0.0.1:1234)> --preserved_segments_outside_live_window 10 --time_shift_buffer_depth 60
```

> - IIS(Windows), Apache/Nginx(Linux) 등의 웹 서버를 활용하면 실시간 패키징 / 재생 테스트를 할 수 있습니다.
> - 패키저가 지원하는 유일한 라이브 스트림 프로토콜은 UDP 입니다. 지원되지 않는 다른 프로토콜의 스트림을 패키징하고 싶은 경우 다음과 같이 ffmpeg을 이용하여 redirect 할 수 있습니다.
> - `# ffmpeg -i <input stream> -f mpegts -vcodec copy -acodec copy udp://127.0.0.1:1234`
> - 위와 같은 PallyConPackager 이외의 라이브 스트리밍 환경은 각 서비스 사이트마다 직접 구성해야 합니다.

> CLI 패키저에서 지원하는 라이브 스트림 패키징 기능은 간단한 개발 테스트 또는 소규모 서비스에 적합하며, 다수의 라이브 채널을 제공하는 대규모 서비스에는 권장하지 않습니다. 이러한 서비스의 경우에는 AWS Elemental 또는 Wowza Streaming Engine 등 별도의 상용 라이브 스트리밍 솔루션을 이용하시기 바랍니다.

### 멀티 키 패키징

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> --dash -i <input file1> <inout_file2> -o <output directory>​ --multi_key
```

### 멀티 키 패키징 (외부 키 사용 시)

```
./PallyConPackager -o <output directory> --dash ​-i <input file1> <input file2> --content_id <content id> --enable_raw_key_encryption --keys <key pair (e.g. label=SD:key_id=<16 bytes key id>:key=<16 bytes key>,label=HD:key_id=<16 bytes key id>:key=<16 bytes key>,label=AUDIO:key_id=<16 bytes key id>:key=<16 bytes key>)>
```

### 멀티 매니페스트 생성

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> -o <output directory> --dash --multi_key ​-i <input file1> <input file2> ... --generate_tracktype_manifests 
```