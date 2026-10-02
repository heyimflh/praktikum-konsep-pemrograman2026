<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=6a1b9a&height=150&section=header&text=6.2%20Pointer%20dan%20Array&fontSize=35&fontColor=ffffff&animation=fadeIn&desc=Aritmatika%20Pointer%20%26%20Hubungan%20Array-Pointer&descSize=14&descAlignY=75)

[⬅️ Modul 6.1: Pengenalan Pointer](01-Pengenalan-Pointer.md) &nbsp;•&nbsp; [Overview Bab 6](Bab6-Overview.md) &nbsp;•&nbsp; [Modul 6.3: Pass By Reference ➡️](03-Pass-By-Reference.md)

---

</div>

## 🎯 Tujuan Pembelajaran

Setelah menyelesaikan modul ini, kamu diharapkan mampu:

1. Menjelaskan hubungan antara nama array dan alamat elemen pertamanya.
2. Melakukan aritmatika pointer dan memahami satuan "lompatan" per tipe data.
3. Menggunakan notasi `arr[i]` dan `*(arr+i)` secara bergantian.
4. Membedakan array dan pointer, terutama perihal `sizeof` dan kemampuan re-assignment.
5. Mengirim array ke fungsi menggunakan pointer dan menjelaskan kenapa panjang harus dikirim terpisah.

---

## 📌 Nama Array = Alamat Elemen Pertama

Saat kamu membuat array `int skor[5]`, nama `skor` secara otomatis "*meluruh*" (*decay*) menjadi pointer ke elemen pertamanya. Artinya, `skor` ≈ `&skor[0]`.

```c
#include <stdio.h>

int main(void) {
    int skor[5] = {10, 20, 30, 40, 50};
    int *ptr     = skor;   /* tidak perlu &skor, karena skor sudah "decay" */

    printf("skor     : %p\n", (void *)skor);
    printf("&skor[0] : %p\n", (void *)&skor[0]);
    printf("ptr      : %p\n", (void *)ptr);
    printf("(ketiganya sama!)\n");

    return 0;
}
```

**Output:**
```text
skor     : 000000BBDFFFF670
&skor[0] : 000000BBDFFFF670
ptr      : 000000BBDFFFF670
(ketiganya sama!)
```

> [!NOTE]
> Istilah *array decay* artinya: dalam sebagian besar ekspresi, nama array secara otomatis dikonversi menjadi pointer ke elemen pertama. Ini bukan casting manual — C melakukannya secara implisit.

---

## ➕ Aritmatika Pointer

Ketika kamu menambah atau mengurangi angka dari pointer, C secara otomatis memperhitungkan **ukuran tipe data**. Jadi `ptr + 1` tidak maju 1 byte, melainkan maju sebesar `sizeof(int)` byte (4 byte pada sistem 64-bit).

```c
#include <stdio.h>

int main(void) {
    int skor[5] = {10, 20, 30, 40, 50};
    int *ptr     = skor;

    printf("ptr   = %p\n", (void *)ptr);
    printf("ptr+1 = %p  (maju %zu byte)\n", (void *)(ptr + 1), sizeof(int));
    printf("ptr+2 = %p\n", (void *)(ptr + 2));

    return 0;
}
```

**Output:**
```text
ptr   = 000000BBDFFFF670
ptr+1 = 000000BBDFFFF674  (maju 4 byte)
ptr+2 = 000000BBDFFFF678
```

Diagram memori untuk `int skor[5]`:

```
Elemen :  skor[0]   skor[1]   skor[2]   skor[3]   skor[4]
Nilai  :    10        20        30        40        50
Alamat : 0x...670  0x...674  0x...678  0x...67C  0x...680
         ^ptr      ^ptr+1    ^ptr+2    ^ptr+3    ^ptr+4
```

Setiap elemen berselang **4 byte** (ukuran `int`). Jika tipenya `double` (8 byte), selisih alamatnya menjadi 8.

---

## 🔄 `arr[i]` Setara dengan `*(arr + i)`

Ini adalah identitas paling penting dalam hubungan array-pointer:

> **`arr[i]`** ≡ **`*(arr + i)`** (selalu, tanpa pengecualian)

```c
#include <stdio.h>

int main(void) {
    int skor[5] = {10, 20, 30, 40, 50};

    printf("=== arr[i] vs *(arr+i) ===\n");
    for (int i = 0; i < 5; i++) {
        printf("skor[%d] = %d  |  *(skor+%d) = %d\n",
               i, skor[i], i, *(skor + i));
    }
    return 0;
}
```

**Output:**
```text
=== arr[i] vs *(arr+i) ===
skor[0] = 10  |  *(skor+0) = 10
skor[1] = 20  |  *(skor+1) = 20
skor[2] = 30  |  *(skor+2) = 30
skor[3] = 40  |  *(skor+3) = 40
skor[4] = 50  |  *(skor+4) = 50
```

