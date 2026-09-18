<div align="center">

# 📖 Bab 2: Structured Programming
### Praktikum Konsep Pemrograman · S1 Informatika UNS

[![Status](https://img.shields.io/badge/Status-Materi_Lengkap-brightgreen?style=for-the-badge&logo=gitbook&logoColor=white)](#)
[![Bahasa](https://img.shields.io/badge/Bahasa-C-00599C?style=for-the-badge&logo=c&logoColor=white)](#)
[![Tingkat](https://img.shields.io/badge/Level-Fundamental_Pemula-orange?style=for-the-badge)](#)

<br>

[📋 Kembali ke Daftar Materi Utama](../Daftar_Materi.md) &nbsp;•&nbsp; [Mulai Belajar Modul 2.1 ➡️](01-Pengantar-C-dan-Variabel.md)

---

</div>

## 🌟 Selamat Datang di Dunia Pemrograman C!

> *"A journey of a thousand miles begins with a single step."* — Lao Tzu

Bab 2 adalah **pondasi emas** perjalananmu sebagai seorang programmer. Di sini, kamu akan mentransformasi caramu berpikir: dari sekadar pengguna komputer (*user*) menjadi perancang logika di balik layar (*creator*). 

Kita akan memulai dari anatomi program C paling fundamental, seni merancang logika (algoritma & pseudocode), bermain dengan operator matematika, hingga membuat program cerdas yang bisa mengambil keputusan (`if-else`) dan mengulang tugas secara otomatis (`while`).

---

## 🗺️ Roadmap Pembelajaran Bab 2

Berikut adalah alur konsep yang akan kamu kuasai secara bertahap:

```mermaid
flowchart LR
    A["🚀 2.1 Pengantar & Variabel"] --> B["🧠 2.2 Algoritma & Pseudocode"]
    B --> C["⚡ 2.3 Operator & Precedence"]
    C --> D["🔀 2.4 Seleksi & Loop Dasar"]
    D --> E["🎯 Siap Masuk Bab 3!"]
    
    style A fill:#e1f5fe,stroke:#0288d1,stroke-width:2px;
    style B fill:#fff3e0,stroke:#f57c00,stroke-width:2px;
    style C fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px;
    style D fill:#e8f5e9,stroke:#388e3c,stroke-width:2px;
    style E fill:#fce4ec,stroke:#c2185b,stroke-width:2px;
```

---

## 🎯 Capaian Pembelajaran

Setelah menuntaskan bab ini, kamu diharapkan mampu:

- [x] **Membedah Anatomi Program C:** Memahami fungsi `#include`, `main()`, format specifier, serta variabel & tipe data.
- [x] **Berpikir Komputasional:** Menyusun tahapan pemecahan masalah dari Bahasa Alami $\to$ Pseudocode $\to$ Source Code C yang executable.
- [x] **Menguasai Aritmatika & Tipe Data:** Menggunakan operator perhitungan, assignment, increment/decrement, serta menghindari *integer division bug*.
- [x] **Membuat Keputusan & Iterasi:** Mengimplementasikan percabangan logika dasar (`if-else`) dan perulangan (`while`) tanpa terjebak *infinite loop*.

---

## 📑 Modul Pembelajaran

Klik salah satu materi di bawah untuk mulai menjelajah:

| Modul | Topik & Link | Fokus Materi | Estimasi Waktu |
|:---:|:---|:---|:---:|
| **2.1** | [🚀 **Pengantar C, Variabel & Basic I/O**](01-Pengantar-C-dan-Variabel.md) | Struktur dasar `main()`, tipe data (`int`, `float`, `char`), `printf` & `scanf`. | ⏱️ 15 Menit |
| **2.2** | [🧠 **Algoritma, Pseudocode & Source Code**](02-Algoritma-Pseudocode-SourceCode.md) | Pola pikir problem solving, konversi algoritma ke kode C. | ⏱️ 15 Menit |
| **2.3** | [⚡ **Operator Aritmatika & Assignment**](03-Operator-Aritmatika-dan-Assignment.md) | Compound assignment, modulo, type casting, dan prioritas operator. | ⏱️ 20 Menit |
| **2.4** | [🔀 **Pemilihan & Perulangan Sederhana**](04-PemilihandanPerulanganSederhana.md) | Logika `if-else`, operator boolean (`&&`, `\|\|`, `!`), dan perulangan `while`. | ⏱️ 25 Menit |

---

## 🛠️ Lab Setup: Cara Menjalankan Kode C

Kamu bisa mencoba seluruh contoh kode di modul ini langsung di komputermu:

```bash
# 1. Buka terminal pada folder file C kamu, lalu compile:
gcc nama_program.c -o program

# 2. Jalankan executable:
./program          # Linux / macOS / Git Bash
program.exe        # Windows Command Prompt / PowerShell
```

> [!TIP]
> **Rekomendasi IDE/Editor:** Gunakan **Visual Studio Code** dengan ekstensi **C/C++ (by Microsoft)** dan **Code Runner**. Kamu cukup menekan tombol `Ctrl + Alt + N` (atau tombol Play di kanan atas) untuk langsung meng-compile dan menjalankan kode!

---

## 💡 Golden Rules untuk Mahasiswa Baru

> [!IMPORTANT]
> 1. **Jangan Copy-Paste:** Biasakan mengetik kode secara manual. *Muscle memory* jari-jemarimu sangat krusial dalam mempelajari sintaks bahasa C.
> 2. **Eksperimen Bebas:** Ubah angka, ganti format, buat programnya error dengan sengaja! Kamu akan belajar paling banyak dari memahami *kenapa* suatu error terjadi.
> 3. **Perhatikan Titik Koma (`;`):** 80% error pemula di bahasa C berasal dari tanda titik koma yang tertinggal atau tanda kurung kurawal `{ }` yang tidak tertutup.

---

<div align="center">

[⬅️ Kembali ke Daftar Materi Utama](../Daftar_Materi.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Mulai Modul 2.1: Pengantar C & Variabel ➡️](01-Pengantar-C-dan-Variabel.md)

</div>
