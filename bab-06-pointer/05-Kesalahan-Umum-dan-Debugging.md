<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=e65100&height=150&section=header&text=6.5%20Kesalahan%20Umum%20%26%20Debugging&fontSize=28&fontColor=ffffff&animation=fadeIn&desc=Mendeteksi%20dan%20Memperbaiki%20Bug%20Pointer&descSize=14&descAlignY=75)

[⬅️ Modul 6.4: DMA](04-Dynamic-Memory-Allocation.md) &nbsp;•&nbsp; [Overview Bab 6](Bab6-Overview.md) &nbsp;•&nbsp; [Modul 6.6: Latihan ➡️](06-Latihan-dan-Rangkuman.md)

---

</div>

## 🎯 Tujuan Pembelajaran

Setelah menyelesaikan modul ini, kamu diharapkan mampu:

1. Mengidentifikasi dan menjelaskan 9+ jenis bug umum yang berkaitan dengan pointer.
2. Mengompilasi program dengan flag debugging yang tepat.
3. Membaca dan menginterpretasi laporan AddressSanitizer (ASan).
4. Menerapkan checklist sebelum mengumpulkan tugas untuk meminimalkan bug.

---

## 🐛 Tabel Kesalahan Umum Pointer

| # | Nama Bug | Contoh Kode | Gejala | Cara Memperbaiki |
|:-:|----------|-------------|--------|-----------------|
| 1 | **Uninitialized / Wild Pointer** | `int *p; *p = 5;` | Crash acak, data korup | Selalu inisialisasi: `int *p = NULL;` |
| 2 | **Dereference NULL** | `int *p = NULL; *p = 5;` | Segmentation fault | Cek `if (p != NULL)` sebelum dereference |
| 3 | **Dangling Pointer** | Return `&local_var` dari fungsi | Nilai acak / crash | Jangan return alamat lokal; gunakan `malloc` atau terima pointer sebagai parameter |
| 4 | **Use-After-Free** | `free(p); printf("%d", *p);` | Nilai acak / crash | Set `p = NULL` langsung setelah `free(p)` |
| 5 | **Double Free** | `free(p); free(p);` | Crash / *heap corruption* | Pastikan setiap blok di-`free` tepat sekali; set `p = NULL` setelah `free` |
| 6 | **Memory Leak** | `malloc` tanpa pasangan `free` | RAM terus naik; program lambat | Setiap `malloc`/`calloc` harus punya `free` yang sesuai |
| 7 | **Buffer Overflow / Out-of-Bounds** | `int a[3]; a[5] = 1;` | Data korup, crash | Selalu validasi indeks: `0 ≤ i < n` |
| 8 | **Ukuran `malloc` Salah** | `int *p = malloc(sizeof(p));` | Memori kurang (4/8 byte bukan ukuran int) | Gunakan `sizeof *p` (bukan `sizeof(p)`) |
| 9 | **Lupa `&` pada `scanf`** | `scanf("%d", x);` | *Undefined behavior*, crash | Selalu `scanf("%d", &x)` |

### Detail Bug yang Sering Membingungkan Pemula

#### Bug 8: Salah Ukuran `malloc`

```c
int *p;

/* ❌ SALAH: sizeof(p) = ukuran pointer (8 byte di 64-bit),
   bukan ukuran int! */
p = malloc(sizeof(p));

/* ✅ BENAR: sizeof(*p) = ukuran yang ditunjuk pointer */
p = malloc(sizeof *p);
```

#### Bug 1 vs Bug 2: Wild Pointer vs NULL Pointer

```c
/* Wild pointer: menunjuk ke alamat ACAK — sangat berbahaya */
int *wild;
*wild = 5;        /* ❌ bisa merusak memori program mana saja */

/* NULL pointer: menunjuk ke alamat 0 — "aman" karena OS proteksi */
int *safe = NULL;
*safe = 5;        /* ❌ segfault yang dapat diprediksi dan di-debug */
```

> [!TIP]
> Selalu inisialisasi pointer ke `NULL`. *Segfault* dari dereference NULL **jauh lebih mudah** di-debug daripada *silent data corruption* dari *wild pointer*.

---

## 🔧 Alat Bantu Debugging

### 1. Flag Kompilasi Dasar: `-Wall -Wextra -g`

Selalu kompilasi dengan flag ini selama development:

```bash
gcc -Wall -Wextra -std=c99 -g program.c -o program
```

