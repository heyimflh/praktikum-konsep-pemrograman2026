# Bab 11 — Preprocessor pada Bahasa C
## Bagian 2: Conditional Compilation

> **Peta perjalanan Bab 11**
>
> | Bagian | Isi | Posisi |
> |---|---|---|
> | **Bagian 1** | 11.1 Pengantar Preprocessor · 11.2 Symbolic Constant dan Macro | [« Kembali ke Bagian 1](Bab-11-Bagian-1-Pengantar-dan-Macro.md) |
> | **Bagian 2** (file ini) | 11.3 Conditional Compilation · Ringkasan Bab · Latihan | 📍 *kamu di sini* |

> **Sebelum mulai, ingat lagi sedikit dari Bagian 1:**
>
> - Preprocessor adalah tahap olah teks **sebelum** compile, dan semua directive-nya diawali `#`.
> - `#define` membuat sebuah simbol (symbolic constant atau macro), dan preprocessor hanya **mengganti teks**.
> - Kita sempat melihat sekilas `#if defined(...)` dan `#if !defined(NDEBUG)` di 11.1. Nah, di bagian ini kita bedah semuanya secara tuntas.

**Daftar isi Bagian 2**

- [11.3 Conditional Compilation](#113--conditional-compilation)
- [Ringkasan Akhir Bab 11](#ringkasan-akhir-bab-11)
- [Latihan Bagian 2](#latihan-bagian-2)

---

# 11.3 — Conditional Compilation

## Definisi

**Conditional compilation** adalah mekanisme pada bahasa level menengah-rendah seperti C untuk **meng-compile hanya bagian kode yang diinginkan**, berdasarkan sebuah kondisi.

Cara gampang membayangkannya: ini seperti `if-else`, tetapi dijalankan saat **preprocessing**, sebelum compile. Bagian yang tidak memenuhi kondisi dibuang dari hasil preprocessing, sehingga compiler tidak pernah melihatnya.

| | `if-else` biasa | `#if` … `#endif` |
|---|---|---|
| Dieksekusi oleh | Program saat berjalan (runtime) | Preprocessor saat compile |
| Kode yang tidak terpilih | Tetap ada di program | Dibuang, tidak ikut di-compile |
| Kondisi yang dicek | Nilai variabel | Konstanta / macro |

Directive yang tersedia: `#if`, `#ifdef`, `#ifndef`, `#else`, `#elif`, dan penutupnya `#endif`. Mari bahas satu per satu.

## Directive `#if`

```c
#if constant_expression
```

`#if` memeriksa apakah `constant_expression` bernilai **benar (bukan nol)**. Jika ya, blok kode di bawahnya ikut di-compile.

Syarat penting untuk `constant_expression`:

- ✅ Boleh berisi konstanta integer, macro yang berisi konstanta (hasil `#define` dari Bagian 1), operator aritmetika, perbandingan (`<`, `>`, `==`, dst.), logika (`&&`, `||`, `!`), dan operator `defined`.
- ❌ Tidak boleh berisi variabel, pemanggilan fungsi, `sizeof`, cast, pointer/address (`&`), serta operasi `++` dan `--`.

Alasannya sederhana: pada saat preprocessing, program belum berjalan sehingga tidak ada variabel yang punya nilai.

> ⚠️ Identifier yang **tidak pernah didefinisikan** pada `#if` dianggap bernilai `0`. Jadi salah ketik nama macro tidak memunculkan error; kodenya hanya diam-diam tidak ikut di-compile.

## Directive `#ifdef`

```c
#ifdef identifier
```

`#ifdef` memeriksa apakah `identifier` **sudah didefinisikan**. Jika sudah, blok kode di bawahnya ikut di-compile. Isi nilainya tidak penting, bahkan `#define DEBUG` tanpa replacement sekalipun sudah dianggap "terdefinisi".

`identifier` bisa didefinisikan lewat dua cara:

1. Dengan `#define` di dalam source code (seperti di 11.2), atau
2. Dari **command line** saat compile, misalnya pada GCC dengan option `-D`:

```bash
gcc main.c -DDEBUG            # sama seperti menulis #define DEBUG
gcc main.c -DDLIMIT=150       # sama seperti menulis #define DLIMIT 150
```

Cara kedua sangat praktis, karena kamu bisa menyalakan/mematikan fitur tanpa menyentuh source code.

## Directive `#ifndef`

```c
#ifndef identifier
```

`#ifndef` adalah **kebalikan** `#ifdef`: blok di bawahnya ikut di-compile jika `identifier` **belum** didefinisikan.

Pemakaian paling terkenal dari `#ifndef` adalah **include guard** pada file header, untuk mencegah isi header ter-include dua kali dan menimbulkan error redefinisi:

```c
/* mylib.h */
#ifndef MYLIB_H
#define MYLIB_H

void halo(void);
int tambah(int a, int b);

#endif /* MYLIB_H */
```

Alurnya: saat `mylib.h` pertama kali di-include, `MYLIB_H` belum terdefinisi sehingga isinya diproses (dan `MYLIB_H` langsung didefinisikan). Jika di-include lagi, `MYLIB_H` sudah ada, sehingga seluruh isinya dilewati.

## Directive `#else`

```c
#else
```

Jika `#if`, `#ifdef`, dan `#ifndef` ibarat `if`, maka `#else` ibarat `else`. Blok di bawahnya ikut di-compile jika kondisi di atasnya **tidak terpenuhi**. `#else` bersifat **opsional**, dan hanya boleh muncul **satu kali** pada satu rangkaian kondisi.

## Directive `#elif`

```c
#elif constant_expression
```

`#elif` ibarat `else if`: dicek hanya jika kondisi-kondisi sebelumnya tidak terpenuhi. Sifatnya juga **opsional**, dan boleh dipakai berkali-kali (selama sebelum `#else`).

## Directive `#endif`

```c
#endif
```

Tidak seperti `if-else` di C yang ditutup dengan kurung kurawal, rangkaian conditional directive **wajib ditutup dengan `#endif`**. Aturannya: **setiap `#if` / `#ifdef` / `#ifndef` harus punya tepat satu `#endif` pasangannya**.

Untuk satu rangkaian `#if … #elif`, cukup satu `#endif`:

```c
#if constant_expression
/* ... */
#elif constant_expression
/* ... */
#endif
```

Untuk directive yang **bersarang (nested)**, setiap `#if` di dalam harus punya `#endif`-nya sendiri:

```c
#if constant_expression
/* ... */
#elif constant_expression
    #if constant_expression
    /* ... */
    #else
    /* ... */
    #endif
#else
    #if constant_expression
    /* ... */
    #else
    /* ... */
    #endif
#endif

/* Indentasi hanya untuk memudahkan membaca, tidak wajib */
```

Cara menghitungnya: ada 3 `#if` (1 di luar + 2 di dalam) → butuh 3 `#endif`.

### Rangkuman struktur

```text
#if / #ifdef / #ifndef   ← pembuka (wajib, 1 kali)
#elif                    ← opsional, boleh banyak
#else                    ← opsional, maksimal 1 kali, di bagian akhir
#endif                   ← penutup (wajib)
```

## Tambahan: Operator `defined`

```c
defined name
/* atau */
defined(name)
```

`defined` adalah operator yang dipakai **di dalam `#if` / `#elif`**. Hasilnya `1` jika `name` sudah didefinisikan dan `0` jika belum. Kelebihannya: kamu bisa **menggabungkan banyak kondisi dalam satu baris**, hal yang tidak bisa dilakukan `#ifdef` sendirian.

```c
#if defined(DEBUG) && defined(VERBOSE)
/* aktif hanya jika DEBUG dan VERBOSE sama-sama terdefinisi */
#endif

#if defined(_WIN32) || defined(__linux__)
/* aktif di Windows atau Linux */
#endif

#if !defined(NDEBUG)
/* aktif jika NDEBUG TIDAK terdefinisi */
#endif
```

Kesetaraan yang bagus untuk diingat:

| Bentuk singkat | Bentuk dengan `defined` |
|---|---|
| `#ifdef X` | `#if defined(X)` |
| `#ifndef X` | `#if !defined(X)` |

## Contoh Penggunaan

### 1. `#if`, `#elif`, `#else` sederhana

```c
#define DLIMIT 100

#if DLIMIT < 100
    /* Dijalankan jika DLIMIT di bawah 100 */
    dlimitLowHandler();
#elif DLIMIT > 200
    /* Dijalankan jika DLIMIT di atas 200 */
    dlimitHighHandler();
#else
    /* Dijalankan jika kedua kondisi di atas tidak terpenuhi */
    dlimitMidHandler();
#endif
```

Karena `DLIMIT` bernilai `100`: `100 < 100` salah, `100 > 200` juga salah, sehingga yang ikut di-compile adalah `dlimitMidHandler();`. Dua pemanggilan lainnya tidak pernah sampai ke compiler.

### 2. `#ifdef` dan kawan-kawannya

```c
#include <stdio.h>

#define DEBUG

/* Jika DEBUG terdefinisi, definisikan PRODUCTION. Jika tidak, definisikan DEPLOYMENT */
#ifdef DEBUG
#define PRODUCTION
#else
#define DEPLOYMENT
#endif

int main(void) {
    int Y = 0;
    for (int i = 0; i < 5; i++) {
        int y;
        y = 2 * i + 1;
#ifdef DEBUG
        /* Jika DEBUG terdefinisi, baris di bawah ikut di-compile sampai #endif */
        printf("f(%d): %d\n", i, y);
#endif
        Y = Y + y;
    }
    printf("Hasil: %d\n", Y);
    return 0;
}
```

Output saat `DEBUG` terdefinisi:

```text
f(0): 1
f(1): 3
f(2): 5
f(3): 7
f(4): 9
Hasil: 25
```

Jika baris `#define DEBUG` dihapus, hanya `Hasil: 25` yang tercetak. Perhatikan juga bahwa conditional directive bisa dipakai untuk men-`#define` hal lain (`PRODUCTION` dan `DEPLOYMENT` pada contoh ini). Di sinilah materi 11.2 dan 11.3 saling bertemu.

### 3. Penggunaan `defined`

```c
#define DEBUG

#ifdef DEBUG
doSomething();
#endif

#if defined(DEBUG)
doSomething();
#endif

/* Kedua blok di atas berperilaku sama */
```

### 4. Contoh nyata: kode lintas platform

```c
#include <stdio.h>

#if defined(_WIN32)
    #define CLEAR_CMD "cls"
#else
    #define CLEAR_CMD "clear"
#endif

int main(void) {
    printf("Perintah bersihkan layar di sistem ini: %s\n", CLEAR_CMD);
    return 0;
}
```

Satu source code yang sama bisa dipakai di Windows maupun Linux/macOS, dan preprocessor yang memilihkan bagian yang sesuai.

---

# Ringkasan Akhir Bab 11

## Alur pemahaman seluruh bab

```text
BAGIAN 1
11.1  Preprocessor = tahap olah teks SEBELUM compile; directive diawali #
          │
          ▼
11.2  #define → substitusi teks
          ├─ Symbolic constant : identifier  → replacement
          ├─ Macro             : identifier(args) → replacement
          └─ Operator khusus   : #  (jadi string)
                                 ## (gabung token)
                                 \  (sambung baris)
          │
          ▼
BAGIAN 2
11.3  Conditional compilation → kode dipilih berdasarkan kondisi
          └─ memanfaatkan macro/define dari 11.2 sebagai "saklar"
```

## Tabel ringkas directive

| Directive | Fungsi singkat |
|---|---|
| `#include` | Menyisipkan isi file lain |
| `#define` | Mendefinisikan symbolic constant atau macro |
| `#if expr` | Compile blok jika `expr` bukan nol |
| `#ifdef X` | Compile blok jika `X` sudah didefinisikan |
| `#ifndef X` | Compile blok jika `X` belum didefinisikan |
| `#elif expr` | "else if" untuk preprocessor |
| `#else` | "else" untuk preprocessor |
| `#endif` | Menutup rangkaian conditional |
| `defined(X)` | Operator pengecek definisi, dipakai di dalam `#if`/`#elif` |

## Hal-hal yang wajib diingat

1. Preprocessor hanya **mengganti teks**; ia tidak memahami tipe data dan tidak menghitung nilai.
2. Directive **tidak diakhiri titik koma**.
3. **Bungkus argumen dan ekspresi macro dengan kurung** untuk menghindari urutan operasi yang salah.
4. **Jangan beri spasi** antara nama macro dan `(` pada macro berargumen.
5. Hindari argumen berefek samping (misal `i++`) pada macro.
6. Operand `#` dan `##` **tidak di-expand** lebih dulu; gunakan macro perantara jika diperlukan.
7. Setiap `#if` / `#ifdef` / `#ifndef` **harus** punya `#endif`.
8. Gunakan `-D` pada command line untuk mengatur macro tanpa mengubah source code, dan `gcc -E` untuk melihat hasil preprocessing.

---

# Latihan Bagian 2

Coba kerjakan dulu sebelum membuka jawabannya.

**Soal 3.** Blok mana yang ikut di-compile?

```c
#define MODE 2

#if MODE == 1
    printf("Mode A");
#elif MODE == 2
    printf("Mode B");
#else
    printf("Mode lain");
#endif
```

<details>
<summary>Lihat jawaban</summary>

Karena `MODE` bernilai `2`, kondisi `MODE == 1` salah dan `MODE == 2` benar, sehingga hanya `printf("Mode B");` yang ikut di-compile. Outputnya: **Mode B**.

</details>

**Soal 4.** Berapa `#endif` yang dibutuhkan?

```c
#ifdef A
    #if B > 1
    /* ... */
    #else
    /* ... */
    #endif
#endif
```

<details>
<summary>Lihat jawaban</summary>

Kode di atas sudah memakai tepat **2 `#endif`**, karena ada 2 pembuka (`#ifdef A` dan `#if B > 1`). `#else` tidak membutuhkan `#endif` sendiri.

</details>

**Soal 5 (tantangan).** Buat include guard untuk header bernama `kalkulator.h`.

<details>
<summary>Lihat jawaban</summary>

```c
#ifndef KALKULATOR_H
#define KALKULATOR_H

/* deklarasi fungsi di sini */

#endif /* KALKULATOR_H */
```

</details>

---

*Selamat! Kalau kamu sudah paham alur dari 11.1 sampai 11.3, kamu punya fondasi yang kuat untuk membaca kode C yang penuh directive, termasuk header library dan proyek lintas platform. Belum yakin dengan salah satu bagian? Kamu bisa kembali ke [Bagian 1](Bab-11-Bagian-1-Pengantar-dan-Macro.md) kapan saja.*
