---
title: Membuat Situs Web dengan Hugo
date: 2025-06-22T10:03:29+08:00
tags:
  - hugo
showToc: true
TocOpen: true
draft: false
description: Artikel ini akan memperkenalkan cara membuat situs web statis menggunakan Hugo dan mengkonfigurasi tema PaperMod.
summary: Artikel ini akan memperkenalkan cara membuat situs web statis menggunakan Hugo dan mengkonfigurasi tema PaperMod.
---

## Instal Hugo dan Buat Situs Web
### Instalasi Hugo

Pertama, pastikan Hugo telah terinstal di komputer Anda. Anda dapat mengunjungi [situs web resmi Hugo](https://gohugo.io/) untuk mengunduh versi yang sesuai dengan sistem operasi Anda. Setelah instalasi selesai, masukkan perintah berikut di terminal untuk memverifikasi apakah instalasi berhasil:

```shell
hugo version
```

### Membuat Proyek Baru

Pertama, kita perlu membuat proyek situs web Hugo baru. Silakan buka terminal (atau alat baris perintah) dan masukkan perintah-perintah berikut secara berurutan:

```shell
hugo new site quickstart
cd quickstart
git init
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

- `hugo new site quickstart`: Membuat proyek Hugo baru bernama `quickstart`.
- `cd quickstart`: Masuk ke direktori proyek.
- `git init`: Menginisialisasi sistem kontrol versi Git untuk memudahkan manajemen kode selanjutnya.
- `git submodule add ...`: Menambahkan tema PaperMod sebagai submodule ke direktori `themes/`.

> **Catatan**: Jika jaringan tidak stabil, unduhan mungkin gagal. Pastikan koneksi jaringan lancar atau coba ubah sumber mirror.

### Menentukan Tema

Selanjutnya, kita tentukan tema yang akan digunakan Hugo untuk merender situs web. Edit file `hugo.toml` di direktori root proyek dan tambahkan konten berikut:

```toml
theme = "PaperMod"
```

### Pratinjau Situs Web

Setelah menyelesaikan langkah-langkah di atas, jalankan perintah berikut untuk memulai server pengembangan lokal:

```shell
hugo server
```

Pada saat ini, Anda dapat mengunjungi `http://localhost:1313/` di browser untuk melihat pratinjau situs web. Tekan `Ctrl + C` untuk menghentikan layanan.

## Konfigurasi Situs Web Hugo
### Konfigurasi Dasar

File konfigurasi inti Hugo adalah `hugo.toml`, yang menentukan perilaku dasar situs web. Kita dapat memodifikasinya sesuai kebutuhan.

{{% details title="Berikut adalah konfigurasi lengkap" closed="true" %}}

```toml  
# Konfigurasi global dasar situs web  
baseURL                = "https://example.org/" # Menetapkan URL root situs web untuk tautan absolut; ubah ke domain aktual saat deployment  
languageCode           = "id"                   # Menentukan kode bahasa konten, digunakan untuk atribut HTML lang dan pengaturan bahasa  
title                  = "ExampleSite"          # Judul utama situs web, biasanya ditampilkan di tab browser dan halaman utama  
theme                  = "PaperMod"             # Nama tema yang digunakan, menentukan tampilan situs web  
  
# Konfigurasi sumber daya ikon situs web  
[assets]  
  # Ikon ditempatkan di direktori static/  
  favicon          = "/favicon.ico"          # Ikon favicon utama, biasanya ditampilkan di tab browser  
  favicon16x16     = "/favicon-16x16.png"    # Ikon favicon 16x16 pixel, cocok untuk perangkat resolusi rendah  
  favicon32x32     = "/favicon-32x32.png"    # Ikon favicon 32x32 pixel, memberikan tampilan definisi lebih tinggi  
  apple_touch_icon = "/apple-touch-icon.png" # Ikon untuk layar beranda perangkat Apple  
  
# Konfigurasi parameter tema, mempengaruhi tampilan dan perilaku fungsional  
[params]  
  # Pengaturan SEO, membantu optimisasi mesin pencari  
  title       = "ExampleSite"             # Menetapkan judul situs web, ditampilkan di tab browser dan hasil pencarian  
  description = "ExampleSite description" # Deskripsi singkat situs web, digunakan untuk ringkasan pencarian  
  keywords    = ["Blog", "Portfolio"]     # Daftar kata kunci terkait untuk meningkatkan relevansi pencarian  
  
  # Konfigurasi perilaku dasar situs web  
  DateFormat   = "2006-01-02" # Menetapkan gaya pemformatan tanggal, digunakan untuk waktu publikasi artikel  
  defaultTheme = "auto"       # Menetapkan mode tema default: "auto" mengikuti sistem, atau bisa diubah ke "light"/"dark"  
  
  # Informasi sambutan halaman utama  
  [params.homeInfoParams]  
    Title   = "Hi there 👋"  
    Content = "Welcome to my blog"  
  
# Konfigurasi menu global, berlaku untuk semua bahasa  
[menu]  
  [[menu.main]] # Mendefinisikan item menu utama  
    identifier = "posts"   # Pengidentifikasi item menu  
    name       = "Tentang"      # Nama item menu  
    url        = "/posts/" # Tautan item menu  
    weight     = 1         # Bobot item menu, digunakan untuk pengurutan  
  [[menu.main]]  
    identifier = "tags"  
    name       = "Tags"  
    url        = "/tags/"  
    weight     = 2  
  [[menu.main]]  
    identifier = "search"  
    name       = "Pencarian"  
    url        = "/search/"  
    weight     = 3  
  
# Pengaturan format output  
[outputs]  
  # Menyediakan dukungan fungsi pencarian  
  home = ["HTML", "RSS", "JSON"]  
```

{{% /details %}}

#### Pengaturan Informasi Dasar Situs Web

```toml
baseURL                = "https://example.org/"
languageCode           = "id"
title                  = "ExampleSite"
theme                  = "PaperMod"
```

Item konfigurasi ini menentukan informasi dasar situs web, seperti judul, bahasa, dan tema. Di antaranya:

- `baseURL` adalah nama domain yang digunakan saat deployment.
- `languageCode` mengontrol lingkungan bahasa situs web.
- `title` ditampilkan pada tab browser.
- `theme` menentukan tema yang digunakan.

#### Pengaturan Ikon

Untuk membuat situs web lebih personal, kita dapat menetapkan ikon untuknya. Tambahkan konten berikut ke `hugo.toml`:

```toml
[assets]
  favicon          = "/favicon.ico"
  favicon16x16     = "/favicon-16x16.png"
  favicon32x32     = "/favicon-32x32.png"
  apple_touch_icon = "/apple-touch-icon.png"
```

Ikon-ikon ini harus ditempatkan di direktori `static/` untuk ditampilkan di berbagai perangkat dan browser.

#### Pengaturan SEO

Optimisasi mesin pencari (SEO) sangat penting untuk meningkatkan visibilitas situs web. Kita dapat menambahkan konfigurasi berikut ke situs web:

```toml
[params]
  title       = "ExampleSite"
  description = "ExampleSite description"
  keywords    = ["Blog", "Portfolio"]
```

- `title`: Judul situs web yang ditampilkan di hasil pencarian mesin pencari.
- `description`: Deskripsi singkat, digunakan untuk tampilan ringkasan.
- `keywords`: Daftar kata kunci, membantu meningkatkan peringkat pencarian.

### Konfigurasi Menu Navigasi

Bilah navigasi merupakan pintu masuk penting bagi pengguna untuk menjelajahi situs web. Kita dapat mendefinisikan item menu dengan cara berikut:

```toml
[menu]
  [[menu.main]]
    identifier = "posts"
    name       = "Tentang"
    url        = "/posts/"
    weight     = 1
  [[menu.main]]
    identifier = "tags"
    name       = "Tags"
    url        = "/tags/"
    weight     = 2
  [[menu.main]]
    identifier = "search"
    name       = "Pencarian"
    url        = "/search/"
    weight     = 3
```

Konfigurasi di atas akan menghasilkan tiga item menu: Posts, Tags, dan Search, yang diurutkan berdasarkan bobot.

- `identifier`: Memberikan item menu nama unik (ID) untuk memudahkan identifikasi dan penggunaan program.
- `name`: Nama yang ditampilkan di halaman web, pengguna akan melihat teks "Posts".
- `url`: URL tujuan saat item menu diklik, dalam hal ini adalah "halaman daftar artikel" di dalam situs web.
- `weight`: Menetapkan urutan tampil. Semakin kecil angka, semakin tinggi prioritasnya.

### Modifikasi Tambahan

Saat ini halaman Posts masih menampilkan 404 karena PaperMod hanya menyediakan halaman tag secara bawaan dan tidak memerlukan pembuatan manual.

Untuk mengatasi ini, kita perlu membuat file `search.md` di direktori `content/` dan menambahkan konfigurasi berikut:

- `search.md`

```yaml
---
title: "Pencarian"
layout: "search"
summary: "search"
placeholder: "Anda bisa memasukkan teks di sini..."
---
```

- `title`: Menetapkan judul halaman.
- `layout`: Menentukan template layout yang digunakan untuk halaman.
- `summary`: Memberikan deskripsi singkat tentang halaman.
- `placeholder`: Mengatur teks placeholder pada kotak pencarian di halaman.

### Pengaturan Format Output

Untuk mendukung fungsi pencarian, kita perlu menentukan format output tambahan untuk halaman utama:

```toml
[outputs]
  home = ["HTML", "RSS", "JSON"]
```

Dengan cara ini, halaman utama tidak hanya akan menghasilkan halaman HTML tetapi juga menyediakan langganan RSS dan antarmuka data JSON.

---

Sampai di sini, konfigurasi situs web telah selesai dan dapat diunggah ke platform seperti [GitHub Pages](https://pages.github.com/), [Cloudflare Pages](https://pages.cloudflare.com/), atau [Netlify](https://www.netlify.com/) untuk deployment. Untuk prosedur detailnya, silakan merujuk pada dokumentasi resmi [Host and deploy | HUGO](https://gohugo.io/hosting-and-deployment/).

## Opsional: Konfigurasi Dukungan Multi-Bahasa
### Mengaktifkan Dukungan Multi-Bahasa

Untuk mengaktifkan fungsi multi-bahasa, pertama-tama kita perlu memodifikasi file konfigurasi global Hugo `hugo.toml` dan mengganti pengaturan menu dan bahasa dasar asli dengan konfigurasi multi-bahasa terstruktur berikut:

```toml
[languages]
  [languages.id] # Bahasa Indonesia language configuration
    languageCode = "id"
    languageName = "Bahasa Indonesia"
    weight       = 1

    [languages.id.menu]
      [[languages.en.menu.main]]
        identifier = "posts"
        name       = "Tentang"
        url        = "/posts/"
        weight     = 1
      [[languages.id.menu.main]]
        identifier = "tags"
        name       = "Tags"
        url        = "/tags/"
        weight     = 2
      [[languages.id.menu.main]]
        identifier = "search"
        name       = "Pencarian"
        url        = "/search/"
        weight     = 3

  [languages.zh] # Chinese language configuration
    languageCode = "zh"
    languageName = "中文"
    weight       = 2

    [languages.zh.menu]
      [[languages.zh.menu.main]]
        identifier = "posts"
        name       = "文章"
        url        = "/posts/"
        weight     = 1
      [[languages.zh.menu.main]]
        identifier = "tags"
        name       = "标签"
        url        = "/tags/"
        weight     = 2
      [[languages.zh.menu.main]]
        identifier = "search"
        name       = "搜索"
        url        = "/search/"
        weight     = 3
```

- `[languages]` adalah blok konfigurasi utama untuk dukungan multi-bahasa Hugo.
- Setiap sub-item (seperti `languages.id` dan `languages.zh`) mewakili satu bahasa.
- `languageCode` digunakan untuk mengidentifikasi jenis bahasa, biasanya menggunakan kode standar ISO (misalnya `id` untuk Bahasa Inggris, `zh` untuk Bahasa Tionghoa).
- `languageName` adalah nama bahasa yang ditampilkan di halaman web.
- `weight` mengontrol urutan prioritas bahasa; semakin kecil nilainya, semakin tinggi prioritasnya.

> **Tips**: Jika membutuhkan bahasa tambahan (seperti Prancis, Jerman, dll.), cukup tambahkan sesuai format di atas.

### Membuat File Halaman Multi-Bahasa

Untuk mendukung multi-bahasa, kita perlu membuat file halaman terpisah untuk setiap bahasa.

Menurut dokumentasi resmi [Multilingual mode | HUGO](https://gohugo.io/content-management/multilingual/), ada dua metode yang dapat digunakan:

1. Pembedaan berdasarkan Nama File
	- `/content/search.id.md`
	- `/content/search.zh.md`
2. Pembedaan berdasarkan Direktori
	- `/content/id/search.md`
	- `/content/zh/search.md`

Pilihan cara pembuatan tergantung pada preferensi individu. Di sini kami memilih bentuk "direktori", sedangkan penggunaan nama file tidak memerlukan konfigurasi—hanya perlu menambahkan kode bahasa yang sesuai ke nama file bahasa yang sesuai. Oleh karena itu, kami akan menekankan pada penjelasan konfigurasi bentuk direktori.

Masuk ke direktori `content/` dan susun struktur seperti ini:

```txt
content/
├── id/
│   └── search.md    # Bahasa Indonesia version search page
└── zh/
    └── search.md    # Chinese version search page
```

Masing-masing edit file halaman bahasa yang sesuai. Contohnya:

- `id/search.md`：

```yaml
---
title: "Pencarian"
layout: "search"
summary: "search"
placeholder: "Anda bisa memasukkan teks di sini..."
---
```

- `zh/search.md`：

```yaml
---
title: "搜索"
layout: "search"
summary: "搜索页面"
placeholder: "这里可以输入..."
---
```

Kemudian lakukan modifikasi akhir pada `hugo.toml` untuk menetapkan bahasa tampilan default dan menentukan direktori untuk setiap bahasa.

```toml
baseURL                = "https://example.org/"
languageCode           = "id"
defaultContentLanguage = "id" # <<--- Newly added item
title                  = "ExampleSite"
theme                  = "PaperMod"

...

[languages]
  [languages.en]
    languageCode = "id"
    languageName = "Bahasa Indonesia"
    contentDir = "content/id" # <<--- Newly added item
    weight       = 1

    [languages.en.menu]
      ...
  
  [languages.zh]
    languageCode = "zh"
    languageName = "中文"
    contentDir = "content/zh" # <<--- Newly added item
    weight       = 2

    [languages.zh.menu]
      ...

```

> **Tips**: Setelah atribut `defaultContentLanguage`, Anda dapat menambahkan `defaultContentLanguageInSubdir = true` untuk mengaktifkan pembuatan konten bahasa default dalam subdirektori.

## Referensi
- [Quick start | HUGO](https://gohugo.io/getting-started/quick-start/)
- [Configure menus | HUGO](https://gohugo.io/content-management/menus/)
- [Multilingual mode | HUGO](https://gohugo.io/content-management/multilingual/)
- [Installation | hugo-PaperMod Wiki](https://github.com/adityatelange/hugo-PaperMod/wiki/Installation)