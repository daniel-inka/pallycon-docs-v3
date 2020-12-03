---
# Course title, summary, and position.
linktitle: 4. Watermark Detecting
summary: Watermark detection analyzes each frame of the video to detect the original watermark pattern and decrypts the data with the secret key used at the time of insertion.
weight: 40

# Page metadata.
title: Watermark Detecting
date: "2018-09-09T00:00:00Z"
lastmod: "2020-10-18T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: false  # Show table of contents? true/false
type: book  # Do not modify.

# Add menu entry to sidebar.
# - name: Declare this menu item as a parent with ID `name`.
# - weight: Position of link in menu.
menu:
  watermarking:
    weight: 40
    parent: Forensic Watermarking
    name: Watermark Detecting
---

If the watermarked video is leaked and distributed illegally, you can request the PallyCon service to detect the suspicious content. Watermark detection analyzes each frame of the video to detect the original watermark pattern and decrypts the data with the secret key used at the time of insertion.

If the watermark payload is found through the detection process, the session database finds the session information whose key value is the payload and reports it as the detection result.

```mermaid
sequenceDiagram
    participant A as Service site
    participant B as PallyCon service
    A ->> B: Suspicious content
    Note right of B: Detect watermark (payload)
    B -->> B: Analyze video frames
    opt Watermark detected
    Note right of B: Session database
    B -->> B: Find session data
    end
    B ->> A: Report detection result
```

Please contact us for detailed information on watermark detection service.

<a href="https://www.pallycon.com/contact/" target="_blank" class="btn btn-primary">Contact Us</a>
