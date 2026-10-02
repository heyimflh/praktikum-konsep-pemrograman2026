<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=1565c0&height=150&section=header&text=6.6%20Latihan%20%26%20Rangkuman&fontSize=33&fontColor=ffffff&animation=fadeIn&desc=Cheatsheet%2C%20Soal%20Bertingkat%2C%20dan%20Mini-Project&descSize=14&descAlignY=75)

[⬅️ Modul 6.5: Debugging](05-Kesalahan-Umum-dan-Debugging.md) &nbsp;•&nbsp; [Overview Bab 6](Bab6-Overview.md) &nbsp;•&nbsp; [📋 Daftar Materi](../Daftar_Materi.md)

---

</div>

## 📖 Cheatsheet Pointer Bab 6

### Simbol dan Operator

| Simbol | Konteks | Arti |
|--------|---------|------|
| `&` | `&x` | Ambil **alamat** variabel `x` |
| `*` | `int *p` (deklarasi) | Deklarasi: "p adalah **pointer** ke int" |
| `*` | `*p` (ekspresi) | *Dereference*: akses nilai di alamat yang ditunjuk `p` |
| `[]` | `arr[i]` | Akses elemen; setara dengan `*(arr + i)` |
| `->` | `ptr->field` | Akses field struct via pointer *(dibahas di bab struct)* |
| `NULL` | `int *p = NULL` | Nilai "tidak menunjuk ke mana-mana" — aman untuk cek |

### Fungsi Manajemen Memori

| Fungsi | Kapan Digunakan | Inisialisasi ke 0? |
|--------|----------------|:------------------:|
| `malloc(n)` | Pesan `n` byte, isi tidak terdefinisi | ❌ |
| `calloc(count, size)` | Pesan `count × size` byte, isi diset 0 | ✅ |
| `realloc(ptr, new_size)` | Ubah ukuran blok; gunakan pointer sementara | ❌ |
| `free(ptr)` | Bebaskan blok; set `ptr = NULL` sesudahnya | — |

### Pola Aman DMA

```c
/* Alokasi + cek */
int *p = malloc(sizeof *p);
if (p == NULL) { /* handle error */ }

/* Pakai */

/* Bebaskan */
free(p);
p = NULL;
```

```c
/* Realloc aman */
int *temp = realloc(arr, new_n * sizeof *temp);
if (temp == NULL) { free(arr); return 1; }
arr = temp;
```

### Const Pointer — Referensi Cepat

| Bentuk | Data | Pointer |
|--------|:----:|:-------:|
| `const int *p` | Read-only | Bisa arahkan ulang |
| `int *const p` | Bisa diubah | Terkunci |
| `const int *const p` | Read-only | Terkunci |

---

## 🏋️ Level 1: Tebak Output / Tracing

*Jawab setiap soal dengan menuliskan tabel memori langkah demi langkah.*

**Soal 1.1**
```c
int a = 5;
int *p = &a;
*p = *p * 2;
printf("%d %d\n", a, *p);
```

**Soal 1.2**
```c
int x = 10, y = 20;
int *p = &x;
int *q = &y;
*p = *p + *q;
q = p;
printf("%d %d %d\n", x, y, *q);
```

**Soal 1.3**
```c
int arr[] = {3, 6, 9, 12};
int *p = arr + 1;
printf("%d %d %d\n", *p, *(p+1), arr[3]);
p++;
printf("%d\n", *p);
```

**Soal 1.4**
```c
int a = 1, b = 2, c = 3;
int *ptr[3] = {&a, &b, &c};
*ptr[1] = 20;
printf("%d %d %d\n", a, b, c);
```

---

## 🔍 Level 2: Cari dan Perbaiki Bug

*Setiap soal berisi 1-2 bug pointer. Identifikasi bug-nya, jelaskan kenapa itu bug, dan tulis versi yang diperbaiki.*

**Soal 2.1**
```c
#include <stdio.h>
int main(void) {
    int *p;
    *p = 100;
    printf("%d\n", *p);
    return 0;
}
```

**Soal 2.2**
```c
#include <stdio.h>
#include <stdlib.h>
int main(void) {
    int n = 5;
    int *arr = malloc(n * sizeof(arr));
    for (int i = 0; i <= n; i++) {
        arr[i] = i * 2;
    }
    free(arr);
    return 0;
}
```

