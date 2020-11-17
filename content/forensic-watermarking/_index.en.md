---
# Course title, summary, and position.
linktitle: Forensic Watermarking
weight: 2

# Page metadata.
title: PallyCon Forensic Watermarking
date: "2018-09-09T00:00:00Z"
lastmod: "2018-09-09T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: false  # Show table of contents? true/false
type: book  # Do not modify.

# Add menu entry to sidebar.
# - name: Declare this menu item as a parent with ID `name`.
# - weight: Position of link in menu.
menu:
  watermarking:
    name: Forensic Watermarking
    weight: 1
---

PallyCon Watermarking is a cloud-based SaaS service that allows you to quickly and easily apply watermarking without building complex server environments.

![watermarking-workflow](/docs/images/pallycon-forensic-watermarking-workflow-en.png)

Please refer to the following guide for watermarking service.

<div class="cards">
<article class="card">
    <div class="text">
        <h3>Watermarking Concepts and Terms</h3>
        This document describes various call API specifications for PallyCon Forensic Watermarking service.<p>
        <a href="{{<ref "watermarking-concepts.en.md">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>Watermarking Integration Workflow</h3>
        This document describes the end-to-end workflows and options of PallyCon Forensic Watermarking integration.<p>
        <a href="{{<ref "watermarking-workflow.en.md">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>Watermark Preprocessing Guide</h3>
        To apply watermarking to your content, you need a preprocessing process that generates two versions of the watermark image from a single video. This document describes how to preprocess in a cloud-based packaging service or with an encoder integration.<p>
        <a href="{{<ref "./preprocessing/">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>Watermark Embedding Guide</h3>
        Two versions (A/B variants) of the video are mixed at the playback time according to the user (session) information and delivered to the client. This document explains how to use the session manager and the watermark embedding module that can run on your CDN edge servers.<p>
        <a href="{{<ref "./embedding/">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>Watermark Detecting Guide</h3>
        If a content service site obtains a leaked version of the watermarked content, it can track the leaker by  detect the watermark.<p>
        <a href="{{<ref "./detecting/">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>Release Notes</h3>
        This document shows release notes for PallyCon Watermarking products.<p>
        <a href="{{<ref "watermarking-release-notes.en.md">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
</div>

***