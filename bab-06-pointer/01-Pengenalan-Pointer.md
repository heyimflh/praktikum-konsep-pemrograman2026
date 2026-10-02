<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=3949ab&height=150&section=header&text=6.1%20Pengenalan%20Pointer&fontSize=35&fontColor=ffffff&animation=fadeIn&desc=Memahami%20Alamat%20Memori%20%26%20Operator%20Pointer&descSize=14&descAlignY=75)

[⬅️ Overview Bab 6](Bab6-Overview.md) &nbsp;•&nbsp; [📋 Daftar Materi](../Daftar_Materi.md) &nbsp;•&nbsp; [Modul 6.2: Pointer dan Array ➡️](02-Pointer-dan-Array.md)

---

</div>

## 🎯 Tujuan Pembelajaran

Setelah menyelesaikan modul ini, kamu diharapkan mampu:

1. Menjelaskan konsep alamat memori dan perbedaannya dengan nilai variabel.
2. Mendeklarasikan pointer dan menggunakan operator `&` (*referencing*) dan `*` (*dereferencing*).
3. Membedakan tiga makna `*` dalam bahasa C (perkalian, deklarasi pointer, dereference).
4. Menerapkan `const` pada pointer dengan benar sesuai kebutuhan.

---

## 💡 Apa itu Pointer?

**Pointer** adalah variabel yang nilainya bukan data biasa (angka, huruf, dsb.), melainkan sebuah **alamat memori** yang menunjuk ke lokasi variabel lain di RAM.

> [!TIP]
> **Analogi Sederhana:**
> Bayangkan variabel `health` adalah sebuah rumah yang menyimpan uang sejumlah 100.
> Alamat rumah tersebut adalah "Jalan C no. 4" (`0x004`).
> **Pointer** adalah secarik kertas yang menuliskan alamat "Jalan C no. 4".
> Jika kamu mengikuti alamat di kertas itu, kamu akan sampai ke rumah `health`!

**Mengapa pointer penting?** Pointer memungkinkan kita untuk **mengakses dan mengubah** nilai variabel yang **tidak berada pada *scope* yang sama**, membangun struktur data dinamis, dan mengefisienkan pengiriman data ke fungsi.

---

## 🧱 Memori itu Seperti Apa?

Saat kamu menulis `int health = 100;`, komputer melakukan tiga hal sekaligus:
1. Memesan ruang di RAM (sebesar `sizeof(int)` = 4 byte)
2. Memberikan alamat pada ruang tersebut (misal: `0xFFBC4`)
3. Mengisi ruang itu dengan nilai `100`

Gambaran sederhana di memori:

```
┌──────────────┬──────────────┬───────────────┐
│ Nama Variabel│   Alamat     │     Nilai      │
├──────────────┼──────────────┼───────────────┤
│   health     │  0xFFBC4     │      100       │
│   health_ptr │  0xFFBC8     │  0xFFBC4 (!)  │
└──────────────┴──────────────┴───────────────┘
```

> [!NOTE]
> Variabel `health_ptr` juga **memiliki alamat sendiri** (`0xFFBC8`), karena ia pun merupakan variabel yang disimpan di RAM. Isinya adalah alamat `health` (`0xFFBC4`), bukan nilai `100`.

### Mencetak Alamat dengan `%p`

```c
#include <stdio.h>

int main(void) {
    int health = 100;
    int *health_ptr = &health;

    printf("Nilai health      : %d\n",  health);
    printf("Alamat health     : %p\n",  (void *)&health);
    printf("Nilai health_ptr  : %p\n",  (void *)health_ptr);
    printf("*health_ptr       : %d\n",  *health_ptr);
    printf("sizeof(health)    : %zu\n", sizeof(health));
    printf("sizeof(health_ptr): %zu\n", sizeof(health_ptr));

    return 0;
}
```

**Output (alamat bisa berbeda di setiap eksekusi/komputer):**
```text
Nilai health      : 100
Alamat health     : 000000D6249FFBC4
Nilai health_ptr  : 000000D6249FFBC4
*health_ptr       : 100
sizeof(health)    : 4
sizeof(health_ptr): 8
```

> [!IMPORTANT]
> Gunakan selalu `(void *)` saat mencetak pointer dengan `%p`. Ukuran pointer **sama untuk semua tipe** pada satu platform (8 byte di sistem 64-bit), karena pointer hanya menyimpan alamat, bukan data.

---

## 🏗️ Deklarasi Pointer

Pointer dideklarasikan dengan menambahkan tanda asterisk `*` di antara tipe dan nama variabel:

```c
<Tipe_Data> *<Nama_Variabel>;
```

Contoh:
```c
int   *health_ptr;   /* pointer ke variabel bertipe int   */
float *ratio_ptr;    /* pointer ke variabel bertipe float */
char  *nama_ptr;     /* pointer ke variabel bertipe char  */
```

