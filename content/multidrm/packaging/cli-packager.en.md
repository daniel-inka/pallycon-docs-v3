---
# Course title, summary, and position.
linktitle: CLI Packager Guide
weight: 2

# Page metadata.
title: CLI Packager Guide
summary: This document describes the basic concepts and how to use the command line interface (CLI) based content packaging tool used for the PallyCon multi-DRM cloud service.
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
    weight: 2
    parent: Content Packaging
---

## Overview

This document describes the basic concepts and how to use the command line interface (CLI) based content packaging tool used for the PallyCon multi-DRM cloud service.

There are five types of content packaging:

1. NCG: Content encryption by Inka Entworks' proprietary NCG (Netsync Content Guard) DRM for downloading or progressive download scenario.
2. CMAF: Applying PlayReady, Widevine, and FairPlay DRM by packaging contents with CMAF(Common Media Application Format) type.
 - There is a limit to use PlayReady DRM with CMAF because IE/Edge does not support CBC mode yet.
3. DASH: A method of applying PlayReady and Widevine Modular DRM by packaging streaming contents with MPEG-DASH CENC standard.
4. HLS: A method of applying FPS (FairPlay Streaming) DRM by packaging HLS-AES streaming contents.
5. HLS-NCG: HLS-AES content packaging with clear key protected by NCG DRM.

> This guide is based on the latest version of CLI packager. You can download the latest version using the download button below.

{{% button href="/docs/files/PallyCon-Packager-Cloud-v3.6.3.zip" icon="fas fa-download" %}}Download PallyCon CLI Packager{{% /button %}}

## Integration architecture and supported environments

![flow1](/docs/images/cli-packager-flow1.png)

PallyCon CLI Packager works in conjunction with PallyCon Multi DRM Cloud Server. PallyCon cloud server manages the content key information for each service site, and when a client requests DRM license information, it finds the key (CEK) information associated with the CID and issues a license.

- Supported OS: Support 64 bit Windows, Linux (Ubuntu, CentOS)
- For Linux environment, gcc and g ++ library version 6.0 or later must be installed.
- Available disk space should be at least twice the size of the contents for packaging.
- Only alphanumeric characters are allowed for input / output filenames.

PallyCon CLI Packager is based on Google's Shaka Packager. Please refer to [Github page](https://github.com/google/shaka-packager) for details, documentation and source code of Shaka Packager.

## Packager execution parameters

> You can use additional Shaka Packager commands in the command format below.

### Command format

```
./PallyConPackager --site_id value --access_key value --content_id value -i value -o value (--dash || --hls || --ncg || --hls_ncg)
```

### Command parameters