### Loop dengan Indeks vs Loop dengan Pointer

```c
#include <stdio.h>

int main(void) {
    int skor[5] = {10, 20, 30, 40, 50};

    /* Cara 1: loop dengan indeks (lebih mudah dibaca) */
    printf("Loop indeks: ");
    for (int i = 0; i < 5; i++) {
        printf("%d ", skor[i]);
    }
    printf("\n");

    /* Cara 2: loop dengan pointer (idiom C klasik) */
    printf("Loop pointer: ");
    for (int *p = skor; p < skor + 5; p++) {
        printf("%d ", *p);
    }
    printf("\n");

    return 0;
}
```

**Output:**
```text
Loop indeks: 10 20 30 40 50 
Loop pointer: 10 20 30 40 50 
```

---

## ↔️ Operasi `ptr++`, `ptr--`, dan Selisih Pointer

```c
#include <stdio.h>

int main(void) {
    int skor[5] = {10, 20, 30, 40, 50};

    int *awal  = skor;
    int *akhir = skor + 4;

    /* Iterasi maju dengan ptr++ */
    printf("Iterasi maju: ");
    for (int *p = awal; p <= akhir; p++) {
        printf("%d ", *p);
    }
    printf("\n");

    /* Selisih dua pointer = jumlah elemen di antara keduanya */
    printf("Selisih (akhir - awal) = %td elemen\n", akhir - awal);

    return 0;
}
```

**Output:**
```text
Iterasi maju: 10 20 30 40 50 
Selisih (akhir - awal) = 4 elemen
```

> [!NOTE]
> Selisih dua pointer menghasilkan tipe `ptrdiff_t` (bukan `int`). Gunakan format `%td` untuk mencetaknya.

---

## ⚡ Perbedaan Penting: Array ≠ Pointer

Meski nama array *decay* ke pointer, **array dan pointer tidak sama**:

```c
#include <stdio.h>

int main(void) {
    int skor[5] = {10, 20, 30, 40, 50};
    int *ptr     = skor;

    /* sizeof berbeda! */
    printf("sizeof(skor): %zu   (ukuran seluruh array = 5 x 4)\n", sizeof(skor));
    printf("sizeof(ptr) : %zu   (ukuran pointer saja)\n",           sizeof(ptr));

    /* ptr bisa diarahkan ulang, array tidak bisa */
    ptr++;                /* ✅ OK: ptr sekarang menunjuk ke skor[1] */
    /* skor++; */         /* ❌ COMPILE ERROR: nama array bukan variabel biasa */

    printf("*ptr setelah ptr++: %d\n", *ptr);   /* 20 */

    return 0;
}
```

**Output:**
```text
sizeof(skor): 20   (ukuran seluruh array = 5 x 4)
sizeof(ptr) : 8    (ukuran pointer saja)
*ptr setelah ptr++: 20
```

| Aspek | `int skor[5]` (array) | `int *ptr` (pointer) |
|-------|----------------------|---------------------|
| `sizeof` | Ukuran **total** array | Ukuran **pointer** saja |
| Re-assignment | ❌ Tidak bisa (`skor++` error) | ✅ Bisa (`ptr++`) |
| Deklarasi beri nilai | `int arr[] = {1,2,3}` | `int *p = arr` |

---

## 📤 Mengirim Array ke Fungsi

Ketika array dikirim ke fungsi, yang **benar-benar disalin** adalah pointer ke elemen pertamanya (bukan seluruh isi array). Ini artinya:
- Fungsi dapat memodifikasi isi array asli.
- Parameter `int arr[]` dan `int *arr` adalah **identik** di parameter fungsi.
- **Panjang array tidak ikut terkirim** — kamu harus kirim secara eksplisit!

```c
#include <stdio.h>

/* int arr[] dan int *arr di sini identik — pilih salah satu */
void cetak_array(const int *arr, int n) {
    for (int i = 0; i < n; i++) {
        printf("arr[%d] = %d\n", i, arr[i]);
    }
}

void gandakan(int *arr, int n) {
    for (int i = 0; i < n; i++) {
        arr[i] *= 2;   /* memodifikasi array asli! */
    }
}

int main(void) {
    int skor[4] = {10, 20, 30, 40};
    int n = 4;

    printf("Sebelum:\n");
    cetak_array(skor, n);

    gandakan(skor, n);

    printf("Sesudah digandakan:\n");
    cetak_array(skor, n);

    return 0;
}
```

**Output:**
```text
Sebelum:
arr[0] = 10
arr[1] = 20
arr[2] = 30
arr[3] = 40
Sesudah digandakan:
arr[0] = 20
arr[1] = 40
arr[2] = 60
arr[3] = 80
```

