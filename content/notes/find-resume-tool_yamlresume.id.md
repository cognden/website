---
title: Temukan Alat Resume (YAMLResume)
date: 2025-10-06T21:14:37+08:00
tags:
  - alat
draft: false
description: Artikel ini memperkenalkan cara penggunaan alat YAMLResume, termasuk pembuatan template resume dan menghasilkan file PDF melalui perintah Docker, serta analisis kelebihan dan kekurangannya.
summary: Artikel ini memperkenalkan cara penggunaan alat YAMLResume, termasuk pembuatan template resume dan menghasilkan file PDF melalui perintah Docker, serta analisis kelebihan dan kekurangannya.
---

Seperti halaman jawaban yang dikirimkan, tata letak yang mudah dibaca dan kata-kata yang tepat selalu dapat menambah nilai kesan. Jika resume saya dapat dengan mudah dipahami dan memungkinkan orang lain mengenal saya dalam waktu singkat, maka resume saya sudah sukses, apapun keputusan penerimaan nantinya.

Sebagai tambahan, untuk mempercepat pembuatan resume tanpa terjebak dalam lingkaran mati penyesuaian tata letak/format, saya mulai mencoba dari templat Word, templat kanvas, hingga akhirnya menemukan templat resume LaTeX—saya langsung terkejut, "ini yang saya inginkan!".

Sama seperti Markdown, Anda hanya perlu menandai konten dengan penanda, tanpa perlu khawatir tentang masalah tata letak. Tidak seperti Word yang seringkali tata letaknya menjadi tidak stabil, Anda hanya perlu fokus pada konten.

Karena saya tidak menguasai sintaks LaTeX dan ingin membuat resume dengan cepat, saya kemudian menemukan [YAMLResume](https://yamlresume.dev/), sebuah alat yang lebih mudah digunakan. Dengan menggunakan sintaks konfigurasi YAML, penulisan LaTeX menjadi disederhanakan, namun pada dasarnya, konten YAML akan dikonversi ke LaTeX, kemudian file LaTeX akan dikompilasi.

![Gambar contoh YAMLResume](https://img.qisrepo.top/img-yamlresume-yaml-and-pdf.webp)

Pemasangannya juga sangat mudah, karena menyediakan gambar Docker. Anda hanya perlu mengunduh dan menjalankannya tanpa khawatir tentang masalah lingkungan, meskipun penggunaan ruangnya cukup besar, sekitar 3G.

- `new`: Menjalankan perintah ini akan membuat [templat contoh](https://yamlresume.dev/docs#sample-resume)

```bash
docker run --rm -v $(pwd):/home/yamlresume yamlresume/yamlresume new my-resume.yml
```

- `build`: Mengekspor file yang dihasilkan oleh perintah `new` menjadi resume dalam format PDF

```bash
docker run --rm -v $(pwd):/home/yamlresume yamlresume/yamlresume build my-resume.yml
```

> Jika ingin menggunakan metode pemasangan lokal, silakan merujuk ke [dokumen resmi](https://yamlresume.dev/docs/installation#non-docker-users).

Gunakan perintah `new` untuk membuat templat contoh, kemudian tulis konten yang sesuai berdasarkan contoh yang diberikan, dan gunakan perintah `build` untuk menghasilkan resume dalam format PDF.

YAMLResume juga menyediakan beberapa pengaturan kustom, seperti:

- Mengatur [alias](https://yamlresume.dev/docs/layout/sections/aliases) untuk judul bagian
- Melakukan [pengurutan kustom](https://yamlresume.dev/docs/layout/sections/reorder) terhadap urutan tampilan bagian resume

Sampai sini, Anda sudah dapat menulis resume yang cukup baik. Mari kita ringkas.

Kelebihan YAMLResume terletak pada kemudahan penggunaan, namun juga memiliki kekurangan:

- Tingkat kustomisasi halaman sangat rendah; jika ingin menambahkan beberapa ikon pada judul bagian, Anda harus memodifikasi templat tema.
- `description` di bawah bagian `projects` tidak dapat memotong baris secara otomatis; jika teks terlalu panjang, akan melampaui batas tampilan halaman.

---

- [YAMLResume](https://yamlresume.dev/)
- [Types - YAMLResume](https://yamlresume.dev/docs/compiler/types)
- [Rich Text - YAMLResume](https://yamlresume.dev/docs/content/rich-text)