| Name | Type | Required | Description |
| :--- | :---- | :--- | :--------------|
| `--site_id` | string | Y | Service site ID (4 bytes) issued by PallyCon |
| `--access_key` | string | Y | Authentication key issued to the service site. <br> Can be checked on PallyCon Console site. |
| `--content_id` | string | Y | Unique ID of the content being packaged. Enter the ID value managed by the customer's CMS, and the same CID must be used in the subsequent client integration step. (Maximum 200 bytes)<br> |
| `--cmaf` | bool | Y | Perform CMAF(Widevine, PlayReady, FPS) packaging |
| `--dash` | bool | Y | Perform DASH-CENC(Widevine, PlayReady) packaging |
| `--hls` | bool | Y | Perform HLS-AES (FPS) packaging |
| `--ncg` | bool | Y | Perform NCG packaging |
| `--hls_ncg` | bool | Y | Perform HLS-AES (NCG) packaging. Generate HLS-AES content to protect keys using NCG DRM. |
| `-i (--input_file)` | string | Y | Source filename. Enable adaptive streaming if two or more files are input. <br> [Additional options] name : track name (:name=value) lang : audio track language (:lang=value) video_bitrate : video track bandwidth (:video_bitrate:value) |
| `-o (--output_dir)` | string | N | Output folder name.<br>Default: Create an output directory at the current location (add the -f command to allow folders and files to be overwritten) |
| `--config_file` | string | N | Config filename with fixed options such as Site ID and access key. Json and XML standards are supported. The default is Json (xml parsing is applied if the file extension is xml) |
| `--clear_lead` | string | N | Encryption disabled section. Default: 0 (second) |
| `--skip_audio_encryption` | bool | N | Disable audio track encryption. Default: false(encrypt audio) |
| `--multi_key` | bool | N | Apply multiple key sets for each track. |
| `--max_sd_height` | number | N | Max resolution to be packaged as SD track. Default: 480 |
| `--max_hd_height` | number | N | Max resolution to be packaged as HD track. Default: 1080 |
| `--max_uhd1_height` | number | N | Max resolution to be packaged as UHD track. Default: 2160 |
| `--fragment_duration` | string | N | Fragment duration (seconds) |
| `--segment_duration` | string | N | Segment duration (seconds) |
| `--on_demand` | bool | N | Apply on-demand profile in DASH packaging.<br>If omitted or 'N', live profile is used. |
| `--output_single_file` | bool | N | Set HLS packaging output as fMP4 file |
| `--mpd_filename` | string | N | Filename of DASH manifest (.mpd) |
| `--m3u8_filename` | string | N | Filename of HLS master manifest (.m3u8) |
| `--subtitle` | string | N | Filename of subtitles |
| `--generate_tracktype_manifests` | bool | N | Create multiple manifest (playlist) files for multi-key packaging. For adaptive streams containing SD to UHD tracks, three manifests are created: 'SD_ONLY', 'SD_HD', and 'SD_UHD'. |
| `--enable_average_bandwidth_mpd` | bool | N | Apply the bandwidth of each track in the MPD file as an average value instead of the maximum value (default: false) |
| `--stop_indicator` | bool | N | Hide packaging status indicator |
| `--quiet` | bool | N | Hide packaging logs |

### Options for using external keys

This option is used when packaging with a key that is managed separately by the service site, without using the encryption key generated by PallyCon key server.

> `--site_id` and `--access_key` options are not used when using external keys.
 
| Name | Type | Required | Description |
| :--- | :---- | :--- | :--------------|
| `--enable_raw_key_encryption` | bool | Y | Enable the use of external key  |
| `--provider` | string | N | DRM Provider name for Widevine PSSH<br>Default: inkaentworks |
| `--license_url` | string | N | License acquisition URL <br>Default: https://license.pallycon.com/ri/licenseManager.do |
| `--keys` | string | Y | Encryption key and key ID pair (HEX) |
| `--ncg_cek` | string | Y (NCG DRM packaging) | 32 bytes key for NCG DRM (HEX) |
| `--iv` | string | N | 16 bytes initial vector (HEX) |
| `--pssh` | string | Y | PlayReady, Widevine PSSH data |

## Error code

### Result format

```xml
<?xml version="1.0" encoding="UTF-8"?> 
<PallyconPackager>
    <RESULT>0</RESULT>
</PallyconPackager>
```

> RESULT: '0' if succeeded. Error code if failed.

### Error codes

| Error Code | Description |
|:------- | :------------- |
| 0 | Succeeded |
| 1101 | None of the argument values  after the run command are passed. |
| 1102 | An invalid parameter value was entered. (Please refer to INFO.) |
| 1103 | The number of the following argument values  is not correct. |
| 1201 | The file is not located in the specified path. |
| 1202 | Can not access file. (Permission / file name problem) |
| 1203 | Creation failed because the path to the file / folder is too long. |
| 1204 | Creation failed because file / folder name is too long. |
| 1205 | File / folder creation failed due to insufficient storage space. |
| 1206 | Moving to that location in the file failed. |
| 1207 | Failed to get file size. |
| 2001 | An error occurred while sending the request to the server. |
| 2002 | An error was returned from the server. (Please refer to INFO.) |
| 2003 | Invalid block size. |
| 2004 | You do not have a private key. |
| 2005 | Invalid private key. |

## Example

> Site ID and Access Key among the input values can be checked on the console site after joining PallyCon service.

