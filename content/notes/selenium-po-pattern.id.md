---
title: Pola PO Selenium
date: 2025-07-31T09:18:36+08:00
tags:
  - catatan
  - alat-pengujian
showToc: true
TocOpen: true
draft: false
description: Artikel ini mencatat pemahaman saya tentang Pola Objek Halaman (PO) dalam Selenium, termasuk apa itu pola PO dan contoh yang sesuai.
summary: Artikel ini mencatat pemahaman saya tentang Pola Objek Halaman (PO) dalam Selenium, termasuk apa itu pola PO dan contoh yang sesuai.
---

> Artikel ini mencatat pemahaman saya tentang pola PO Selenium, termasuk apa itu pola PO dan contoh yang sesuai.

Ketika pertama kali melihat pola PO (Model Objek Halaman), ada dua pertanyaan yang muncul di pikiran saya:

- Apa itu ini?
- Bagaimana saya harus menggunakannya?

Dengan kedua pertanyaan ini, saya mencari informasi dan belajar bahwa ini adalah pola desain yang memisahkan kode untuk menemukan dan mengoperasikan elemen halaman dari kode pengujian untuk meningkatkan kemudahan pemeliharaan dan kemampuan penggunaan kembali pengujian.

Artinya, enkapsulasi penemuan dan operasi elemen halaman, kemudian sediakan antarmuka tunggal ke atas, dan kemudian lakukan panggilan.

Melakukan ini memiliki keuntungan. Jika elemen halaman berubah (seperti perubahan `id` tag, perubahan struktur `html`, dll.), Anda hanya perlu menyesuaikan kode yang telah dienkapsulasi sebelumnya tanpa mengubah kode pengujian.

Berikut adalah contoh kode tanpa menggunakan pola PO:

```python
import pytest
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

@pytest.fixture  
def driver():  
    driver = webdriver.Edge()  
    driver.get("https://example.com/login")  
    yield driver  
    driver.quit()

def test_login_without_po(driver):
    # Langsung temukan dan operasikan elemen dalam kasus pengujian
    # Tunggu sampai kotak input username selesai dimuat
    username_input = WebDriverWait(driver, 10).until(
        EC.presence_of_element_located((By.ID, "username"))
    )
    username_input.send_keys("test_user")
    
    # Tunggu sampai kotak input password selesai dimuat
    password_input = WebDriverWait(driver, 10).until(
        EC.presence_of_element_located((By.ID, "password"))
    )
    password_input.send_keys("test_password")
    
    # Tunggu sampai tombol login dapat diklik dan kliknya
    login_button = WebDriverWait(driver, 10).until(
        EC.element_to_be_clickable((By.ID, "login-button"))
    )
    login_button.click()
    
    # Verifikasi pesan selamat datang setelah login berhasil
    login_message = WebDriverWait(driver, 10).until(
        EC.visibility_of_element_located((By.ID, "login_message"))
    )
    assert "Welcome" in login_message.text
```

Seperti yang Anda lihat, sejumlah besar detail operasi elemen bercampur dengan logika pengujian, yang tidak hanya melanggar **Prinsip Tanggung Jawab Tunggal** tetapi juga menyebabkan sejumlah besar kode duplikat. Satu-satunya perbedaan antara mereka adalah elemen halaman.

Bayangkan saja, saat ini hanya pengujian untuk fungsi login, sehingga jumlah kode terlihat relatif kecil, tetapi jika Anda ingin menguji fungsi seluruh situs web (seperti pendaftaran, logout, informasi pribadi, dll.), maka penemuan dan operasi elemen halaman yang sama harus diulang dalam setiap pengujian.

Selain itu, selama proses pengembangan, struktur halaman web akan terus berubah. Setiap kali ada perubahan, kita perlu menyesuaikan kembali kode pengujian. Tidak apa-apa untuk satu atau dua perubahan, tetapi tidak tahan ketika ada banyak!

Saya menganggap diri saya orang yang malas, jadi tentu saja saya ingin sehemat mungkin.

## Konsep Inti Pola PO

Setelah menulis sejauh ini, perlu untuk memperkenalkan konsep inti pola PO untuk memudahkan pemahaman yang lebih baik dalam contoh berikut.

- Objek halaman
    - Abstraksikan setiap halaman web sebagai kelas, dan enkapsulasi elemen dan operasi pada halaman sebagai atribut dan metode kelas.
    - Misalnya, halaman login dapat diabstraksikan sebagai kelas `LoginPage`.
- Pemisahan perhatian antara operasi halaman dan kasus pengujian
    - **Objek halaman**: Bertanggung jawab untuk penemuan elemen dan operasi interaktif (seperti klik dan input).
    - **Kasus pengujian**: Berfokus pada logika bisnis dan panggil metode objek halaman untuk menyelesaikan pengujian.

## Contoh Implementasi Pola PO