**Soal 2.3**
```c
#include <stdio.h>
#include <stdlib.h>
int *buat_array(int n) {
    int arr[n];
    for (int i = 0; i < n; i++) arr[i] = i;
    return arr;
}
int main(void) {
    int *hasil = buat_array(5);
    printf("%d\n", hasil[2]);
    return 0;
}
```

**Soal 2.4**
```c
#include <stdio.h>
#include <stdlib.h>
int main(void) {
    int *p = malloc(sizeof(int));
    *p = 42;
    free(p);
    free(p);
    return 0;
}
```

---

## 💻 Level 3: Buat Program

**Soal 3.1 — Swap Tiga Variabel**  
Tulis fungsi `void rotasi(int *a, int *b, int *c)` yang merotasi nilai tiga variabel: nilai `a` → `b`, `b` → `c`, `c` → `a`. Uji di `main` dengan mencetak nilai sebelum dan sesudah.

**Soal 3.2 — Balik Array dengan Pointer**  
Tulis fungsi `void balik(int *arr, int n)` yang membalik urutan elemen array *in-place* menggunakan aritmatika pointer (tanpa `arr[i]`). Uji dengan array `{1, 2, 3, 4, 5}`.

**Soal 3.3 — Hitung Panjang String dengan Pointer**  
Tulis fungsi `int panjang_string(const char *s)` yang menghitung panjang string **tanpa menggunakan `strlen`**, hanya menggunakan pointer. Uji dengan beberapa string berbeda.

**Soal 3.4 — Array Dinamis + Rata-Rata**  
Tulis program lengkap yang:
1. Meminta input `n` dari pengguna.
2. Mengalokasikan array `int` berukuran `n` secara dinamis (dengan cek NULL).
3. Meminta `n` nilai dari pengguna.
4. Menghitung dan mencetak nilai minimum, maksimum, dan rata-rata.
5. Membebaskan memori.

**Soal 3.5 — Hitung Kemunculan Karakter**  
Tulis fungsi `int hitung_karakter(const char *str, char target)` yang menghitung berapa kali karakter `target` muncul dalam string `str`. Gunakan pointer, bukan indeks.

---

## 🏆 Mini-Project Akhir Bab: Manajemen Daftar Nilai Mahasiswa

### Deskripsi

Buat program **Manajemen Daftar Nilai** menggunakan array dinamis. Program harus memiliki menu interaktif:

```
=== Manajemen Nilai Mahasiswa ===
1. Tambah Mahasiswa
2. Tampilkan Semua
3. Hitung Statistik (Min, Max, Rata-rata)
4. Hapus Mahasiswa Terakhir
5. Keluar
```

### Spesifikasi Teknis

- Simpan nama (`char *`) dan nilai (`int`) setiap mahasiswa.
- Gunakan **array dinamis** (`realloc`) agar bisa tumbuh sesuai input.
- Data mahasiswa disimpan sebagai array of `struct`:
  ```c
  typedef struct {
      char *nama;   /* alokasi dinamis dengan malloc + strlen */
      int   nilai;
  } Mahasiswa;
  ```
- Fungsi yang wajib diimplementasikan:
  - `tambah_mahasiswa(...)` — tambah ke akhir array, realloc jika perlu
  - `tampilkan_semua(...)` — cetak tabel nama dan nilai
  - `hitung_statistik(...)` — min, max, rata-rata
  - `hapus_terakhir(...)` — hapus elemen terakhir (free nama-nya!)
  - `bebaskan_semua(...)` — bebaskan semua memori sebelum keluar

### Kriteria Penilaian

| Kriteria | Poin |
|----------|:----:|
| Program berhasil dikompilasi tanpa warning (`-Wall -Wextra`) | 15 |
| Semua operasi menu berfungsi dengan benar | 30 |
| Pengecekan NULL pada setiap malloc/realloc | 15 |
| Tidak ada memory leak (setiap malloc punya free) | 20 |
| Pointer di-set NULL setelah free | 5 |
| Struktur kode bersih (fungsi terpisah, nama deskriptif) | 15 |
| **Total** | **100** |

> [!TIP]
> Kerjakan secara bertahap: pertama buat menu dan struct, lalu implementasikan `tambah` dan `tampilkan`, kemudian tambahkan `statistik` dan `hapus`. Pastikan tidak ada memory leak di setiap langkah sebelum melanjutkan.

