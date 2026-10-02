<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=00897b&height=150&section=header&text=6.4%20Dynamic%20Memory%20Allocation&fontSize=30&fontColor=ffffff&animation=fadeIn&desc=Mengelola%20Memori%20Secara%20Manual%20dengan%20malloc%20%26%20free&descSize=13&descAlignY=75)

[⬅️ Modul 6.3: Pass By Reference](03-Pass-By-Reference.md) &nbsp;•&nbsp; [Overview Bab 6](Bab6-Overview.md) &nbsp;•&nbsp; [Modul 6.5: Debugging ➡️](05-Kesalahan-Umum-dan-Debugging.md)

---

</div>

## 🎯 Tujuan Pembelajaran

Setelah menyelesaikan modul ini, kamu diharapkan mampu:

1. Menjelaskan perbedaan *stack* dan *heap* serta siklus hidup variabel di masing-masingnya.
2. Menggunakan `malloc`, `calloc`, `realloc`, dan `free` dengan pola yang aman.
3. Membuat dan mengelola array dengan ukuran yang ditentukan saat *runtime*.
4. Menerapkan aturan emas DMA untuk mencegah memory leak, double free, dan use-after-free.

---

## 💡 Apa itu Dynamic Memory Allocation?

**Dynamic Memory Allocation (DMA)** adalah teknik memesan ruang di RAM secara manual saat program sedang berjalan (*runtime*). Memori yang dipesan akan **terus ada** sampai kita secara eksplisit membebaskannya.

Karena memori berkaitan erat dengan alamat, **DMA tidak dapat dipisahkan dari Pointer**.

---

## 🗂️ Stack vs Heap: Di Mana Memori Disimpan?

Sebelum memahami DMA, penting untuk tahu bahwa RAM program dibagi menjadi dua wilayah utama:

```
     Memori Program
  ┌─────────────────┐  ← alamat tinggi
  │      STACK      │  ← variabel lokal, parameter fungsi
  │   (tumbuh ↓)    │     - dikelola otomatis oleh compiler
  │                 │     - hancur saat scope/fungsi selesai
  ├─────────────────┤
  │    (kosong)     │
  ├─────────────────┤
  │      HEAP       │  ← malloc/calloc/realloc
  │   (tumbuh ↑)    │     - dikelola MANUAL oleh programmer
  │                 │     - tetap ada sampai free() dipanggil
  ├─────────────────┤
  │  Kode Program   │
  └─────────────────┘  ← alamat rendah
```

| Aspek | Stack | Heap |
|-------|-------|------|
| Pengelolaan | Otomatis (compiler) | Manual (programmer) |
| Siklus hidup | Sampai *scope*/fungsi selesai | Sampai `free()` dipanggil |
| Kecepatan alokasi | Sangat cepat | Lebih lambat |
| Ukuran | Terbatas (biasanya 1-8 MB) | Jauh lebih besar |
| Risiko | *Stack overflow* (ukuran terlalu besar) | *Memory leak* (lupa `free`) |

### Masalah Variabel Biasa (Stack)

```c
if (kondisi) {
    int data = 5;   /* data dibuat di STACK */
    printf("%d", data);
}
/* data sudah hancur saat keluar dari blok {} ini! */
```

### Solusi dengan DMA (Heap)

```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    /* address adalah variabel lokal di outer scope — BUKAN variabel global! */
    int *address = NULL;
    int kondisi  = 1;

    if (kondisi) {
        int *data = malloc(sizeof *data);   /* data dialokasikan di HEAP */
        if (data == NULL) {
            fprintf(stderr, "Alokasi memori gagal!\n");
            return 1;
        }
        *data    = 5;
        address  = data;   /* simpan alamat heap ke pointer di outer scope */
    }
    /* data (stack) sudah hancur, tapi MEMORI HEAP-nya tetap ada! */

    if (address != NULL) {
        printf("Nilai: %d\n", *address);   /* ✅ masih bisa diakses */
        free(address);                      /* wajib: bebaskan memori heap */
        address = NULL;
    }

    return 0;
}
```

**Output:**
```text
Nilai: 5
```

---

## 🛠️ Empat Fungsi Manajemen Memori

