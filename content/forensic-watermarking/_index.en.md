---
# Course title, summary, and position.
linktitle: Forensic Watermarking
weight: 20

# Page metadata.
title: PallyCon Forensic Watermarking
date: "2018-09-09T00:00:00Z"
lastmod: "2020-09-09T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: false  # Show table of contents? true/false
type: book  # Do not modify.

# Add menu entry to sidebar.
# - name: Declare this menu item as a parent with ID `name`.
# - weight: Position of link in menu.
menu:
  watermarking:
    name: Forensic Watermarking
    weight: 20
---

PallyCon Watermarking is a cloud-based SaaS service that allows you to quickly and easily apply watermarking without building complex server environments.

{{< figure library="true" numbered="false" src="pallycon-forensic-watermarking-workflow-en.png" title="How it works" >}}

Please refer to the following guide for watermarking service.

<div class="row">
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">Getting Started</h3>
        <p class="card-text">Provides basic description and guide for quick understanding and experience of PallyCon Forensic Watermarking service.</p>
        <a href="./getting-started/" class="btn btn-primary">Read More</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">Watermark Preprocessing</h3>
        <p class="card-text">To apply watermarking to your content, you need a preprocessing process that generates two versions of the watermark image from a single video. This document describes how to preprocess in a cloud-based packaging service or with an encoder integration.</p>
        <a href="./preprocessing/" class="btn btn-primary">Read More</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">Watermark Embedding</h3>
        <p class="card-text">Two versions (A/B variants) of the video are mixed at the playback time according to the user (session) information and delivered to the client. This document explains how to use the session manager and the watermark embedding module that can run on your CDN edge servers.</p>
        <a href="./embedding/" class="btn btn-primary">Read More</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">Watermark Detecting</h3>
        <p class="card-text">If a content service site obtains a leaked version of the watermarked content, it can track the leaker by  detect the watermark.</p>
        <a href="./detecting/" class="btn btn-primary">Read More</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">Release Notes</h3>
        <p class="card-text">This document shows release notes for PallyCon Watermarking products.</p>
        <a href="./watermarking-release-notes/" class="btn btn-primary">Read More</a>
      </div>
    </div>
  </div>
</div>