Sekarang mari kita mulai operasi nyata. Menurut deskripsi di atas, dengan memisahkan operasi halaman dari kasus pengujian, proyek dapat dibagi menjadi:

- **Lapisan objek halaman**: Satu kelas untuk setiap halaman, enkapsulasi elemen dan operasi.
- **Lapisan kasus pengujian**: Panggil objek halaman untuk menyelesaikan pengujian.

Struktur proyek adalah sebagai berikut:
```txt
my_project/
├── pages/
│   ├── page_login.py
│   └── ...
└── tests/
    ├── test_login.py
    └── ...
```

### Definisikan `page_login.py`

Misalkan kita ingin menguji antarmuka login. Kita dapat membuat kelas `LoginPage` untuk mengenkapsulasi operasi "login".

```python
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

class LoginPage:
    def __init__(self, driver):
        """Inisialisasi instance LoginPage.
        
        Parameter:
            driver (WebDriver): Instance WebDriver Selenium yang diteruskan,
                              digunakan untuk berinteraksi dengan browser.
        """
        self.__driver = driver
        self.__username_input = (By.ID, "username")
        self.__password_input = (By.ID, "password")
        self.__login_button = (By.ID, "login_button")
        self.__login_message = (By.ID, "login_message")

    def login(self, username, password):
        """Masuk ke sistem.

        Parameter:
            username (str): Nama pengguna
            password (str): Kata sandi
        """
        self.__enter_username(username)
        self.__enter_password(password)
        self.__click_login()

    def get_login_message(self):
        """Dapatkan teks pesan login.

        Mengembalikan:
            str: Konten teks pesan login.
        """
        return WebDriverWait(self.__driver, 10).until(
            EC.visibility_of_element_located(self.__login_message)
        ).text

    def __enter_username(self, username):
        """Masukkan nama pengguna yang ditentukan ke dalam kotak input username.

        Parameter:
            username (str): Nama pengguna yang akan dimasukkan
        """
        WebDriverWait(self.__driver, 10).until(
            EC.visibility_of_element_located(self.__username_input)
        ).send_keys(username)

    def __enter_password(self, password):
        """Masukkan kata sandi yang ditentukan ke dalam kotak input password.

        Parameter:
            password (str): Kata sandi yang akan dimasukkan
        """
        WebDriverWait(self.__driver, 10).until(
            EC.visibility_of_element_located(self.__password_input)
        ).send_keys(password)

    def __click_login(self):
        """Tunggu sampai tombol login dapat diklik dan lakukan operasi klik."""
        WebDriverWait(self.__driver, 10).until(
            EC.element_to_be_clickable(self.__login_button)
        ).click()
```

### Definisikan `test_login.py`

Sekarang buat `test_login.py` untuk menguji fungsi login.

```python
import pytest
from selenium import webdriver
from pages.page_login import LoginPage

@pytest.fixture
def driver():
    """Buat dan konfigurasikan instance WebDriver Edge untuk menguji fungsi login.

    Mengembalikan:
        WebDriver: Instance WebDriver Edge yang dikonfigurasi
    """
    driver = webdriver.Edge()
    driver.get("https://example.com/login")
    yield driver
    driver.quit()

def test_login(driver):
    """Uji fungsi login.

    Parameter:
        driver (WebDriver): Instance WebDriver Selenium untuk operasi otomatisasi browser.
    """
    login_page = LoginPage(driver)
    login_page.login("test_user", "test_password")
    assert "Welcome" in login_page.get_login_message(), "Login gagal, pesan selamat datang tidak ditemukan"
```

## Enkapsulasi Lebih Lanjut: `base.py`

Apakah contoh ini sudah sempurna pada titik ini? Tidak, operasi pada elemen halaman dapat dienkapsulasi lebih lanjut dengan mengekstraksi operasi umum.

Anda mungkin merasa bingung dan bertanya-tanya, "Ya, itu bisa dienkapsulasi lebih lanjut, tapi mengapa?"

Jawabannya terletak pada kemudahan pemeliharaan. Bayangkan bahwa dalam sebuah program, kita akan sering menggunakan string `"passwd"`. Haruskah kita menggunakan variabel untuk menyimpannya atau menggunakannya langsung?

Tentu saja, kita memilih untuk mendefinisikan variabel untuk menyimpannya. Dengan cara ini, jika ada kebutuhan di masa depan, kita dapat mengubahnya di satu tempat dan seluruh program akan secara otomatis menggunakan konten baru.

Di sini, itu sama. Dengan mengekstraksi operasi yang sama, jika ada metode implementasi yang lebih baik di masa depan, kita hanya dapat mengubahnya di satu tempat tanpa mengubah kode lainnya.

Jadi kita tambahkan lapisan dasar untuk menyimpan operasi umum.

