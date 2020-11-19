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

|<div style="width:80px">Integration type</div>| Description | Pros | Cons |
| --- | --- | --- | --- |
|**Callback**|The major implementation is between PallyCon license server and your back-end system.|Smaller implementation work on the client side. Better control of license issuance and security|License acquisition may take longer than token model due to the additional communication via callback.|
|**Token**|The major implementation is between your back-end system and client apps.|Better performance on license issuance because the process is simpler than callback type.|More implementation work needed in client app. You need to manage the generated token securely on your own.|

> The callback type integration method is not recommended because delays or errors may occur depending on the situation of the customer's system. Please use the token method instead, and contact us if you want the callback method for a specific reason.

The below documents describe how to issue DRM licenses.


<div class="row">
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">License Token Guide</h3>
        <p class="card-text">In the license token scenario, the service site generates a license token of a predefined specification and delivers it to the client, and the client acquires DRM license using the token. This document describes how to issue a token-based license.</p>
        <a href="./license-token/" class="btn btn-primary">Read More</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">License Token Tutorial</h3>
        <p class="card-text">This tutorial explains how to create a license token by the above specification. This document has step-by-step guide with sample code.</p>
        <a href="./license-token-tutorial/" class="btn btn-primary">Read More</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">FPS Cert Registration Tutorial</h3>
        <p class="card-text">This document describes the certificate registration process required to integrate FairPlay DRM.</p>
        <a href="./fps-cert-tutorial/" class="btn btn-primary">Read More</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h3 class="card-title">License Error Code</h3>
        <p class="card-text">This document describes various error codes that may occur during the DRM license issuance process.</p>
        <a href="./license-errorcode/" class="btn btn-primary">Read More</a>
      </div>
    </div>
  </div>
</div>
