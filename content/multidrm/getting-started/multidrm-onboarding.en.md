---
title: Multi-DRM Onboarding Guide
linktitle: Multi-DRM Onboarding
summary: We provide onboarding guides for PallyCon Multi-DRM service, so that you can easily find guide documents that suit your various situations and requirements.
toc: false
type: book
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  multidrm:
    parent: Getting Started
    weight: 1

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 1
---

We provide onboarding guides for PallyCon Multi-DRM service, so that you can easily find guide documents that suit your various situations and requirements.

```mermaid
graph TD;
    subgraph Content Service
    A[Source Content] --> B(Encoder)
    B -->|Encoded Content| C(PallyCon Packager<br>or<br>On-the-fly Packaging Server)
    C -->|Encrypted<br>DASH/HLS Stream| D(CDN<br>Storage)
    end
    subgraph PallyCon Service
    C -->|Packaging<br>Info| E(PallyCon Multi DRM<br>License Server)
    end
    subgraph End User
    D --> |Encrypted<br>DASH/HLS Stream| F(Client<br>Player)
    E --> |DRM License| F
    end
```