> [!WARNING]
> **Hati-hati Saat Deklarasi Sekaligus!**
> ```c
> int *health_ptr, ammo_ptr;   /* ❌ ammo_ptr adalah int biasa, BUKAN pointer! */
> ```
> Penulisan yang benar jika ingin keduanya pointer:
> ```c
> int *health_ptr, *ammo_ptr;  /* ✅ Keduanya pointer ke int */
> ```

---

## ✨ Dua Makna `*` dalam C

> [!IMPORTANT]
> **Callout: Dua Makna `*`**
>
> Simbol `*` memiliki arti **berbeda** tergantung konteksnya:
>
> | Konteks | Contoh | Arti |
> |---------|--------|------|
> | **Deklarasi** | `int *health_ptr;` | "*`health_ptr` adalah pointer* ke `int`" |
> | **Ekspresi** | `*health_ptr = 75;` | "Akses nilai di alamat yang ditunjuk `health_ptr`" (*dereference*) |
>
> ```c
> int health = 100;
> int *p = &health;   /* ← * di sini = DEKLARASI (p adalah pointer) */
> *p = 75;            /* ← * di sini = DEREFERENCE (tulis ke alamat yg ditunjuk p) */
> printf("%d\n", *p); /* ← * di sini = DEREFERENCE (baca dari alamat yg ditunjuk p) */
> ```
> `*` sebagai perkalian (`a * b`) adalah makna ketiga yang terpisah dari pointer sama sekali.

---

## 🌱 Inisialisasi Pointer (Operator `&`)

Setelah dideklarasikan, pointer masih kosong (atau berisi alamat acak — berbahaya!). Arahkan pointer ke sebuah variabel menggunakan operator *referencing* (`&`), yang berarti **"ambil alamat memori dari"**.

```c
int health = 100;
int *health_ptr;          /* pointer belum diarahkan — berbahaya! */

health_ptr = &health;     /* sekarang health_ptr menyimpan alamat health */
```

---

## 📚 Kamu Sudah Memakai `&` Sejak Lama!

Jika kamu pernah menggunakan `scanf`, kamu sudah memakai operator `&` tanpa disadari:

```c
int umur;
scanf("%d", &umur);   /* &umur = "kirim ALAMAT variabel umur ke scanf" */
```

Fungsi `scanf` membutuhkan **alamat** (bukan nilai) agar bisa **menulis** hasil input langsung ke variabel `umur` di memori. Inilah alasan mengapa melupakan `&` pada `scanf` adalah salah satu bug paling umum!

---

## 🔍 Membaca & Mengubah Data via Pointer (Operator `*`)

Nilai variabel yang ditunjuk pointer dapat diakses dengan operator *dereferencing* (`*`), yang berarti **"ambil isi dari alamat memori ini"**.

### Membaca Data:
```c
int health = 100;
int *health_ptr = &health;

printf("Health: %d\n", *health_ptr);  /* Output: Health: 100 */

health = 50;   /* ubah via variabel asli */
printf("Health: %d\n", *health_ptr);  /* Output: Health: 50 (pointer ikut!) */
```

### Mengubah Data (*Write*):
```c
int health = 100;
int *health_ptr = &health;

*health_ptr = 75;  /* ubah isi rumah via pointer */

printf("Health akhir: %d\n", health);  /* Output: Health akhir: 75 */
```

---

## 📊 Tracing Table: Langkah Demi Langkah

Mari kita telusuri program berikut baris per baris:

```c
int health     = 100;       /* baris 1 */
int *health_ptr = &health;  /* baris 2 */
*health_ptr = 75;           /* baris 3 */
health = health + 5;        /* baris 4 */
```

| Baris | Aksi | `health` | `health_ptr` | `*health_ptr` |
|:-----:|------|:--------:|:------------:|:-------------:|
| 1 | Buat `health`, isi 100 | `100` | — | — |
| 2 | `health_ptr` → alamat `health` | `100` | `&health` | `100` |
| 3 | Tulis 75 ke alamat yg ditunjuk | `75` | `&health` | `75` |
| 4 | Tambah 5 ke `health` langsung | `80` | `&health` | `80` |

> [!NOTE]
> Baris 3 dan 4 keduanya memodifikasi **sel memori yang sama**, hanya melalui cara yang berbeda. Perubahan pada satu langsung terlihat dari yang lain.

---

## ⚠️ Tipe Pointer Harus Sesuai

Pointer bertipe `int *` hanya boleh menunjuk ke variabel `int`, pointer `float *` hanya ke `float`, dst. Tipe penting karena menentukan **berapa byte yang dibaca saat dereference**.

```c
#include <stdio.h>

int main(void) {
    int   health = 100;
    float ratio  = 0.85f;

    int   *hp = &health;  /* ✅ int*    menunjuk ke int   */
    float *rp = &ratio;   /* ✅ float*  menunjuk ke float */

    /* int *wrong = &ratio; */  /* ❌ COMPILE ERROR: tipe tidak cocok */

    printf("Health via pointer: %d\n",   *hp);
    printf("Ratio  via pointer: %.2f\n", *rp);
    return 0;
}
```

---

## 🛑 NULL Pointer

