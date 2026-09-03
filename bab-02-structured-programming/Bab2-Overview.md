# 📖 Bab 2 - Structured Programming

> Praktikum Konsep Pemrograman · S1 Informatika UNS

Bab ini membahas fondasi pemrograman terstruktur dalam bahasa C: mulai dari cara kerja program C paling sederhana, cara berpikir sebelum menulis kode (algoritma & pseudocode), operator, hingga struktur kendali utama — **pemilihan (percabangan)** dan **perulangan**. Semua sub-materi disusun berurutan dan saling membangun, jadi disarankan dipelajari sesuai urutan di bawah.

## 🎯 Capaian Pembelajaran

Setelah menyelesaikan Bab 2, praktikan diharapkan mampu:
- Menjelaskan struktur dasar sebuah program C dan mengoperasikan variabel serta tipe data
- Membedakan algoritma, pseudocode, dan source code, serta menerjemahkan satu ke yang lain
- Menggunakan operator assignment dan aritmatika dengan benar, termasuk memahami operator precedence
- Menerapkan struktur pemilihan (`if`, `if-else`, `switch-case`) dan perulangan (`for`, `while`, `do-while`) sederhana

## 📑 Daftar Sub-Materi

| Modul | Topik Pembelajaran | Deskripsi Singkat |
|:---:|:---|:---|
| **i** | [Pengantar (Program Sederhana, Variabel, dan Basic I/O)](01-Pengantar-C-dan-Variabel.md) | Struktur program C, tipe data, variabel, I/O dasar |
| **ii** | [Algoritma, Pseudocode, dan Source Code](02-Algoritma-Pseudocode-SourceCode.md) | Cara berpikir sebelum menulis kode, notasi pseudocode |
| **iii** | [Operasi Assignment dan Aritmatika](03-Operator-Aritmatika-dan-Assignment.md) | Operator matematika, assignment, increment/decrement, precedence |
| **iv** | [Pemilihan dan Perulangan Sederhana](04-PemilihandanPerulanganSederhana.md) | Penggunaan `if-else` dan perulangan `while`/`for` secara mendasar |

## 🖥️ Cara Menjalankan Contoh Program

Semua contoh program bisa ditulis dan dijalankan dengan mudah. Kompilasi menggunakan GCC:
```bash
gcc nama_file.c -o output
./output          # Linux/Mac
output.exe        # Windows
```
Atau langsung jalankan dari VS Code menggunakan extension **Code Runner** / **C/C++**.

## ✅ Tips Belajar Efektif

- **Jangan cuma dibaca — ketik ulang manual.** Mengetik ulang kode (bukan copy-paste) membantu otak terbiasa dengan sintaks C.
- **Ubah-ubah nilai input**, lihat bagaimana output berubah. Ini cara tercepat memahami logika program.
- **Gambar dulu di kertas** (algoritma/pseudocode) sebelum menulis kode untuk kasus yang agak rumit.
- Kalau ada error, baca pesan error dari compiler baris per baris — 90% error di tahap ini adalah salah titik koma, kurung, atau tipe data.

---
<div align="center">
⬅️ <b><a href="../Daftar_Materi.md">Kembali ke Daftar Materi Utama</a></b>
</div>