Semua fungsi DMA membutuhkan `#include <stdlib.h>`.

### Tabel Ringkas

| Fungsi | Tanda Tangan | Inisialisasi | Keterangan |
|--------|-------------|:---:|----------|
| `malloc` | `void *malloc(size_t size)` | ❌ Tidak | Pesan `size` byte; isi tak terdefinisi |
| `calloc` | `void *calloc(size_t n, size_t size)` | ✅ Ya (ke 0) | Pesan `n×size` byte; isi diset ke nol |
| `realloc` | `void *realloc(void *ptr, size_t size)` | ❌ Tidak | Ubah ukuran blok yang sudah ada |
| `free` | `void free(void *ptr)` | — | Bebaskan memori yang dialokasikan |

> [!NOTE]
> Semua fungsi (`malloc`, `calloc`, `realloc`) mengembalikan `void *`. Di C, `void *` **otomatis dikonversi** ke tipe pointer manapun — kamu **tidak perlu menulis cast** seperti `(int *)malloc(...)`. Di C++ kamu wajib menggunakan cast, tapi modul ini menggunakan C99.
>
> Gaya yang direkomendasikan: **tanpa cast**, dengan `sizeof *ptr`:
> ```c
> int *p = malloc(sizeof *p);          /* ✅ tanpa cast, direkomendasikan */
> int *p = (int *)malloc(sizeof(int)); /* juga umum ditemui, terutama di kode C++ */
> ```

---

### 1. `malloc()` — Memory Allocation

```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int *data = NULL;

    /* Pesan memori untuk 1 int */
    data = malloc(sizeof *data);
    if (data == NULL) {   /* SELALU cek NULL! malloc bisa gagal jika RAM habis */
        fprintf(stderr, "Alokasi memori gagal!\n");
        return 1;
    }

    *data = 32;
    printf("Isi memori: %d\n", *data);

    free(data);     /* WAJIB: bebaskan setelah selesai */
    data = NULL;    /* Set NULL agar tidak jadi dangling pointer */

    return 0;
}
```

**Output:**
```text
Isi memori: 32
Memori berhasil dibebaskan.
```

> [!IMPORTANT]
> Selalu gunakan `sizeof *ptr` (bukan `sizeof(int)`) untuk menentukan ukuran. Alasannya: jika kamu mengubah tipe pointer, `sizeof *ptr` otomatis menyesuaikan, sementara `sizeof(int)` harus diubah manual.
> ```c
> int *p  = malloc(sizeof *p);          /* ✅ aman saat refaktor tipe */
> int *p2 = malloc(sizeof(int));        /* ⚠️ harus ingat ubah jika ganti tipe */
> ```

---

### 2. `calloc()` — Cleared Allocation

Berbeda dengan `malloc`, `calloc` **menginisialisasi semua byte ke nol**. Berguna saat kamu butuh nilai awal 0.

```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int n = 5;
    int *arr = calloc(n, sizeof *arr);  /* n elemen, masing-masing sizeof(int) */
    if (arr == NULL) { return 1; }

    printf("Nilai awal setelah calloc (semua 0):\n");
    for (int i = 0; i < n; i++) {
        printf("arr[%d] = %d\n", i, arr[i]);
    }

    free(arr);
    arr = NULL;
    return 0;
}
```

**Output:**
```text
Nilai awal setelah calloc (semua 0):
arr[0] = 0
arr[1] = 0
arr[2] = 0
arr[3] = 0
arr[4] = 0
```

---

### 3. `realloc()` — Resize Allocation

`realloc` mengubah ukuran blok memori yang sudah ada. Gunakan **pointer sementara** agar blok lama tidak hilang jika `realloc` gagal.