| Flag | Fungsi |
|------|--------|
| `-Wall` | Aktifkan peringatan penting |
| `-Wextra` | Peringatan tambahan yang lebih ketat |
| `-std=c99` | Gunakan standar C99 |
| `-g` | Sisipkan informasi debug (nomor baris, nama variabel) |

**Contoh: Compiler memperingatkan *dangling pointer*:**

```c
/* program_dangling.c */
#include <stdio.h>
int *buat_nilai(void) {
    int x = 42;
    return &x;   /* ← compiler akan memperingatkan ini */
}
int main(void) {
    int *p = buat_nilai();
    printf("%d\n", *p);
    return 0;
}
```

```bash
$ gcc -Wall -Wextra -std=c99 program_dangling.c -o out
program_dangling.c:4:12: warning: function returns address of local variable [-Wreturn-local-addr]
     return &x;
            ^~
```

**Jangan abaikan peringatan dari compiler!** Peringatan sering kali menunjuk langsung ke bug.

---

### 2. AddressSanitizer (ASan): Deteksi Bug Memori Saat *Runtime*

ASan adalah alat yang dibangun ke dalam GCC/Clang. Ia mendeteksi secara otomatis:  
*use-after-free*, *heap buffer overflow*, *stack buffer overflow*, *memory leak*, dll.

**Cara mengaktifkan:**

```bash
gcc -Wall -Wextra -std=c99 -g -fsanitize=address,undefined program.c -o program
./program
```

#### Contoh Langkah Demi Langkah

**Program bermasalah (`bug_leak.c`):**

```c
/* bug_leak.c — program dengan memory leak dan use-after-free */
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int *arr = malloc(5 * sizeof(int));
    /* lupa cek NULL — lewati untuk contoh singkat */
    
    for (int i = 0; i < 5; i++) arr[i] = i * 10;
    
    free(arr);
    
    /* Bug 1: use-after-free */
    printf("arr[2] setelah free: %d\n", arr[2]);
    
    /* Bug 2: memory leak — malloc ini tidak punya free */
    int *leak = malloc(100 * sizeof(int));
    (void)leak;   /* diam-diam tidak dipakai, tidak di-free */
    
    return 0;
}
```

**Langkah 1: Kompilasi dengan ASan:**

```bash
gcc -Wall -Wextra -std=c99 -g -fsanitize=address,undefined bug_leak.c -o bug_leak
```

**Langkah 2: Jalankan:**

```bash
./bug_leak
```

**Langkah 3: Baca laporan ASan** *(output bersifat ilustrasi — format asli bervariasi antar platform/versi GCC)*:

```text
=================================================================
==12345==ERROR: AddressSanitizer: heap-use-after-free on address 0x...
READ of size 4 at 0x... thread T0
    #0 main bug_leak.c:13

0x... is located 8 bytes inside of 20-byte region [0x..., 0x...)
freed by thread T0 here:
    #0 free ...
    #1 main bug_leak.c:10

SUMMARY: AddressSanitizer: heap-use-after-free bug_leak.c:13 in main

==12345==ERROR: LeakSanitizer: detected memory leaks
Direct leak of 400 byte(s) in 1 object(s) allocated from:
    #0 malloc ...
    #1 main bug_leak.c:16
```

**Cara membaca laporan ASan:**

| Bagian Laporan | Artinya |
|----------------|---------|
| `heap-use-after-free` | Tipe bug yang terdeteksi |
| `bug_leak.c:13` | Baris kode tempat bug **terjadi** |
| `freed by ... bug_leak.c:10` | Baris kode tempat memori **dibebaskan** |
| `LeakSanitizer: detected memory leaks` | Ada memori yang tidak pernah di-`free` |
| `400 byte(s) ... allocated from ... bug_leak.c:16` | Memory leak bermula dari baris 16 |

**Langkah 4: Perbaiki**

```c
free(arr);
arr = NULL;          /* ✅ Perbaiki use-after-free */
/* hapus printf arr[2] */

int *leak = malloc(100 * sizeof(int));
/* ... gunakan leak ... */
free(leak);          /* ✅ Perbaiki memory leak */
leak = NULL;
```