Struktur proyek yang diperbarui adalah sebagai berikut:
```txt
my_project/
├── bases/        # Baru ditambahkan
│   ├── base.py   # Simpan operasi umum
│   └── ...
├── pages/
│   ├── page_login.py
│   └── ...
└── tests/
    ├── test_login.py
    └── ...
```

### Definisikan `base.py`

Definisikan kelas `Base` untuk menyimpan pencarian elemen dan beberapa metode operasi umum (seperti klik dan memasukkan teks).

```python
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

class Base:
    def __init__(self, driver):
        """Inisialisasi kelas Base.

        Parameter:
            driver (WebDriver): Instance WebDriver yang digunakan untuk berinteraksi dengan browser.
        """
        self.driver = driver

    def base_find_element(self, locator, timeout=10):
        """Tunggu sampai elemen yang ditentukan menjadi terlihat di halaman.

        Parameter:
            locator (tuple): Tuple yang digunakan untuk menemukan elemen, biasanya terdiri dari By dan nilai penemuan.
            timeout (int, opsional): Waktu maksimum (dalam detik) untuk menunggu elemen. Standarnya adalah 10 detik.

        Mengembalikan:
            WebElement: Elemen terlihat yang telah ditunggu.
        """
        return WebDriverWait(self.driver, timeout=timeout).until(
            EC.visibility_of_element_located(locator)
        )

    def base_click(self, locator):
        """Lakukan operasi klik pada elemen.

        Parameter:
            locator (tuple): Tuple untuk menemukan elemen, berisi metode penemuan dan nilai penemuan.
        """
        self.base_find_element(locator).click()

    def base_send_keys(self, locator, text):
        """Masukkan teks ke dalam elemen yang ditemukan.

        Parameter:
            locator (tuple): Tuple untuk menemukan elemen, biasanya berisi metode penemuan dan nilai penemuan.
            text (str): Konten teks yang akan dimasukkan.
        """
        element = self.base_find_element(locator)
        element.clear()
        element.send_keys(text)
```

### Modifikasi `page_login.py`

Saat ini, hanya `page_login.py` yang perlu dimodifikasi, dan tidak perlu mengubah kasus pengujian karena kita telah menyediakan antarmuka panggilan tunggal untuk kasus pengujian. Selama kita tidak mengubah ini, kita bisa. Inilah pesona enkapsulasi.

```python
from selenium.webdriver.common.by import By
from bases.base import Base

class LoginPage:
    def __init__(self, driver):
        """Inisialisasi instance LoginPage.
        
        Parameter:
            driver (WebDriver): Instance WebDriver Selenium yang diteruskan,
                              digunakan untuk berinteraksi dengan browser.
        """
        self.__base = Base(driver)
        self.__username_input = (By.ID, "username")
        self.__password_input = (By.ID, "password")
        self.__login_button = (By.ID, "login_button")
        self.__login_message = (By.ID, "login_message")

    def login(self, username, password):
        """Masuk ke sistem

        Parameter:
            username (str): Nama pengguna
            password (str): Kata sandi
        """
        self.__enter_username(username)
        self.__enter_password(password)
        self.__click_login()

    def get_login_message(self) -> str:
        """Dapatkan teks pesan login.

        Mengembalikan:
            str: Konten teks pesan login.
        """
        return self.__base.base_find_element(self.__login_message).text

    def __enter_username(self, username):
        """Masukkan nama pengguna yang ditentukan ke dalam kotak input username.

        Parameter:
            username (str): Nama pengguna yang akan dimasukkan
        """
        self.__base.base_send_keys(self.__username_input, username)

    def __enter_password(self, password):
        """Masukkan kata sandi yang ditentukan ke dalam kotak input password.

        Parameter:
            password (str): Kata sandi yang akan dimasukkan
        """
        self.__base.base_send_keys(self.__password_input, password)

    def __click_login(self):
        """Tunggu sampai tombol login dapat diklik dan lakukan operasi klik."""
        self.__base.base_click(self.__login_button)
```

## Ringkasan

Pola PO membuat kode pengujian lebih jelas, lebih mudah dipelihara, dan dapat digunakan kembali dengan mengenkapsulasi elemen dan operasi halaman sebagai objek.

- **Meningkatkan kemampuan penggunaan kembali kode**: Beberapa kasus pengujian dapat berbagi objek halaman yang sama.
- **Meningkatkan kemudahan pemeliharaan**: Hanya perlu memperbarui objek halaman ketika halaman berubah.
- **Meningkatkan keterbacaan**: Kasus pengujian lebih dekat dengan bahasa alami, seperti `login_page.enter_username("test")`.

Menurut pola PO, struktur proyek dapat dibagi menjadi tiga lapisan:

- **Lapisan dasar**: Enkapsulasi metode umum.
- **Lapisan objek halaman**: Satu kelas untuk setiap halaman, enkapsulasi elemen dan operasi.
- **Lapisan kasus pengujian**: Panggil objek halaman untuk menyelesaikan pengujian.
