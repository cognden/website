---
title: Three Python Keywords_async Await Yield
date: 2025-08-31T20:42:52+08:00
tags:
  - Python
draft: false
description: "Artikel ini memperkenalkan tiga kata kunci Python yang penting: async, await, dan yield, menjelaskan makna, fungsi, dan penerapan mereka dalam pemrograman asinkron dan generator. Artikel ini juga menunjukkan melalui contoh praktis bagaimana menggunakan kata kunci ini untuk meningkatkan kinerja program."
summary: "Pelajari tentang kata kunci Python async, await, dan yield, pahami metode penggunaan pemrograman asinkron dan generator, serta tingkatkan efisiensi eksekusi kode."
---

# Tiga Kata Kunci Python: async, await, yield

Baru-baru ini saya mempelajari tiga kata kunci Python yang cukup berguna:
- `async + await` memungkinkan pemrograman asinkron untuk multitasking tanpa mengkonsumsi sumber daya sebanyak multithreading.
- `yield` membantu mengimplementasikan pemuatan malas (lazy loading) dalam kode, di mana komputasi dilakukan hanya ketika dipanggil (yaitu, ketika benar-benar diperlukan), menghindari akumulasi data yang berlebihan atau waktu eksekusi yang lama.


Isi yang akan saya bahas meliputi:
1. Penjelasan tentang `async`, `await`, dan `yield`
2. Pengenalan singkat tentang pemrograman asinkron dan korutin
3. Perbandingan antara operasi sinkron dan asinkron
4. Contoh praktis (unggah file)
5. Ringkasan artikel

## Arti dan Peran `async`, `await`, dan `yield`

### `async`

- **Arti**: Kata kunci yang digunakan untuk mendefinisikan fungsi asinkron (korutin), menandai bahwa fungsi tersebut mungkin berisi operasi asinkron dan perlu dijadwalkan melalui loop acara (event loop).
- **Peran**: Mendeklarasikan sebuah fungsi sebagai korutin, memungkinkannya menggunakan kata kunci `await` secara internal.
- **Sintaks**: Menempatkan kata kunci `async` sebelum definisi fungsi (misal, `async def nama_fungsi()`).


```python
# Mendefinisikan fungsi asinkron (korutin)
async def async_function():
    print("Ini adalah fungsi asinkron")
    # Dapat menggunakan await secara internal untuk memanggil objek yang dapat di-await lainnya
    await another_async_operation()
    print("Eksekusi selesai")
```

### `await`

- **Arti**: Digunakan untuk menjeda eksekusi korutin saat ini, menunggu objek yang dapat di-await lainnya (misal, korutin, Task, Future) selesai, dan mengambil hasil kembalinya.
- **Peran**: Secara sukarela menyerahkan hak eksekusi CPU selama menunggu, memungkinkan loop acara menjadwalkan tugas lain yang siap, mencapai penantian non-blokir dan meningkatkan efisiensi program.
- **Sintaks**:


```python
import asyncio

async def fetch_data():
    # Mensimulasikan permintaan jaringan asinkron
    await asyncio.sleep(2)  # Menunggu 2 detik secara non-blokir
    return "Data yang dikembalikan"      # Mengembalikan data

async def main():
    # Menunggu fetch_data selesai dan mendapatkan hasil
    data = await fetch_data()
    print(data)  # Keluaran: Data yang dikembalikan

asyncio.run(main()) # Menjalankan korutin
```

### `yield`

- **Arti**: Digunakan dalam fungsi generator untuk menjeda eksekusi dan mengembalikan nilai; pemanggilan berikutnya ke generator melanjutkan eksekusi dari posisi yang dijeda.
- **Peran**: Mengubah sebuah fungsi menjadi generator, memungkinkan fungsionalitas iterator untuk menghasilkan data sesuai kebutuhan (evaluasi malas) dan menghemat memori.
- **Sintaks**:


```python
# Mendefinisikan fungsi generator
def number_generator(n):
    for i in range(n):
        yield i  # Mengembalikan i ketika next() dipanggil, kemudian menjeda eksekusi
        print(f"Melanjutkan setelah menghasilkan {i}")

# Membuat objek generator
gen = number_generator(3)

# Mengiterasi generator
print(next(gen))  # Keluaran: 0
print(next(gen))  # Keluaran: Melanjutkan setelah menghasilkan 0 → 1
print(next(gen))  # Keluaran: Melanjutkan setelah menghasilkan 1 → 2
print(next(gen))  # Memicu pengecualian StopIteration (pembuatan selesai)
```

#### Perbedaan Antara `yield` dan `return`

