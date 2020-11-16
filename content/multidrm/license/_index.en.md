---
# Course title, summary, and position.
linktitle: 3. License Issuance
weight: 3

# Page metadata.
title: DRM License Issuance
date: "2018-09-09T00:00:00Z"
lastmod: "2018-09-09T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: false  # Show table of contents? true/false
type: book  # Do not modify.

# Add menu entry to sidebar.
# - name: Declare this menu item as a parent with ID `name`.
# - weight: Position of link in menu.
menu:
  multidrm:
    parent: Multi-DRM
    name: License Issuance
    weight: 3
---

To play the packaged content on a client device, you must acquire a DRM license from that device. Licensing can be done in two ways: callback and token. Please refer to the table below for the differences and advantages and disadvantages of the two methods.

| Integration type | Description | Pros | Cons |
| --- | --- | --- | --- |
|**Callback**|The major implementation is between PallyCon license server and your back-end system.|Smaller implementation work on the client side. Better control of license issuance and security|License acquisition may take longer than token model due to the additional communication via callback.|
|**Token**|The major implementation is between your back-end system and client apps.|Better performance on license issuance because the process is simpler than callback type.|More implementation work needed in client app. You need to manage the generated token securely on your own.|

> The callback type integration method is not recommended because delays or errors may occur depending on the situation of the customer's system. Please use the token method instead, and contact us if you want the callback method for a specific reason.

The below documents describe how to issue DRM licenses.

<div class="cards">
<article class="card">
    <div class="text">
        <h3>License Token Guide</h3>
        In the license token scenario, the service site generates a license token of a predefined specification and delivers it to the client, and the client acquires DRM license using the token. This document describes how to issue a token-based license.<p>
        <a href="{{<ref "license-token.en.md">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>License Token Tutorial</h3>
        This tutorial explains how to create a license token by the above specification. This document has step-by-step guide with sample code.<p>
        <a href="{{<ref "license-token-tutorial.en.md">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>Multi-DRM License Integration</h3>
        Customers have an option to integrate PallyCon multi-DRM with native DRM client on their own, without using PallyCon client SDKs. This document explains how to integrate multi-DRM license issuance without the SDKs.<p>
        <a href="{{<ref "multidrm-license.en.md">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>FairPlay Cert Registration Tutorial</h3>
        This document describes the certificate registration process required to integrate FairPlay DRM.<p>
        <a href="{{<ref "fps-cert-tutorial.en.md">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>License Error Code</h3>
        This document describes various error codes that may occur during the DRM license issuance process.<p>
        <a href="{{<ref "license-errorcode.en.md">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
</div>

***
