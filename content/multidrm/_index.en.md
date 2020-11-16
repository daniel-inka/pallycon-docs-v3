---
# Course title, summary, and position.
linktitle: Multi-DRM Guide
weight: 1

# Page metadata.
title: PallyCon Multi-DRM Guide
summary: PallyCon Multi DRM service is a cloud-based SaaS (Solution as a Service) which consists of packaging solutions that apply DRM to contents, DRM license issuance service, and client solutions for DRM contents playback.
date: "2018-09-09T00:00:00Z"
lastmod: "2020-09-16T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: false  # Show table of contents? true/false
type: book  # Do not modify.

# Add menu entry to sidebar.
# - name: Declare this menu item as a parent with ID `name`.
# - weight: Position of link in menu.
menu:
  multidrm:
    name: Multi-DRM Guide
    weight: 1
---

PallyCon Multi DRM service is a cloud-based SaaS (Solution as a Service) which consists of packaging solutions that apply DRM to contents, DRM license issuance service, and client solutions for DRM contents playback.

![howitworks](/docs/images/pallycon-howitworks.png)

You can integrate your content service with PallyCon Multi-DRM service by referring to the following guide documents.

<div class = "cards">
<article class="card">
    <div class="text">
        <h3>DRM Concepts and Terms</h3>
        This document describes various concepts and terminology related to PallyCon Multi DRM service.<p>
        <a href="{{<ref "drm-concepts.en.md">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>Content Packaging</h3>
        In order to apply PallyCon Multi-DRM service, you must first perform packaging processes to encrypt your contents. This document describes a variety of content packaging methods, including PallyCon Packager, AWS Elemental Media Services, Wowza Streaming Engine, and PallyCon Packaging Services.<p>
        <a href="{{<ref "./packaging/">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>License Issuance Integration</h3>
        To play the packaged content on a client device, you must acquire a DRM license from that device. This document describes how to issue DRM licenses, such as callback and token methods.<p>
        <a href="{{<ref "./license/">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>Client Integration</h3>
        PallyCon Multi DRM service provides integration APIs and SDKs to support various client environments such as PC, mobile, OTT platform. This document explains how to use the APIs and SDKs for each client type.<p>
        <a href="{{<ref "./clients/">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>Release Notes</h3>
        This document shows release notes for PallyCon Multi-DRM products.<p>
        <a href="{{<ref "release-notes.en.md">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
</div>