Baik `yield` maupun `return` adalah kata kunci Python untuk mengembalikan nilai dari fungsi, tetapi mekanisme dan kasus penggunaan mereka berbeda secara fundamental. Perbedaan inti terletak pada **apakah mereka menghentikan eksekusi fungsi** dan **cara nilai kembalian digunakan**.


- `return`: Menghentikan fungsi dan mengembalikan hasil
  - **Peran**: Secara langsung menghentikan eksekusi fungsi, mengembalikan hasil ke pemanggil, dan kode berikutnya dalam fungsi tidak dieksekusi.
  - **Fitur**: Sebuah fungsi dapat berisi beberapa pernyataan `return`, tetapi hanya satu yang akan dieksekusi (karena fungsi berhenti setelahnya).
  - **Kasus Penggunaan**: Fungsi reguler, untuk mengembalikan hasil komputasi dan mengakhiri fungsi.
  - **Contoh**:


```python
def add(a, b):
    # Fungsi berhenti segera setelah mengembalikan hasil
    return a + b
    # Baris berikut tidak akan pernah dieksekusi
    print("Baris ini tidak akan pernah dieksekusi")

result = add(2, 3)
print(result)  # Keluaran: 5
```


- `yield`: Menjeda fungsi dan mengembalikan hasil saat ini
  - **Peran**: Menjeda eksekusi fungsi, mengembalikan hasil saat ini ke pemanggil, tetapi tidak menghentikan fungsi; pemanggilan berikutnya melanjutkan dari posisi yang dijeda.
  - **Fitur**: Fungsi yang berisi `yield` menjadi **generator**. Memanggilnya tidak dieksekusi secara langsung tetapi mengembalikan objek generator, yang memerlukan `next()` atau iterasi untuk memicu eksekusi.
  - **Kasus Penggunaan**: Skenario yang memerlukan "pembuatan data sesuai kebutuhan" (misal, mengiterasi kumpulan data besar, evaluasi malas) untuk menghindari beban memori akibat pembuatan semua data sekaligus.
  - **Contoh**:


```python
def number_generator(n):
    for i in range(n):
        # Menjeda fungsi, mengembalikan i, dan melanjutkan di sini pada waktu berikutnya
        yield i
        print(f"Melanjutkan eksekusi setelah menghasilkan {i}")  # Dieksekusi pada pemanggilan berikutnya

# Memanggil fungsi generator; mengembalikan objek generator (tidak dieksekusi secara langsung)
gen = number_generator(3)

# Pemanggilan pertama next(): eksekusi hingga menghasilkan 0, mengembalikan 0, dan menjeda
print(next(gen))  # Keluaran: 0

# Pemanggilan kedua next(): melanjutkan dari jeda, mencetak "Melanjutkan eksekusi setelah menghasilkan 0", kemudian menghasilkan 1 dan mengembalikan 1
print(next(gen))  # Keluaran: Melanjutkan eksekusi setelah menghasilkan 0 → 1

# Pemanggilan ketiga next(): secara serupa, mengembalikan 2
print(next(gen))  # Keluaran: Melanjutkan eksekusi setelah menghasilkan 1 → 2

# Pemanggilan keempat next(): loop berakhir, memicu pengecualian StopIteration
print(next(gen))  # Memicu pengecualian StopIteration
```


Ringkasan:
- `return` bersifat "tidak dapat dibalik": Fungsi berakhir sepenuhnya setelah mengembalikan hasil.
- `yield` bersifat "dijeda sementara": Menjeda setelah mengembalikan hasil intermediate, dan dapat dilanjutkan nanti.

## Apa Itu Pemrograman Asinkron?

Pemrograman asinkron adalah paradigma pemrograman yang menangani inefisiensi yang disebabkan oleh operasi "menunggu" (misal, permintaan jaringan, I/O file) dalam program. Ini memungkinkan program menghindari blokir selama menunggu dan terus mengeksekusi tugas lain.

### Masalah Inti: Titik Pain Pemrograman Sinkron

Dalam pemrograman sinkron, tugas dieksekusi secara berurutan. Ketika menemukan operasi yang memakan waktu (misal, mengambil data jaringan), program "beku" saat menunggu hasil, tidak dapat melakukan tugas lain selama itu, menyebabkan inefisiensi.


Contoh:


```python
import time

# Pendekatan sinkron
def get_data():
    # Mensimulasikan permintaan jaringan (membutuhkan 2 detik)
    time.sleep(2)
    return "Data"

print("Mulai")
data = get_data()  # Program beku di sini selama 2 detik, tanpa operasi lain
print("Data diterima:", data)
print("Selesai")
```


Kode ini akan berhenti selama 2 detik pada `get_data()`, tidak dapat mengeksekusi operasi berikutnya selama menunggu.