---

## 🔑 Kunci Jawaban

<details>
<summary><strong>Klik untuk Membuka Kunci Jawaban Level 1</strong></summary>

### Kunci 1.1
```
Tabel Memori:
Baris     | a  | *p
----------|----|----- 
int a = 5 | 5  | —
int *p=&a | 5  | 5
*p = *p*2 | 10 | 10

Output: 10 10
```

### Kunci 1.2
```
Tabel Memori:
Aksi          | x  | y  | p     | q     | *q
--------------|----|----|-------|-------|----
x=10, y=20    | 10 | 20 | —     | —     | —
p=&x, q=&y   | 10 | 20 | &x    | &y    | 20
*p = *p + *q  | 30 | 20 | &x    | &y    | 20
q = p         | 30 | 20 | &x    | &x    | 30

Output: 30 20 30
```

### Kunci 1.3
```
arr   = {3, 6, 9, 12}, indeks [0..3]
p     = arr + 1  → menunjuk ke arr[1] = 6

*p    = 6
*(p+1)= arr[2] = 9
arr[3]= 12

Output baris 1: 6 9 12

p++ → p menunjuk ke arr[2] = 9
Output baris 2: 9
```

### Kunci 1.4
```
ptr[0]=&a, ptr[1]=&b, ptr[2]=&c
*ptr[1] = 20  → b = 20

Output: 1 20 3
```

</details>

<details>
<summary><strong>Klik untuk Membuka Kunci Jawaban Level 2</strong></summary>

### Kunci 2.1
**Bug:** *Uninitialized (wild) pointer* — `p` dideklarasikan tapi tidak diarahkan ke variabel manapun. Dereference `*p = 100` adalah *undefined behavior*.

**Perbaikan:**
```c
int x = 0;          /* variabel target */
int *p = &x;        /* arahkan pointer ke variabel yang valid */
*p = 100;
printf("%d\n", *p);
```

### Kunci 2.2
**Bug 1:** `malloc(n * sizeof(arr))` — `sizeof(arr)` = ukuran **pointer** (8 byte), bukan `sizeof(int)`. Harusnya `sizeof *arr`.

**Bug 2:** Loop `i <= n` — mengakses `arr[5]` yang di luar batas (indeks valid 0–4). Buffer overflow!

**Perbaikan:**
```c
int *arr = malloc(n * sizeof *arr);   /* perbaiki ukuran */
if (arr == NULL) return 1;
for (int i = 0; i < n; i++) {        /* < bukan <= */
    arr[i] = i * 2;
}
free(arr);
```

### Kunci 2.3
**Bug:** *Dangling pointer* — `arr` adalah VLA di *stack*. Begitu fungsi `buat_array` selesai, memori `arr` hancur. Mengembalikan `arr` menghasilkan pointer ke memori tidak valid.

**Perbaikan:**
```c
#include <stdlib.h>
int *buat_array(int n) {
    int *arr = malloc(n * sizeof *arr);   /* alokasi di heap */
    if (arr == NULL) return NULL;
    for (int i = 0; i < n; i++) arr[i] = i;
    return arr;   /* heap tetap hidup setelah fungsi selesai */
}
int main(void) {
    int *hasil = buat_array(5);
    if (hasil == NULL) return 1;
    printf("%d\n", hasil[2]);
    free(hasil);   /* caller bertanggung jawab membebaskan */
    return 0;
}
```

### Kunci 2.4
**Bug:** *Double free* — `free(p)` dipanggil dua kali. Setelah `free` pertama, blok memori sudah tidak valid. `free` kedua adalah *undefined behavior* (*heap corruption*).

**Perbaikan:**
```c
int *p = malloc(sizeof(int));
*p = 42;
free(p);
p = NULL;   /* set NULL agar free kedua tidak berbahaya */
/* free(p) kedua sekarang aman: free(NULL) tidak melakukan apa-apa */
```

</details>

<details>
<summary><strong>Klik untuk Membuka Kunci Jawaban Level 3</strong></summary>