> [!WARNING]
> **Kenapa panjang harus dikirim terpisah?**
> Di dalam fungsi, `sizeof(arr)` akan mengembalikan ukuran **pointer** (8 byte), bukan ukuran array. Tidak ada cara bagi fungsi untuk "menghitung" panjang array dari pointer semata — kamu wajib kirim `n` secara eksplisit.

---

## 🔤 Pengenalan: String sebagai `char *`

*String* di C pada dasarnya adalah **array karakter** yang diakhiri karakter null `'\0'`. Ada dua cara menyimpannya:

```c
#include <stdio.h>

int main(void) {
    /* 1. char[] — disimpan di stack, BISA dimodifikasi */
    char nama[] = "Budi";
    nama[0] = 'R';   /* ✅ OK: mengubah isi array */
    printf("char[]: %s\n", nama);   /* Rudi */

    /* 2. const char * ke string literal — READ ONLY, JANGAN dimodifikasi! */
    const char *greeting = "Halo dunia";
    printf("literal: %s\n", greeting);

    /* Iterasi karakter dengan pointer */
    printf("Karakter: ");
    for (const char *p = greeting; *p != '\0'; p++) {
        printf("%c ", *p);
    }
    printf("\n");

    return 0;
}
```

**Output:**
```text
char[]: Rudi
literal: Halo dunia
Karakter: H a l o   d u n i a 
```

> [!WARNING]
> **String literal bersifat *read-only*!**
> ```c
> char *s = "Hello";
> s[0] = 'h';   /* ❌ UNDEFINED BEHAVIOR — program bisa crash! */
> ```
> Selalu gunakan `const char *` untuk pointer ke string literal, agar compiler memberimu peringatan jika kamu tidak sengaja memodifikasinya.

---

## 🚧 Peringatan: Out-of-Bounds = *Undefined Behavior*

Mengakses elemen di luar batas array adalah **salah satu kesalahan paling berbahaya** di C:

```c
int arr[3] = {1, 2, 3};

arr[3]  = 99;  /* ❌ UNDEFINED BEHAVIOR: indeks 3 di luar batas [0..2] */
arr[-1] = 88;  /* ❌ UNDEFINED BEHAVIOR: indeks negatif */
```

> [!CAUTION]
> *Undefined behavior* berarti compiler tidak wajib memberikan peringatan, program mungkin **tampak bekerja**, tetapi bisa crash kapan saja, menghasilkan output acak, atau — yang paling berbahaya — diam-diam merusak data lain di memori. Selalu pastikan indeks berada dalam rentang `[0, n-1]`.

---

## 📝 Ringkasan

- Nama array secara otomatis *decay* menjadi pointer ke elemen pertama: `arr` ≈ `&arr[0]`.
- Aritmatika pointer memperhitungkan ukuran tipe: `ptr + 1` maju sebesar `sizeof(tipe)` byte.
- `arr[i]` dan `*(arr + i)` selalu menghasilkan nilai yang sama.
- Array dan pointer berbeda: `sizeof(arr)` = ukuran total array, `sizeof(ptr)` = ukuran pointer.
- Nama array tidak bisa di-*assign* ulang (`arr++` adalah *compile error*).
- Saat mengirim array ke fungsi, panjangnya harus dikirim sebagai parameter terpisah.
- String literal bersifat *read-only* — gunakan `const char *` untuk menunjuk ke literal.
- Akses di luar batas array adalah *undefined behavior*.

---

## 🏋️ Latihan Singkat

Kerjakan latihan berikut. Jawaban lengkap ada di [Modul 6.6: Latihan dan Rangkuman](06-Latihan-dan-Rangkuman.md).

1. **(Tebak Output)** Apa yang dicetak program ini?
   ```c
   int arr[] = {5, 10, 15, 20};
   int *p = arr + 2;
   printf("%d %d\n", *p, *(p - 1));
   ```

2. **(Tebak Output)** Apa nilai `arr[1]` setelah kode ini dijalankan?
   ```c
   int arr[3] = {1, 2, 3};
   int *p = arr;
   *(p + 1) = 99;
   ```

3. **(Cari Bug)** Apa yang salah dengan fungsi ini?
   ```c
   void cetak(int *arr) {
       int n = sizeof(arr) / sizeof(arr[0]);
       for (int i = 0; i < n; i++) printf("%d ", arr[i]);
   }
   ```

4. **(Buat Program)** Tulis fungsi `balik_array(int *arr, int n)` yang membalik urutan elemen array **di tempat** (*in-place*) menggunakan pointer.

---

<div align="center">

[⬅️ Sebelumnya: 6.1 Pengenalan Pointer](01-Pengenalan-Pointer.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 6.3: Pass By Reference ➡️](03-Pass-By-Reference.md)

</div>