### HLS-NCG packaging

HLS-AES128 stream packaging that protects clear keys with NCG DRM.

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> --hls_ncg -i <input file> -o <output directory>
```

### HLS packaging

FairPlay Streaming DRM-protected HLS stream packaging.

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> --hls -i <input file> -o <output directory>
```

### DASH packaging

PlayReady, Widevine DRM-protected DASH stream packaging.

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> --dash -i <input file> -o <output directory>
```

### CMAF packaging

PlayReady, Widevine, FPS DRM-protected CMAF stream packaging.

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> --cmaf -i <input file> -o <output directory>
```

### NCG packaging

NCG DRM-encoded MP4 file packaging used for download or progressive download scenarios.

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> --ncg -i <input file> -o <output directory>
```

### Adaptive-Streaming packaging

For DASH or HLS packaging, you can enter content with multiple resolutions and package it for adaptive streaming.

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> --dash --hls -i <input file1> <input file2> <input file3>  -o <output directory>
```

### Using external key (NCG, HLS-NCG)

This is a method of packaging NCG DRM using the key managed by the service site instead of the encryption key generated by PallyCon key server.

```
./PallyConPackager --site_id <site id> ​--content_id <content id> --enable_raw_key_encryption --ncg_cek <32bytes key> --ncg --hls_ncg -i <input file> -o <output directory>
```

### Using external key (DASH, HLS)

This is a method of multi DRM packaging using the key managed by the service site instead of the encryption key generated by PallyCon key server.

```
./PallyConPackager ​--content_id <content id> --dash --hls --enable_raw_key_encryption --keys <key pair (e.g. label=:key_id=<16 bytes key id>:key=<16 bytes key>)> -i <input file> -o <output directory>
```

### Using configuration file

It is a method to save fixed setting values among input parameters as a separate config file.

```
./PallyConPackager ​--config_file <configuration file path> -i <input file> -o <output directory> --content_id <content_id>
```

#### Example of config file

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

### Packaging subtitles

> Supports external subtitles in VTT format only.

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> -i <input file> -o <output directory> ​--subtitle <subtitle file path1>:lang=en <subtitle file path2>:lang=ko:name=Korean <subtitle file path3>:lang=fr:name=French ...
```

### Live stream packaging (DASH or HLS)

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id>​  -o <output directory> ​--dash(or --hls) -i <input stream (e.g. udp://127.0.0.1:1234)> --preserved_segments_outside_live_window 10 --time_shift_buffer_depth 60
```

> - Live packaging / playback test can be done by using an web server such as IIS (Windows) and Apache / Nginx (Linux).
> - The only live stream protocol supported by Packager is UDP. If you want to package streams of other unsupported protocols, you can redirect using ffmpeg as follows:
> - `# ffmpeg -i <input stream> -f mpegts -vcodec copy -acodec copy udp://127.0.0.1:1234`
> - Live streaming environments other than PallyCon Packager should be configured by each service site itself.

> The live stream packaging function supported by the CLI packager is suitable for simple development tests or small services, and is not recommended for large-scale services that provide a large number of live channels. For these services, please use a commercial live streaming solution such as AWS Elemental or Wowza Streaming Engine.

### Multi-key Packaging

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> --dash -i <input file1> <input file2> -o <output directory>​ --multi_key 
```

### Multi-key Packaging with external keys

```
./PallyConPackager -o <output directory> --dash -i <input file1> <input file2> --content_id <content id> --enable_raw_key_encryption --keys <key pair (e.g. label=SD:key_id=<16 bytes key id>:key=<16 bytes key>,label=HD:key_id=<16 bytes key id>:key=<16 bytes key>,label=AUDIO:key_id=<16 bytes key id>:key=<16 bytes key>)>
```

### Multiple manifests for allowed track types

```
./PallyConPackager --site_id <site id> --access_key <access key> --content_id <content id> -o <output directory> --dash --multi_key -i <input file1> <input file2> ... --generate_tracktype_manifests
```
