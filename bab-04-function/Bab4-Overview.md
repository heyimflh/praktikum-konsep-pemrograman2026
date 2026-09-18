<div align="center">

# 📖 Bab 4: C Function
### Praktikum Konsep Pemrograman · S1 Informatika UNS

[![Status](https://img.shields.io/badge/Status-Materi_Lengkap-brightgreen?style=for-the-badge&logo=gitbook&logoColor=white)](#)
[![Bahasa](https://img.shields.io/badge/Bahasa-C-00599C?style=for-the-badge&logo=c&logoColor=white)](#)
[![Tingkat](https://img.shields.io/badge/Level-Intermediate-blueviolet?style=for-the-badge)](#)

<br>

[📋 Kembali ke Daftar Materi Utama](../Daftar_Materi.md) &nbsp;•&nbsp; [Mulai Belajar Modul 4.1 ➡️](01-Pengenalan-Function.md)

---

</div>

## 🧩 Seni Pemrograman Modular: Pecah & Kuasai!

Pernahkah kamu membayangkan membuat aplikasi berukuran ribuan baris, tetapi semuanya ditulis di dalam satu fungsi `main()` raksasa? Kode tersebut pasti akan menjadi mimpi buruk: sulit dibaca, penuh duplikasi, dan mustahil untuk di-debug!

Filosofi utama rekayasa perangkat lunak adalah **Divide and Conquer (Bagi dan Selesaikan)**. Di Bab 4 ini, kita akan mempelajari **Fungsi (Function)** — blok kode mandiri yang dapat dipanggil berkali-kali (*reusable*) untuk menyelesaikan tugas spesifik. 

Kita juga akan mengeksplorasi ribuan kemampuan bawaan dari **C Standard Library**, memahami batas wilayah variabel (**Scope**), dan membedah teknik memukau pemanggilan diri sendiri (**Rekursi**).

---

## 🗺️ Roadmap Pembelajaran Bab 4

```mermaid
flowchart LR
    A["⚙️ 4.1 Pengenalan Function<br><i>Definisi, Parameter, Return</i>"] --> B["📚 4.2 Library Bawaan C<br><i>stdio, math, string, stdlib</i>"]
    B --> C["🌐 4.3 Aturan Scope<br><i>Lokal vs Global & Shadowing</i>"]
    C --> D["🌀 4.4 Rekursi Elegan<br><i>Base Case & Call Stack</i>"]
    D --> E["🚀 Siap Masuk Bab 5: Array!"]
    
    style A fill:#e1f5fe,stroke:#0288d1,stroke-width:2px;
    style B fill:#e8f5e9,stroke:#388e3c,stroke-width:2px;
    style C fill:#fff3e0,stroke:#f57c00,stroke-width:2px;
    style D fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px;
    style E fill:#fce4ec,stroke:#c2185b,stroke-width:2px;
```

---

## 🎯 Capaian Pembelajaran

Setelah menuntaskan Bab 4, praktikan diharapkan mampu:

- [x] **Merancang Fungsi Mandiri:** Mendeklarasikan prototipe (*function prototype*), mendefinisikan parameter masukan, dan mengembalikan nilai (*return value*) yang tepat.
- [x] **Memanfaatkan Standar Library C:** Menggunakan fungsi matematika canggih (`math.h`), manipulasi teks string (`string.h`), dan utilitas angka acak (`stdlib.h`).
- [x] **Memahami Arsitektur Scope:** Membedakan visibilitas variabel lokal vs global untuk mencegah bug tabrakan data (*side-effects*).
- [x] **Menyelesaikan Masalah dengan Rekursi:** Merancang fungsi rekursif dengan *base case* yang kokoh tanpa menyebabkan *stack overflow*.

---

## 📑 Modul Pembelajaran

| Modul | Topik & Tautan | Pokok Bahasan Utama | Estimasi |
|:---:|:---|:---|:---:|
| **4.1** | [⚙️ **Pengenalan Function**](01-Pengenalan-Function.md) | Deklarasi, definisi, parameter, argument, fungsi `void`, dan prinsip DRY. | ⏱️ 25 Menit |
| **4.2** | [📚 **Fungsi Library Bawaan C**](02-Fungsi-Library-C.md) | Bedah pustaka `<math.h>`, `<string.h>`, `<stdlib.h>`, dan `<stdio.h>`. | ⏱️ 25 Menit |
| **4.3** | [🌐 **Aturan Scope & Lifetime**](03-Aturan-Scope.md) | Local Scope, Global Scope, *block scope*, dan fenomena *variable shadowing*. | ⏱️ 15 Menit |
| **4.4** | [🌀 **Rekursi (Recursion)**](04-Rekursi.md) | Konsep fungsi memanggil diri sendiri, *base case*, visualisasi *call stack*. | ⏱️ 20 Menit |

---

## 💡 Manfaat Utama Menggunakan Function: Prinsip D.R.Y

> [!TIP]
> **D.R.Y = Don't Repeat Yourself!**
> 1. **Mencegah Duplikasi Kode:** Tulis satu fungsi sekali, panggil 100 kali di mana saja.
> 2. **Perbaikan Terpusat:** Jika ada bug atau perubahan rumus, kamu cukup memperbaikinya di dalam satu fungsi tersebut.
> 3. **Meningkatkan Keterbacaan:** Program menjadi seperti membaca daftar cerita (misal: `siapkanData()`, `hitungTotal()`, `cetakLaporan()`).

---

<div align="center">

[⬅️ Kembali ke Bab 3: Program Control](../bab-03-program-control/Bab3-Overview.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Mulai Modul 4.1: Pengenalan Function ➡️](01-Pengenalan-Function.md)

</div>
