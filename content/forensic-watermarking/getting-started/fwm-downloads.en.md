---
title: Sample Downloads
linktitle: Sample Downloads
toc: true
type: book
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  multidrm:
    parent: Getting Started
    weight: 4

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 4
---

You can download sample codes to integrate PallyCon Forensic Watermarking service with your service site.

## Forensic Watermarking API request sample

- Common sample codes of API request for [Watermark Packaging API]({{ %ref "packaging-api.en.md"%}}), [Akamai CDN Session API]({{ %ref "akamai-session-manager.en.md"%}}), and [Amazon CloudFront CDN Session API]({{ %ref "cloudfront-session-manager.en.md"%}}).
- For the request JSON specifications, please check the linked API guides above.

|Sample Type |GitHub Link |
|---|---|
|Python| <https://github.com/inka-pallycon/pallycon-wm-api-request-sample-python> |
|Java| <https://github.com/inka-pallycon/pallycon-wm-api-request-sample-java> |

## Forensic watermarking CDN integration sample

- PallyCon Forensic Watermarking service requires CDN integration for real time watermark mixing.

- You can download the Lambda@Edge sample source for the Amazon CloudFront CDN integration from the link below.

  {{% button href="/docs/files/pallycon-watermark-lambda-v2.2.0.zip" icon="fas fa-download" %}}Download CloudFront Lambda sample{{% /button %}}

> In case of Akamai CDN, you don't need this kind of integration module. If you need to use a CDN other than CloudFront and Akamai, please contact us.

<a href="https://www.pallycon.com/contact/" target="_blank" class="btn btn-default">Contact Us</a>
