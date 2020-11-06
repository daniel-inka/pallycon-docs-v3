---
title: "FairPlay Cert Registration Tutorial"
date: 2020-02-05T16:12:37+09:00
draft: false
weight: 35
---

## Overview

To use Apple's [FairPlay Streaming (FPS) DRM](https://developer.apple.com/streaming/fps/), a content service provider should acquire `FPS Deployment Package` from Apple and register the below data with PallyCon service.

```s
- FPS Certificate file (.der or .cer)
- Private key file (.pem)
- Private key password string
- Application secret key (ASK) string
```

This document will guide you step by step to ensure that your process is accurate.

## Step 1: Sign up Apple Developer Account and Request Deployment Package

You need Apple developer account to get the `FPS Deployment Package`. Sign up for the account on the below site if you don't have one.

- https://developer.apple.com/support/enrollment/

Click ['Request Deployment Package' link](https://developer.apple.com/contact/fps/) at the bottom of [FairPlay Streaming website](https://developer.apple.com/streaming/fps/) and log in with your Apple developer account.

If you apply for the Deployment Package according to the input form, you will be issued a package containing the `FPS Credential Creation Guide` document after Apple's confirmation.

> During the application process, you will be asked if you have completed the implementation and testing of the `Key Server Module (KSM)`. This can be answered by saying, 'I am using a 3rd party DRM company and the company has already built and tested KSM'.

## Step 2: Create Private Key and Certificate Signing Request(CSR)

Create a private key (privatekey.pem) file and a certificate signing request (certreq.csr) file by referring to the guide document in the package. The following describes OpenSSL method in `Generating a Certiﬁcate Signing Request` section of the guide.

> OpenSSL must be installed on the PC or server environment where this process is performed.

1. Create a private key (privatekey.pem) file
  ![generate-private-key](/docs/images/fps_tutorial_01_PrivateKeyFile(pem).png)
  - Run the following command to generate the private key:
  ```
  openssl genrsa -aes256 -out privatekey.pem 1024
  ```
  - Enter any password for the private key and make a note of it for later use.
  - The password should be shorter than 32 characters and special characters are not allowed.

1. Create a certificate signing request file
  ![create-csr](/docs/images/fps_tutorial_02_CertificateSigningRequestFile(csr).png)
  - Run the following command: The contents of the -subj parameter can be modified to suit your organization.
  ```
  openssl req -new -sha1 -key privatekey.pem -out certreq.csr -subj "/CN=SubjectName/OU=OrganizationalUnit/O=Organization/C=US"
  ```
  - Enter the private key password from the above process.

## Step 3: Create FPS Cert at Apple Developer Portal

1. Log in to the Apple Developer Portal as shown below and go to the `Certificate, IDs & Profiles` menu.
  ![apple-portal-login](/docs/images/fps_tutorial_03_AppleDeveloperPortalLogin.png)

2. Press the `+` button on the menu screen to move to the `Create a New Certificate` screen.
  ![apple-portal-login](/docs/images/fps_tutorial_04_CertificatesList.png)

3. Select the `FairPlay Streaming Certificate` item and click the `Continue` button.
  ![apple-portal-login](/docs/images/fps_tutorial_05_CertTypeSelect.png)

4. Click `Choose File`, select the `certreq.csr` file created in the previous step and click the `Continue` button.
  ![apple-portal-login](/docs/images/fps_tutorial_06_ChooseCsrFile.png)

5. Copy the `Application Secret Key (ASK)` string, record it separately, paste it in the space below and click the `Continue` button.
  ![apple-portal-login](/docs/images/fps_tutorial_07_ASKValueCheck.png)

6. A pop-up will appear to confirm that you have recorded the ASK string separately. Click the `Generate` button.
  ![apple-portal-login](/docs/images/fps_tutorial_08_ASKSavedCheckPopup.png)

7. When the above process is completed, the certificate created with `FairPlay Streaming` type will be displayed in the `Certificate` list.
  ![apple-portal-login](/docs/images/fps_tutorial_09_ChecklistofAddedFPSCert.png)

8. Click the `Download` button to save the `FPS certificate file` (fairplay.cer).
  ![apple-portal-login](/docs/images/fps_tutorial_10_DownloadCert.png)

## Step 4: Register FPS Cert at PallyCon Console Site

1. Log in to [PallyCon Console site](https://console.pallycon.com).

2. Click on the `Site Settings` menu and go to the `Integration Settings` tab.

3. Click the `Register` button in the `FPS Cert Registration` section of `Multi-DRM Settings`.

4. Enter the files (private key, certificate) and strings (private key password, ASK) created in the previous steps in the pop-up window.

> Note: If you have performed Step #2 multiple times for testing, you may encounter a mismatch of certificate / key pairs or invalid private key passwords. Please be careful to register the correct data.

## What's Next

This completes the `FairPlay Cert Registration Tutorial`.

After the `FPS Cert Registration`, create FairPlay DRM-protected HLS content through [Content Packaging](/docs/en/multidrm/packaging) and [Integrate Client Player](/docs/en/multidrm/clients) to check playback.

> The download URL of FPS certification file is `https://license.pallycon.com/ri/fpsKeyManager.do?siteId='Site_ID'`. Input your `Site_ID` which is issued by PallyCon Cloud service. (4byte)

***
