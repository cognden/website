---
title: How to Import Certificates to Flatpak Edge Browser on Linux
date: 2025-10-30T16:54:49+08:00
tags: 
  - Linux
draft: false
description: "This article introduces two methods for importing certificates into the Flatpak version of Edge browser on Linux, solving the problem caused by the lack of certificate management options in Flatpak Edge."
summary: "When using the Flatpak version of Edge browser, you may encounter the problem of missing certificate management functions. This article provides two solutions: 1) Import certificates graphically through the internal link edge://certificate-manager/localcerts; 2) Copy the certificate file to the system trust directory /etc/pki/ca-trust/source/anchors/ and execute the update-ca-trust command. The second method is particularly suitable when the first method encounters import errors."
---

When using [Watt Toolkit](https://steampp.net/), I noticed that the Flatpak version of the Edge browser does not have a "Certificate Management" option. You can only access the "Certificate Manager" via Edge's internal link: `edge://certificate-manager/localcerts`.

After research, there are two methods to import certificates into Edge:

1. Use the [Edge internal link](edge://certificate-manager/localcerts) to import certificates through a graphical method, which is the same as the standard method.
2. Copy the certificate to the `/etc/pki/ca-trust/source/anchors/` directory.

> The `/etc/pki/ca-trust/source/anchors/` directory is a core directory for managing system-level trusted root certificates in Fedora/Red Hat-based Linux distributions.
>
> However, this path varies across different Linux distributions. If you are using another Linux distribution, you need to find the corresponding directory.

## Steps for Importing Certificates Using Method 2

{{% steps %}}

### Copy the Certificate File

Copy the certificate file in PEM or DER format (e.g., `my-ca.cer`) to the aforementioned directory using the following command:

```bash
sudo cp /path/to/my-ca.cer /etc/pki/ca-trust/source/anchors/
```

> Replace the path `/path/to/my-ca.cer` with the actual location of your certificate file here.

### Update the Trust Store

Execute the `update-ca-trust` command to make the system regenerate the integrated trust store file for use by all applications:

```bash
sudo update-ca-trust
```

{{% /steps %}}

## Additional Information

When using Method 1 for import, you may encounter an import error. In such cases, you can use Method 2 instead.