> [!NOTE]
> **ASan di Windows:** ASan bawaan GCC MinGW di Windows memiliki keterbatasan. Jika laporan tidak muncul, coba gunakan GCC di WSL (Windows Subsystem for Linux) atau Clang. Alternatifnya, gunakan [Dr. Memory](https://drmemory.org/) yang berjalan native di Windows.

---

### 3. Valgrind (Linux/macOS — Opsional)

> [!NOTE]
> **Valgrind** tidak tersedia di Windows secara native. Bagian ini ditujukan bagi yang menggunakan Linux atau macOS, atau menggunakan WSL.

```bash
# Kompilasi dengan info debug
gcc -Wall -g -std=c99 bug_leak.c -o bug_leak

# Jalankan dengan Valgrind
valgrind --leak-check=full --show-leak-kinds=all ./bug_leak
```

**Contoh output Valgrind** *(ilustrasi)*:

```text
==12346== Memcheck, a memory error detector
==12346== Invalid read of size 4
==12346==    at 0x...: main (bug_leak.c:13)
==12346==  Address 0x... is 8 bytes inside a block of size 20 free'd
==12346==    at 0x...: free (vg_replace_malloc.c:...)
==12346==    by 0x...: main (bug_leak.c:10)
==12346==
==12346== LEAK SUMMARY:
==12346==    definitely lost: 400 bytes in 1 blocks
```

---

### 4. GDB: Debugger Interaktif (Opsional)

> [!NOTE]
> GDB membutuhkan binary yang dikompilasi dengan `-g`. Di Windows, gunakan GDB yang datang bersama MinGW atau via WSL.

```bash
gcc -g -std=c99 program.c -o program
gdb ./program
```

Perintah GDB yang paling sering dibutuhkan pemula:

| Perintah | Fungsi |
|----------|--------|
| `run` | Jalankan program |
| `break main` | Beri breakpoint di fungsi `main` |
| `next` / `n` | Jalankan satu baris (step over) |
| `print var` | Cetak nilai variabel `var` |
| `print *ptr` | Cetak nilai yang ditunjuk `ptr` |
| `backtrace` / `bt` | Tampilkan call stack saat crash |
| `quit` | Keluar GDB |

---

## ✅ Checklist Sebelum Mengumpulkan Tugas Pointer

Sebelum submit, pastikan semua poin berikut terpenuhi:

```
KOMPILASI & PERINGATAN
[ ] Tidak ada error kompilasi
[ ] Tidak ada warning dari gcc -Wall -Wextra (atau semua warning dipahami)
[ ] Sudah diuji dengan -fsanitize=address,undefined (atau Valgrind di Linux)

POINTER & INISIALISASI
[ ] Semua pointer diinisialisasi (NULL atau alamat valid) sebelum digunakan
[ ] Tidak ada pointer yang di-dereference tanpa pengecekan NULL
[ ] Parameter fungsi yang hanya dibaca diberi const

MANAJEMEN MEMORI (DMA)
[ ] Setiap malloc/calloc dicek hasilnya (if == NULL)
[ ] Setiap malloc/calloc memiliki pasangan free yang sesuai
[ ] Tidak ada free yang dipanggil dua kali pada blok yang sama
[ ] Pointer di-set NULL setelah free
[ ] Tidak ada akses ke memori setelah free (use-after-free)
[ ] Tidak ada dangling pointer (tidak return &variabel_lokal)

ARRAY & BATAS
[ ] Semua akses array dalam batas [0, n-1]
[ ] Panjang array dikirim sebagai parameter terpisah ke fungsi

INPUT / OUTPUT
[ ] Semua scanf menggunakan &variabel (tidak lupa &)
[ ] Semua %p menggunakan cast (void *)
```

---

## 📝 Ringkasan

- Bug pointer sering tidak menimbulkan error kompilasi tetapi menyebabkan *crash* atau *silent data corruption* saat *runtime*.
- Kompilasi selalu dengan `-Wall -Wextra -g`; jangan abaikan warning compiler.
- `-fsanitize=address,undefined` adalah alat paling mudah untuk mendeteksi bug memori saat *runtime*.
- Laporan ASan memberi tahu tipe bug, baris tempat bug terjadi, dan baris alokasi asal.
- Gunakan checklist sebelum submit untuk memastikan tidak ada bug umum yang terlewat.

---

<div align="center">

[⬅️ Sebelumnya: 6.4 DMA](04-Dynamic-Memory-Allocation.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 6.6: Latihan dan Rangkuman ➡️](06-Latihan-dan-Rangkuman.md)

</div>