### Ide Solusi Pemrograman Asinkron

Pemrograman asinkron memungkinkan program "melewati" sebuah tugas saat menunggu operasi yang memakan waktu, mengeksekusi tugas lain, dan kembali memproses hasil setelah operasi yang memakan waktu selesai.


Konsep Utama:
- **Loop Acara (Event Loop)**: Bertindak sebagai "pusat penjadwalan" yang mengelola urutan eksekusi semua tugas dan memutuskan tugas mana yang akan dijalankan pada waktu tertentu.
- **Korutin**: Fungsi khusus (didefinisikan dengan `async def`) yang dapat dijeda dan dilanjutkan, berperan sebagai unit dasar tugas asinkron.
- **await**: Digunakan dalam korutin. Ketika menemukan operasi yang memakan waktu (misal, permintaan jaringan), `await` memberitahu loop acara: "Saya perlu menunggu hasil; jalankan tugas lain dulu."

### Menggunakan Pemrograman Asinkron di Python

Pemrograman asinkron di Python terutama bergantung pada pustaka `asyncio`, dengan sintaks inti `async/await` untuk mendefinisikan dan menjadwalkan korutin. Berikut adalah langkah-langkah kunci dan contoh:

#### Menjalankan Korutin

Untuk menjalankan korutin, gunakan fungsi `asyncio.run()`. Fungsi ini membuat loop acara dan menjalankan korutin yang ditentukan.


```python
import asyncio

async def main():
    print("Mulai")
    await asyncio.sleep(3)  # Menjeda selama 3 detik untuk mensimulasikan operasi panjang
    print("Selesai")

asyncio.run(main())
```

#### Eksekusi Bersamaan

Gunakan `asyncio.gather()` untuk mengeksekusi beberapa korutin secara bersamaan dan menunggu semua selesai.


```python
import asyncio

async def task1():
    print("Tugas 1 dimulai")
    await asyncio.sleep(1) # Mensimulasikan operasi I/O (penantian non-blokir)
    print("Tugas 1 selesai")

async def task2():
    print("Tugas 2 dimulai")
    await asyncio.sleep(2) # Mensimulasikan operasi I/O (penantian non-blokir)
    print("Tugas 2 selesai")

async def main():
    await asyncio.gather(task1(), task2())

asyncio.run(main())
```

#### Membuat Tugas (Task)

Tugas (task) adalah pembungkus untuk korutin, merepresentasikan eksekusi korutin yang sedang berlangsung atau akan datang. Gunakan `asyncio.create_task()` untuk membuat tugas, yang ditambahkan ke antrian tugas loop acara dan mulai berjalan secara otomatis (kecuali loop acara tidak dimulai).


```python
async def say_hello():
    print("Halo!")

async def main():
    task = asyncio.create_task(say_hello())
    await task
```


`asyncio.gather()` secara otomatis membungkus korutin menjadi Tugas dan memulai 它们 (setara dengan memanggil `create_task()` terlebih dahulu). Ia juga dapat menerima tugas yang dibuat melalui `asyncio.create_task()`.


Memanggil `asyncio.create_task()` secara eksplisit memberikan fleksibilitas lebih, seperti meneruskan parameter khusus ke korutin atau menghasilkan tugas secara batch dalam loop.

#### Kontrol Waktu Habis (Timeout)

Gunakan `asyncio.wait_for()` untuk mengatur batas waktu (timeout) untuk sebuah korutin. Jika korutin tidak selesai dalam waktu yang ditentukan, pengecualian `asyncio.TimeoutError` akan dilemparkan.


```python
import asyncio

async def long_task():
    await asyncio.sleep(10)
    print("Tugas selesai")

async def main():
    try:
        # Membatasi tugas untuk selesai dalam 5 detik
        await asyncio.wait_for(long_task(), timeout=5)
    except asyncio.TimeoutError:
        print("Tugas waktu habis")

asyncio.run(main())  # Keluaran: Tugas waktu habis
```

### Contoh Pemrograman Asinkron

```python
import asyncio

# Mendefinisikan tugas asinkron (korutin)
async def get_data():
    print("Memulai permintaan data...")
    await asyncio.sleep(2) # Mensimulasikan permintaan jaringan (penantian non-blokir)
    print("Permintaan data selesai")
    return "Data"

async def other_task():
    print("Menjalankan tugas lain...")
    await asyncio.sleep(1) # Mensimulasikan operasi I/O (penantian non-blokir)
    print("Tugas lain selesai")

# Korutin utama
async def main():
    # Memulai kedua tugas secara bersamaan
    await asyncio.gather(get_data(), other_task())

# Memulai loop acara
asyncio.run(main())
```


