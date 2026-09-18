<div align="center">

# 🚀 2.1 Pengantar Program C & Variabel
### Bab 2: Structured Programming · Modul Fundamental

[⬅️ Overview Bab 2](Bab2-Overview.md) &nbsp;•&nbsp; [📋 Daftar Materi](../Daftar_Materi.md) &nbsp;•&nbsp; [Modul 2.2: Algoritma & Pseudocode ➡️](02-Algoritma-Pseudocode-SourceCode.md)

---

</div>

## 💡 Mengapa Belajar Bahasa C?

Bahasa C diciptakan oleh **Dennis Ritchie** pada tahun 1972 di Bell Labs. Meskipun usianya sudah lebih dari 50 tahun, bahasa C tetap menjadi **bahasa pemrograman paling berpengaruh di dunia**. Sistem operasi modern seperti Linux, Windows kernel, sistem embedded di perangkat IoT, mesin game, bahkan interpreter bahasa lain (seperti Python) ditulis menggunakan C!

Belajar C akan memberimu pemahaman mendalam tentang bagaimana komputer dan memori bekerja di level yang sangat dekat dengan perangkat keras.

---

## 🏛️ Anatomi Struktur Dasar Program C

Setiap program dalam bahasa C, mulai dari aplikasi kalkulator hingga sistem operasi, memiliki kerangka struktur dasar yang sama. Perhatikan contoh klasik program berikut:

```c
#include <stdio.h>  // (1) Preprocessor Directive: Memuat pustaka I/O standar

int main() {        // (2) Titik masuk utama (Entry Point) program
    printf("Halo, Selamat Datang di Praktikum Pemrograman C!\n"); // (3) Statement/Perintah
    return 0;       // (4) Status kembalian: 0 menandakan eksekusi sukses
}
```

### 🔍 Bedah Kode Baris per Baris:

1. **`#include <stdio.h>`**
   Instruksi kepada *preprocessor* untuk menyisipkan file header `stdio.h` (*Standard Input Output*). File ini berisi deklarasi fungsi penting seperti `printf` (cetak teks) dan `scanf` (baca input).
   > [!NOTE]
   > Baris ini **tidak diakhiri tanda titik koma (`;`)** karena merupakan direktif pra-prosesor, bukan instruksi program C biasa.

2. **`int main()`**
   Fungsi utama program. Compiler C selalu mencari `main()` sebagai **titik awal mula eksekusi**, tidak peduli berapa ratus fungsi lain yang kamu tulis di tempat lain.

3. **`{ ... }` (Kurung Kurawal)**
   Menandai blok tubuh (*body*) dari fungsi tempat instruksi-instruksi C diletakkan.

4. **Statement & Titik Koma (`;`)**
   Setiap instruksi perintah dalam C wajib diakhiri dengan titik koma. Tanda ini berfungsi seperti tanda titik pada sebuah kalimat.

5. **`return 0;`**
   Mengirimkan kode status keluar (*exit status*) kembali ke sistem operasi. Nilai `0` menandakan program selesai berjalan normal tanpa kesalahan.

> [!TIP]
> **Komentar Kode:**
> - `// komentar satu baris`
> - `/* komentar banyak baris */`  
> Komentar sepenuhnya diabaikan oleh compiler. Manfaatkan untuk menjelaskan *alasan* suatu logika dibuat!

---

## 📦 Variabel & Tipe Data

Bayangkan variabel sebagai sebuah **kotak berlabel** di dalam memori RAM komputer. Di dalam bahasa C yang bertipe *statically typed*, kamu harus menentukan:
1. **Label / Nama Kotak** (*Identifier*)
2. **Jenis Barang yang Boleh Masuk** (*Data Type*)

```
Memori RAM:
┌───────────────────────────┐
│  umur (int)       -> [ 20 ]
│  ipk (float)      -> [ 3.85 ]
│  hurufMutu (char) -> [ 'A' ]
└───────────────────────────┘
```

### Tabel Tipe Data Primitif Utama di C:

| Kategori | Tipe Data | Ukuran Memori | Format Specifier | Rentang / Contoh Nilai |
|---|:---:|:---:|:---:|---|
| **Bilangan Bulat** | `int` | 4 byte (32-bit) | `%d` atau `%i` | `-2,147,483,648` s.d. `+2,147,483,647` (Contoh: `42`, `-15`) |
| **Bilangan Bulat Besar** | `long` | 8 byte (64-bit) | `%ld` | Contoh: `9000000000` |
| **Karakter Tunggal** | `char` | 1 byte (8-bit) | `%c` | Simbol ASCII dalam petik tunggal: `'A'`, `'7'`, `'#'` |
| **Desimal (Presisi Tunggal)** | `float` | 4 byte | `%f` | 6-7 digit presisi desimal (Contoh: `3.14f`) |
| **Desimal (Presisi Ganda)** | `double` | 8 byte | `%lf` | 15 digit presisi desimal (Contoh: `3.1415926535`) |
| **Bilangan Bulat Positif** | `unsigned int` | 4 byte | `%u` | `0` s.d. `4,294,967,295` |

### Cara Deklarasi & Inisialisasi:

```c
int umur;              // Deklarasi saja (hati-hati: isi memori masih acak/garbage value!)
umur = 20;             // Inisialisasi nilai

int skor = 100;        // Deklarasi langsung sekaligus inisialisasi
float tinggi = 172.5f; // Gunakan akhiran 'f' untuk literal float
char grade = 'A';      // WAJIB petik tunggal untuk char, BUKAN petik ganda
int a = 1, b = 2, c = 3; // Deklarasi jamak dengan tipe sama
```

> [!IMPORTANT]
> **Aturan Penamaan Variabel (Identifier Rules):**
> - ✅ Boleh memuat huruf (`a-z`, `A-Z`), angka (`0-9`), dan garis bawah (`_`).
> - ❌ **Dilarang** diawali dengan angka (contoh: `1nilai` ❌, gunakan `nilai1` ✅).
> - ❌ **Case-Sensitive:** variabel `total`, `Total`, dan `TOTAL` dianggap 3 kotak yang sepenuhnya berbeda.
> - ❌ **Dilarang** memakai kata kunci (*keywords*) bahasa C (contoh: `int`, `return`, `float`, `for`).

---

## 🖨️ Menampilkan Data: `printf()`

Fungsi `printf()` digunakan untuk mencetak teks serta nilai variabel ke layar terminal menggunakan placeholder yang disebut **Format Specifier**.

```c
#include <stdio.h>

int main() {
    char nama[] = "Alzen";
    int angkatan = 2026;
    float ipk = 3.92f;

    printf("Nama Mahasiswa : %s\n", nama);
    printf("Tahun Angkatan : %d\n", angkatan);
    printf("IPK Kumulatif  : %.2f\n", ipk); // "%.2f" membatasi cetakan 2 angka di belakang koma

    return 0;
}
```

**Output Terminal:**
```text
Nama Mahasiswa : Alzen
Tahun Angkatan : 2026
IPK Kumulatif  : 3.92
```

### Escape Sequences Penting:
- `\n` : Pindah baris (*newline*)
- `\t` : Tabulasi horizontal (*tab*)
- `\\` : Mencetak karakter backslash
- `\"` : Mencetak karakter kutip ganda

---

## ⌨️ Membaca Input dari Pengguna: `scanf()`

Fungsi `scanf()` membaca data dari keyboard dan menyimpannya langsung ke alamat memori variabel tujuan.

```c
#include <stdio.h>

int main() {
    int nim;
    float nilaiUjian;

    printf("Masukkan 3 digit terakhir NIM: ");
    scanf("%d", &nim); // Menggunakan operator & (address-of)

    printf("Masukkan Nilai Ujian: ");
    scanf("%f", &nilaiUjian);

    printf("\nData Tersimpan -> NIM: %d | Nilai: %.1f\n", nim, nilaiUjian);
    return 0;
}
```

> [!WARNING]
> ### ⚠️ Jebakan Paling Sering: Lupa Tanda Ampersand (`&`)
> Mengapa harus ada tanda `&` di depan nama variabel saat `scanf`?  
> Karena `scanf()` membutuhkan **alamat memori** variabel untuk memasukkan data yang kamu ketik. Jika kamu menulis `scanf("%d", nim);` tanpa `&`, program akan mencoba menulis ke alamat memori acak dan menyebabkan program **Crash (Segmentation Fault)**!
>
> *(Catatan khusus: untuk string/array karakter seperti `char nama[50]`, tanda `&` tidak diperlukan karena nama array sudah otomatis berupa alamat memori).*

---

## 🥊 Tantangan & Mini Kuis

Uji pemahamanmu sebelum beranjak ke materi berikutnya!

### Soal 1: Tebak Output
Perhatikan potongan kode berikut:
```c
int a = 15;
int b = 4;
printf("Hasil: %d\n", a / b);
```
Berapakah output yang keluar di layar? Apakah `3.75` atau `3`?

<details>
<summary>🔍 <b>Klik untuk Buka Kunci Jawaban</b></summary>

**Jawaban:** Outputnya adalah `3`.  
**Penjelasan:** Karena kedua operan (`a` dan `b`) bertipe bilangan bulat (`int`), C akan melakukan **integer division**, yang membuang seluruh angka di belakang koma (truncation). Jika ingin hasil desimal `3.75`, salah satu variabel harus di-cast menjadi desimal: `(float)a / b` dengan format specifier `%f`.
</details>

---

### Soal 2: Mini Project Mandiri
Buatlah sebuah program C bernama `biodata.c` yang:
1. Meminta pengguna menginput:
   - Inisial nama (1 karakter, gunakan `%c`)
   - Umur (bilangan bulat, gunakan `%d`)
   - Tinggi badan dalam meter (desimal, gunakan `%f`)
2. Menampilkan kartu identitas rapi seperti format berikut:
```text
==============================
      KARTU IDENTITAS
==============================
Inisial : A
Umur    : 19 Tahun
Tinggi  : 1.75 m
==============================
```

---

<div align="center">

[⬅️ Kembali ke Overview Bab 2](Bab2-Overview.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 2.2: Algoritma & Pseudocode ➡️](02-Algoritma-Pseudocode-SourceCode.md)

</div>
