---
title: Cara Mengimpor Sertifikat ke Browser Edge Flatpak di Linux
date: 2025-10-30T16:55:01+08:00
tags: 
  - Linux
draft: true
description: "Artikel ini memperkenalkan dua metode untuk mengimpor sertifikat ke browser Edge versi Flatpak di Linux, menyelesaikan masalah yang disebabkan oleh kurangnya opsi manajemen sertifikat di Flatpak Edge."
summary: "Saat menggunakan browser Flatpak Edge, Anda mungkin mengalami masalah fungsi manajemen sertifikat yang hilang. Artikel ini menyediakan dua solusi: 1) Impor sertifikat secara grafis melalui tautan internal edge://certificate-manager/localcerts; 2) Salin file sertifikat ke direktori kepercayaan sistem /etc/pki/ca-trust/source/anchors/ dan jalankan perintah update-ca-trust. Metode kedua sangat cocok ketika metode pertama mengalami kesalahan impor."
---

Saat menggunakan [Watt Toolkit](https://steampp.net/), saya menemukan bahwa versi Flatpak browser Edge tidak memiliki opsi "Manajemen Sertifikat". Anda hanya dapat mengakses "Manajer Sertifikat" melalui tautan internal Edge: `edge://certificate-manager/localcerts`.

Setelah melakukan penelitian, ada dua metode untuk mengimpor sertifikat ke Edge:

1. Gunakan [tautan internal Edge](edge://certificate-manager/localcerts) untuk mengimpor sertifikat melalui metode grafis, yang sama dengan metode standar.
2. Salin sertifikat ke direktori `/etc/pki/ca-trust/source/anchors/`.

> Direktori `/etc/pki/ca-trust/source/anchors/` adalah direktori inti untuk mengelola sertifikat root tepercaya tingkat sistem pada distribusi Linux berbasis Fedora/Red Hat.
>
> Namun, path ini berbeda antar distribusi Linux. Jika Anda menggunakan distribusi Linux lain, Anda perlu menemukan direktori yang sesuai.

## Langkah Mengimpor Sertifikat Menggunakan Metode 2

{{% steps %}}

### Salin File Sertifikat

Salin file sertifikat dalam format PEM atau DER (misalnya, `my-ca.cer`) ke direktori di atas menggunakan perintah berikut:

```bash
sudo cp /path/to/my-ca.cer /etc/pki/ca-trust/source/anchors/
```

> Gantikan path `/path/to/my-ca.cer` dengan lokasi sebenarnya file sertifikat Anda di sini.

### Perbarui Toko Kepercayaan (Trust Store)

Jalankan perintah `update-ca-trust` agar sistem menghasilkan ulang file toko kepercayaan terintegrasi, yang akan digunakan oleh semua aplikasi:

```bash
sudo update-ca-trust
```

{{% /steps %}}

## Informasi Tambahan

Saat menggunakan Metode 1 untuk mengimpor, Anda mungkin mengalami kesalahan impor. Dalam kasus seperti itu, Anda dapat menggunakan Metode 2 sebagai alternatif.