```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int count = 3;
    int *arr  = malloc(count * sizeof *arr);
    if (arr == NULL) { return 1; }

    arr[0] = 10; arr[1] = 20; arr[2] = 30;
    printf("Array awal (%d elemen): %d %d %d\n", count, arr[0], arr[1], arr[2]);

    /* Perluas array dari 3 ke 5 elemen */
    int new_count = 5;
    int *temp     = realloc(arr, new_count * sizeof *temp);

    if (temp == NULL) {
        /* realloc gagal: arr MASIH VALID, bisa digunakan atau dibebaskan */
        fprintf(stderr, "realloc gagal! Data lama masih aman.\n");
        free(arr);
        return 1;
    }
    arr = temp;   /* realloc berhasil — update pointer utama */

    arr[3] = 40; arr[4] = 50;
    printf("Array setelah realloc (%d elemen): ", new_count);
    for (int i = 0; i < new_count; i++) {
        printf("%d ", arr[i]);
    }
    printf("\n");

    free(arr);
    arr = NULL;
    return 0;
}
```

**Output:**
```text
Array awal (3 elemen): 10 20 30
Array setelah realloc (5 elemen): 10 20 30 40 50 
```

> [!WARNING]
> **Jangan langsung `arr = realloc(arr, ...)`!**
> Jika `realloc` gagal, ia mengembalikan `NULL` dan **blok lama tetap valid**. Jika kamu langsung assign ke `arr`, pointer ke blok lama hilang dan terjadi **memory leak**.
> ```c
> arr = realloc(arr, baru);  /* ❌ SALAH — memory leak jika realloc gagal */
> int *temp = realloc(arr, baru);
> if (temp != NULL) arr = temp;  /* ✅ BENAR — blok lama aman jika gagal */
> ```

---

## 📦 Array Dinamis: Ukuran Ditentukan Saat *Runtime*

Kelemahan array statis biasa (`int arr[100]`) adalah ukurannya harus ditentukan saat *compile time*. Selain itu, array lokal berukuran sangat besar (`int arr[999999]`) berisiko **stack overflow** karena berada di *stack*.

> [!NOTE]
> **Bagaimana dengan VLA (Variable Length Array)?**
> C99 mengenalkan VLA (`int arr[n]` di mana `n` adalah variabel), sehingga ukuran bisa ditentukan saat *runtime*. Namun VLA tetap berada di *stack* (rentan *stack overflow*), tidak bisa digunakan dengan `realloc`, dan masa hidupnya terbatas pada *scope*-nya. Karena itu, VLA **tidak menggantikan** DMA untuk kasus data besar atau data yang hidup melewati *scope*.

```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int *price_list = NULL;
    int count;
    int i;

    printf("Masukkan jumlah barang: ");
    scanf("%d", &count);

    /* Ukuran ditentukan oleh input pengguna saat runtime */
    price_list = malloc(count * sizeof *price_list);
    if (price_list == NULL) {
        fprintf(stderr, "Alokasi memori gagal!\n");
        return 1;
    }

    for (i = 0; i < count; i++) {
        printf("Masukkan harga barang ke-%d: ", i + 1);
        scanf("%d", &price_list[i]);
    }

    printf("\n=== LIST HARGA BARANG (Jml: %d) ===\n", count);
    for (i = 0; i < count; i++) {
        printf("- Barang ke-%d: Rp%d\n", i + 1, price_list[i]);
    }

    free(price_list);
    price_list = NULL;
    return 0;
}
```

---

## 🏆 Aturan Emas DMA (Checklist)

> [!IMPORTANT]
> **4 Aturan yang Wajib Selalu Diikuti:**
>
> - [ ] **Cek NULL** — Selalu periksa hasil `malloc`/`calloc`/`realloc` sebelum digunakan.
> - [ ] **`free` tepat satu kali** — Setiap blok yang dialokasikan harus dibebaskan **tepat sekali**. Dua kali `free` (*double free*) adalah *undefined behavior*.
> - [ ] **Set NULL setelah `free`** — Langsung set pointer ke `NULL` setelah `free(ptr)` untuk menghindari *dangling pointer*.
> - [ ] **Jangan pakai setelah `free`** — Mengakses memori setelah dibebaskan (*use-after-free*) adalah *undefined behavior*.

```c
int *p = malloc(sizeof *p);
if (p == NULL) { /* handle error */ }

/* ... gunakan p ... */

free(p);     /* bebaskan */
p = NULL;    /* set NULL segera! */

/* free(p); */    /* ❌ double free jika p tidak NULL! */
/* *p = 1;  */    /* ❌ use-after-free! */
```

---

## 📝 Ringkasan