### Kunci 3.1 — Rotasi Tiga Variabel
```c
#include <stdio.h>

void rotasi(int *a, int *b, int *c) {
    int temp = *a;
    *a = *c;
    *c = *b;
    *b = temp;
}

int main(void) {
    int x = 1, y = 2, z = 3;
    printf("Sebelum: x=%d y=%d z=%d\n", x, y, z);
    rotasi(&x, &y, &z);
    printf("Sesudah: x=%d y=%d z=%d\n", x, y, z);
    return 0;
}
```
Output: `Sesudah: x=3 y=1 z=2`

### Kunci 3.2 — Balik Array dengan Pointer
```c
#include <stdio.h>

void balik(int *arr, int n) {
    int *kiri  = arr;
    int *kanan = arr + n - 1;
    while (kiri < kanan) {
        int temp = *kiri;
        *kiri  = *kanan;
        *kanan = temp;
        kiri++;
        kanan--;
    }
}

int main(void) {
    int data[] = {1, 2, 3, 4, 5};
    int n = 5;
    balik(data, n);
    for (int i = 0; i < n; i++) printf("%d ", data[i]);
    printf("\n");
    return 0;
}
```
Output: `5 4 3 2 1`

### Kunci 3.3 — Panjang String dengan Pointer
```c
#include <stdio.h>

int panjang_string(const char *s) {
    const char *p = s;
    while (*p != '\0') p++;
    return (int)(p - s);
}

int main(void) {
    printf("%d\n", panjang_string("Hello"));        /* 5 */
    printf("%d\n", panjang_string("Bab 6 Pointer")); /* 13 */
    printf("%d\n", panjang_string(""));              /* 0 */
    return 0;
}
```

### Kunci 3.4 — Array Dinamis + Statistik
```c
#include <stdio.h>
#include <stdlib.h>

void hitung_min_max(const int *arr, int n, int *min, int *max, float *rata) {
    *min  = arr[0];
    *max  = arr[0];
    int jumlah = 0;
    for (int i = 0; i < n; i++) {
        if (arr[i] < *min) *min = arr[i];
        if (arr[i] > *max) *max = arr[i];
        jumlah += arr[i];
    }
    *rata = (float)jumlah / n;
}

int main(void) {
    int n;
    printf("Masukkan jumlah nilai: ");
    scanf("%d", &n);

    int *arr = malloc(n * sizeof *arr);
    if (arr == NULL) { fprintf(stderr, "Gagal alokasi!\n"); return 1; }

    for (int i = 0; i < n; i++) {
        printf("Nilai ke-%d: ", i + 1);
        scanf("%d", &arr[i]);
    }

    int mn, mx;
    float rata;
    hitung_min_max(arr, n, &mn, &mx, &rata);

    printf("Minimum : %d\n", mn);
    printf("Maksimum: %d\n", mx);
    printf("Rata-rata: %.2f\n", rata);

    free(arr);
    arr = NULL;
    return 0;
}
```

### Kunci 3.5 — Hitung Karakter
```c
#include <stdio.h>

int hitung_karakter(const char *str, char target) {
    int count = 0;
    for (const char *p = str; *p != '\0'; p++) {
        if (*p == target) count++;
    }
    return count;
}

int main(void) {
    printf("%d\n", hitung_karakter("programming", 'g')); /* 2 */
    printf("%d\n", hitung_karakter("pointer", 'p'));     /* 1 */
    printf("%d\n", hitung_karakter("hello", 'z'));       /* 0 */
    return 0;
}
```

</details>

---

## 🎓 Selamat Menyelesaikan Bab 6!

Kamu telah mempelajari salah satu konsep paling fundamental dan paling kuat dalam bahasa C. Pointer bukan hanya fitur bahasa — mereka adalah jendela untuk memahami **bagaimana komputer benar-benar bekerja** di tingkat memori.

> [!TIP]
> **Langkah Selanjutnya:**
> - Kerjakan Mini-Project dengan serius — ini mensimulasikan kode nyata.
> - Coba jalankan program dengan `-fsanitize=address` dan pastikan tidak ada laporan error.
> - Baca kembali [Modul 6.5](05-Kesalahan-Umum-dan-Debugging.md) jika menemui bug yang sulit ditemukan.

---

<div align="center">

[⬅️ Sebelumnya: 6.5 Debugging](05-Kesalahan-Umum-dan-Debugging.md) &nbsp;&nbsp;|&nbsp;&nbsp; [📋 Kembali ke Daftar Materi Utama](../Daftar_Materi.md)

</div>