Sangat berbahaya membiarkan pointer tidak diinisialisasi — ia bisa menunjuk ke lokasi memori acak dan menyebabkan program *crash*.

Jika pointer belum digunakan, **selalu inisialisasi dengan `NULL`**.

```c
#include <stdio.h>

int main(void) {
    int health = 100;
    int *health_ptr = NULL;  /* pointer diset kosong dengan aman */

    int choice;
    printf("Pilih 1 untuk main: ");
    scanf("%d", &choice);

    if (choice == 1) {
        health_ptr = &health;
    }

    /* SELALU cek sebelum dereference! */
    if (health_ptr != NULL) {
        *health_ptr = 85;
        printf("Pointer berhasil digunakan! Health = %d\n", health);
    } else {
        printf("Pointer masih kosong (NULL)!\n");
    }

    return 0;
}
```

---

## 🛡️ Pointer dan `const`

Kata kunci `const` dapat digunakan bersama pointer dalam **tiga bentuk** yang berbeda. Cara mudah membacanya: **baca deklarasi dari kanan ke kiri**.

| Bentuk | Deklarasi | Pointer boleh diarahkan ulang? | Data boleh diubah? |
|--------|-----------|:---:|:---:|
| *Pointer to constant* | `const int *p` | ✅ Ya | ❌ Tidak |
| *Constant pointer* | `int *const p` | ❌ Tidak | ✅ Ya |
| *Constant pointer to constant* | `const int *const p` | ❌ Tidak | ❌ Tidak |

> [!TIP]
> **Tips Baca Kanan ke Kiri:**
> - `const int *p` → "p adalah pointer (*) ke int yang const" → data read-only
> - `int *const p` → "p adalah const pointer (*const) ke int" → pointer terkunci
> - `const int *const p` → "p adalah const pointer ke int yang const" → keduanya terkunci

```c
#include <stdio.h>

int main(void) {
    int health = 100;
    int mana   = 50;

    /* 1. const int *p  — Pointer to Constant */
    const int *p1 = &health;
    printf("p1 -> %d\n", *p1);    /* ✅ Membaca: OK */
    p1 = &mana;                    /* ✅ Arahkan ulang: OK */
    /* *p1 = 999; */               /* ❌ COMPILE ERROR: data read-only */

    /* 2. int *const p  — Constant Pointer */
    int *const p2 = &health;
    *p2 = 200;                     /* ✅ Ubah data: OK */
    printf("p2 -> %d\n", *p2);
    /* p2 = &mana; */              /* ❌ COMPILE ERROR: pointer terkunci */

    /* 3. const int *const p  — Constant Pointer to Constant */
    const int *const p3 = &health;
    printf("p3 -> %d\n", *p3);    /* ✅ Hanya membaca */
    /* *p3 = 999;  */              /* ❌ COMPILE ERROR: data read-only */
    /* p3  = &mana; */             /* ❌ COMPILE ERROR: pointer terkunci */

    return 0;
}
```

> [!NOTE]
> Bentuk `const int *p` (*pointer to constant*) sangat berguna sebagai parameter fungsi yang **hanya boleh membaca** data, tidak mengubahnya. Ini adalah praktik yang baik untuk fungsi "pembaca".

---

## 📝 Ringkasan

- **Pointer** menyimpan **alamat memori** variabel lain, bukan nilai langsung.
- Operator `&` digunakan untuk mengambil alamat variabel (*referencing*).
- Operator `*` digunakan untuk mengakses nilai di alamat yang ditunjuk (*dereferencing*).
- `*` punya dua makna berbeda: di deklarasi berarti "ini pointer", di ekspresi berarti "dereference".
- **Tipe pointer harus sesuai** dengan tipe variabel yang ditunjuk.
- Selalu inisialisasi pointer ke `NULL` jika belum ada tujuan.
- `const` pada pointer punya tiga bentuk berbeda — baca deklarasi dari kanan ke kiri.

---

## 🏋️ Latihan Singkat

Kerjakan latihan berikut. Jawaban lengkap ada di [Modul 6.6: Latihan dan Rangkuman](06-Latihan-dan-Rangkuman.md).

1. **(Tracing)** Telusuri kode berikut — apa nilai `x` dan `*p` di akhir?
   ```c
   int x = 5;
   int *p = &x;
   *p = *p + 3;
   x = x * 2;
   ```

2. **(Tebak Output)** Apa yang dicetak oleh kode ini?
   ```c
   int a = 10, b = 20;
   int *p = &a;
   p = &b;
   printf("%d\n", *p);
   ```

3. **(Cari Bug)** Apa yang salah dengan kode berikut?
   ```c
   int *ptr;
   *ptr = 100;
   printf("%d\n", *ptr);
   ```

4. **(Buat Program)** Tulis program yang minta user input satu bilangan bulat, lalu cetak nilai dan alamatnya menggunakan pointer.

---

<div align="center">

[⬅️ Sebelumnya: Overview Bab 6](Bab6-Overview.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 6.2: Pointer dan Array ➡️](02-Pointer-dan-Array.md)

</div>