**Urutan Eksekusi**:
1. Loop acara dimulai, menjalankan `main()`, membuat dan memulai dua tugas.
2. `get_data()` mulai dieksekusi, menemukan `await asyncio.sleep(2)`, dijeda, dan loop acara beralih ke `other_task()`.
3. `other_task()` dieksekusi hingga `await asyncio.sleep(1)`, dijeda. Kedua tugas sekarang menunggu, dan loop acara menunggu selama 1 detik.
4. Setelah 1 detik, `other_task()` dilanjutkan, mencetak "Tugas lain selesai", dan berakhir.
5. Setelah 1 detik lagi (total 2 detik), `get_data()` dilanjutkan, mencetak "Permintaan data selesai", dan berakhir.


**Total waktu eksekusi: ~2 detik** (dibandingkan 3 detik untuk eksekusi sinkron), menunjukkan peningkatan efisiensi yang signifikan.

## Studi Kasus

Implementasikan fungsi unggah file menggunakan pemrograman asinkron. Ide umumnya adalah mengambil jalur file yang ditentukan (satu atau lebih) dari terminal, kemudian membaca beberapa file secara asinkron dan menyimpannya ke direktori saat ini.

### Membaca Isi File dengan Generator

Gunakan generator untuk membaca isi file baris demi baris, menghindari luapan memori yang disebabkan oleh file besar.


```python
def file_reader_generator(file_path):
    try:
        with open(file_path, "r", encoding="utf-8") as file:
            for line in file:
                # Menjeda, mengembalikan satu baris, dan mengembalikan baris berikutnya pada pemanggilan selanjutnya
                yield line
    except Exception as e:
        print(f"Error membaca file {file_path}: {e}")
```

### Menyimpan Isi yang Dibaca ke File

Menggunakan pembacaan streaming: membaca isi file baris demi baris dan menyimpannya ke file target secara bersamaan.


```python
async def save_content(original_file_path):
    original_path = Path(original_file_path)
    new_filepath = f"save_{original_path.name}"

    with open(new_filepath, "w", encoding="utf-8") as file:
        for line in file_reader_generator(original_file_path):
            file.write(line)

    print(f"File disimpan ke: {new_filepath}")
```

### Memproses File Secara Asinkron

```python
async def process_files(file_paths):
    tasks = []
    for file_path in file_paths:
        if os.path.exists(file_path):
            tasks.append(
                asyncio.create_task(save_content(file_path))
            )
        else:
            print(f"File tidak ada: {file_path}")
    
    await asyncio.gather(*tasks)
```

### Kode Lengkap

```python
import asyncio
import sys
import os
from pathlib import Path

def file_reader_generator(file_path):
    """
    Membaca isi file menggunakan generator untuk menghindari luapan memori dari file besar
    """
    try:
        with open(file_path, "r", encoding="utf-8") as file:
            for line in file:
                yield line
    except Exception as e:
        print(f"Error membaca file {file_path}: {e}")


async def save_content(original_file_path):
    """Menyimpan isi ke file baru"""
    original_path = Path(original_file_path)
    new_filepath = f"save_{original_path.name}"

    with open(new_filepath, "w", encoding="utf-8") as file:
        for line in file_reader_generator(original_file_path):
            file.write(line)

    print(f"File disimpan ke: {new_filepath}")


async def process_files(file_paths):
    """Memproses semua file"""
    tasks = []
    for file_path in file_paths:
        if os.path.exists(file_path):
            tasks.append(
                asyncio.create_task(save_content(file_path))
            )
        else:
            print(f"File tidak ada: {file_path}")
    
    await asyncio.gather(*tasks)


if __name__ == "__main__":
    # Mengambil parameter jalur file dari terminal
    if len(sys.argv) < 2:
        print("Silakan berikan setidaknya satu jalur file sebagai argumen")
        print("Penggunaan: python main.py file1.txt file2.txt ...")
        sys.exit(1)

    file_paths = sys.argv[1:]
    asyncio.run(process_files(file_paths))
```

## Ringkasan

- `yield` digunakan dalam generator untuk mengimplementasikan protokol iterator, terutama untuk pembuatan data dan pemuatan malas.
- `async` dan `await` digunakan dalam pemrograman asinkron untuk mengimplementasikan operasi I/O non-blokir dan meningkatkan konkurensi program.
- `async/await` biasanya digunakan dengan modul `asyncio` untuk membangun aplikasi asinkron.

## Referensi

- [Asynchronous I/O | Situs Resmi Liao Xuefeng](https://liaoxuefeng.com/books/python/async-io/index.html)
- [Modul Python asyncio | Tutorial Runoob](https://www.runoob.com/python3/python-asyncio.html)