- Variabel lokal hidup di *stack* dan **hancur** saat *scope* berakhir; memori DMA hidup di *heap* dan bertahan sampai `free()`.
- `malloc(n)` pesan `n` byte tanpa inisialisasi; `calloc(count, size)` pesan dan inisialisasi ke nol.
- `realloc` mengubah ukuran blok; selalu gunakan pointer sementara agar blok lama tidak hilang jika gagal.
- Tidak perlu cast `(int *)` pada `malloc` di C — `void *` otomatis dikonversi.
- Aturan emas: cek NULL → gunakan → free → set NULL.
- VLA (C99) ada tapi tidak menggantikan DMA: masih di stack, tidak bisa `realloc`, masa hidup terbatas scope.

---

## 🏋️ Latihan Singkat

Kerjakan latihan berikut. Jawaban lengkap ada di [Modul 6.6: Latihan dan Rangkuman](06-Latihan-dan-Rangkuman.md).

1. **(Tebak Output)** Apa yang dicetak program ini?
   ```c
   int *p = calloc(3, sizeof(int));
   p[1] = 7;
   printf("%d %d %d\n", p[0], p[1], p[2]);
   free(p);
   ```

2. **(Cari Bug)** Ada berapa bug di kode ini? Sebutkan semuanya.
   ```c
   int *arr = malloc(5);   /* minta 5 byte */
   arr[0] = 100;
   free(arr);
   printf("%d\n", arr[0]);
   free(arr);
   ```

3. **(Buat Program)** Tulis program yang meminta input `n` (jumlah mahasiswa), lalu alokasikan array nilai bertipe `int` secara dinamis, isi dengan input pengguna, hitung rata-rata, lalu bebaskan memori. Sertakan pengecekan NULL.

4. **(Cari Bug)** Apa yang salah dengan pola `realloc` ini?
   ```c
   arr = realloc(arr, new_size * sizeof(int));
   if (arr == NULL) { fprintf(stderr, "gagal\n"); return 1; }
   ```

---

## 🔬 Pengayaan (Opsional): Array 2D Dinamis

> [!NOTE]
> **Bagian ini opsional** — hanya untuk yang ingin eksplorasi lebih lanjut.

Array 2D dinamis dibuat menggunakan **pointer ke pointer** (`int **`): satu array of pointers, di mana setiap pointer menunjuk ke sebuah baris.

```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int rows = 3, cols = 4;

    /* Langkah 1: Alokasi array of pointers (satu per baris) */
    int **matrix = malloc(rows * sizeof *matrix);
    if (matrix == NULL) { return 1; }

    /* Langkah 2: Alokasi setiap baris */
    for (int i = 0; i < rows; i++) {
        matrix[i] = malloc(cols * sizeof **matrix);
        if (matrix[i] == NULL) {
            /* Bebaskan baris yang sudah teralokasi sebelumnya */
            for (int j = 0; j < i; j++) free(matrix[j]);
            free(matrix);
            return 1;
        }
    }

    /* Isi dan tampilkan */
    for (int i = 0; i < rows; i++)
        for (int j = 0; j < cols; j++)
            matrix[i][j] = i * cols + j + 1;

    printf("Matrix %dx%d:\n", rows, cols);
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) printf("%3d", matrix[i][j]);
        printf("\n");
    }

    /* Langkah 3: Bebaskan — baris dulu, lalu array of pointers */
    for (int i = 0; i < rows; i++) {
        free(matrix[i]);
        matrix[i] = NULL;
    }
    free(matrix);
    matrix = NULL;

    return 0;
}
```

**Output:**
```text
Matrix 3x4:
  1  2  3  4
  5  6  7  8
  9 10 11 12
```

> [!CAUTION]
> **Urutan pembebasan sangat penting!** Bebaskan setiap baris terlebih dahulu (`free(matrix[i])`), baru bebaskan array of pointers (`free(matrix)`). Membalik urutan ini akan menyebabkan *memory leak* karena alamat baris-baris sudah tidak bisa diakses.

---

<div align="center">

[⬅️ Sebelumnya: 6.3 Pass By Reference](03-Pass-By-Reference.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 6.5: Debugging ➡️](05-Kesalahan-Umum-dan-Debugging.md)

</div>
