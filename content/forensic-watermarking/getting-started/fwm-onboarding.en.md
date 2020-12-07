---
title: Forensic Watermarking Onboarding Guide
linktitle: Onboarding Guide
summary: We provide onboarding guides for PallyCon Forensic Watermarking service, so that you can easily find guide documents that suit your various situations and requirements.
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
lastmod: "2020-12-03T00:00:00Z"
draft: false
menu:
  watermarking:
    parent: Getting Started
    weight: 10

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 10
---

We provide onboarding guides for PallyCon Forensic Watermarking service, so that you can easily find guide documents that suit your various situations and requirements.

## Step 1 - Preprocessing

```mermaid
graph TD;
  start_onboarding(Start onboarding) --> preprocessing["Step 1 - Preprocessing"]

  preprocessing --> preprocessing_type{"Choose preprocessing type"}
  preprocessing_type -->|SaaS type|saas_packaging(Watermarking<br>packaging service)
  preprocessing_type -->|On-prem type|onprem_preprocessing{Choose encoder<br>integration type}
  onprem_preprocessing -->|External|cli_type[CLI preprocessor<br>integration]
  onprem_preprocessing -->|Internal|library_type[Preprocessing library<br>integration]

  cli_type --> source_encoding(Encode source video<br>into mp4 video)
  source_encoding --> cli_preprocessing(Run CLI preprocessor<br>to create A/B variants)
  cli_preprocessing --> packaging_type{Choose DRM<br>packaging type}

  library_type --> library_integration(Integrate<br>preprocessor library)
  library_integration --> ab_encoding(Encode source video<br>into A/B variant mp4)
  ab_encoding --> packaging_type

  packaging_type -->|PallyCon CLI Packager|cli_packaging(CLI packaging<br>: A/B version DASH/HLS)
  cli_packaging --> cdn_upload(Upload results on<br>CDN storage)

  packaging_type -->|3rd party packager|3rdparty_packaging(DRM packaging<br>: A/B version DASH/HLS)
  3rdparty_packaging -->packaging_rule(Apply rules<br>for CDN integration)
  packaging_rule --> cdn_upload

  saas_packaging --> storage_registration(Register S3 storage)
  storage_registration --> source_upload(Upload encoded mp4<br>on S3 storage)
  source_upload --> packaging_job(Create packaging job)
  packaging_job --> packaging_done(Packaging job complete<br>: A/B version DASH/HLS)
  packaging_done --> cdn_upload

  cdn_upload --> preprocessing_done[End of step 1]

  click preprocessing "../../preprocessing/"
  click saas_packaging "../../preprocessing/packaging-service"
  click cli_type "../../preprocessing/cli-preprocessor"
  click library_type "../../preprocessing/preprocessor-library"
  click packaging_type "../../../multidrm/packaging"
  click cli_packaging "../../../multidrm/packaging/cli-packager"
  click packaging_rule "../fwm-workflow/#option2-c"
```

## Step 2 - Embedding

```mermaid
graph TD;
  watermark_embedding[Step 2 - Watermark embedding] --> cdn_type{Choose CDN integration}
  cdn_type -->|Akamai CDN|akamai_account(Contact Akamai<br>- request watermark integration)
  cdn_type --> |Amazon CloudFront|lambda_download(Download Lambda module)

  akamai_account --> pallycon_account(Contact PallyCon<br>- request Akamai cert key)
  pallycon_account --> akamai_config(Configure watermarking<br> on Akamai CDN)
  akamai_config --> session_manager(Integrate Session<br> Manager API)

  lambda_download --> lambda_config(Configure Lambda module)
  lambda_config --> session_manager
  session_manager --> player_config(Apply session URL to player)
  player_config --> embedding_done[End of Step 2]

  click akamai_config "https://learn.akamai.com/en-us/webhelp/adaptive-media-delivery/adaptive-media-delivery-implementation-guide/GUID-0BA201AE-8CB5-4A0C-AB11-39155F7CD96F.html"
  click lambda_download "../fwm-downloads"
  click lambda_config "../../embedding/cloudfront-embedder"
  click session_manager "../../embedding/session_manager"
```

## Step 3 - Detection test

```mermaid
graph TD;
  detecting[Step 3 - Watermark detection test] --> playback(Playback using session URL<br>Record one of multiple playback sessions)
  playback --> request_detection(Send recorded video to PallyCon<br>- request watermark detection)
  request_detection --> detection_done[Confirm detection result<br>End of Step 3]
  detection_done --> onboarding_complete(Finish onboarding)

```
