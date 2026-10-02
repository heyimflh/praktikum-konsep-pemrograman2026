<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=d81b60&height=150&section=header&text=6.3%20Pass%20By%20Reference&fontSize=32&fontColor=ffffff&animation=fadeIn&desc=Memanipulasi%20Variabel%20Antar%20Fungsi%20via%20Pointer&descSize=14&descAlignY=75)

[⬅️ Modul 6.2: Pointer dan Array](02-Pointer-dan-Array.md) &nbsp;•&nbsp; [Overview Bab 6](Bab6-Overview.md) &nbsp;•&nbsp; [Modul 6.4: DMA ➡️](04-Dynamic-Memory-Allocation.md)

---

</div>

> [!NOTE]
> **"Pass by reference" di C sebenarnya adalah simulasi.**
> C **selalu** menyalin nilai saat memanggil fungsi (*pass by value*). Yang kita sebut "pass by reference" sebenarnya adalah: **mengirim alamat memori (pointer) sebagai nilai**. Fungsi menerima salinan alamat itu, lalu menggunakannya untuk mengakses dan memodifikasi variabel asli.

---

## 🎯 Tujuan Pembelajaran

Setelah menyelesaikan modul ini, kamu diharapkan mampu:

1. Menjelaskan perbedaan *pass by value* dan simulasi *pass by reference* di C.
2. Mengimplementasikan fungsi yang memodifikasi variabel di *scope* pemanggil menggunakan pointer.
3. Menulis fungsi yang "mengembalikan" lebih dari satu nilai melalui parameter pointer.
4. Menghindari kesalahan *dangling pointer* akibat mengembalikan alamat variabel lokal.

---

## 💡 Mengingat Kembali Masalah *Scope*

Perhatikan contoh berikut:

```c
#include <stdio.h>

void kurangi_health(int health, int jumlah) {
    /* ⚠️ Logic bug: variabel `health` di sini adalah SALINAN LOKAL.
       Memodifikasinya tidak berdampak apapun pada `health` di main().
       Kode ini tetap terkompilasi tanpa error! */
    health = health - jumlah;
}

int main(void) {
    int health = 100;
    kurangi_health(health, 20);
    printf("Health akhir: %d\n", health); /* Tetap 100 — tidak berubah! */
    return 0;
}
```

**Output:**
```text
Health akhir: 100
```

Fenomena ini disebut **Pass By Value**. Program hanya memberikan *fotokopi* dari isi variabel `health` ke fungsi. Berapapun perubahan pada fotokopi, dokumen asli di `main()` tetap utuh.

---

## 📬 Solusi: Simulasi Pass By Reference

Kita bisa "menembus" batas *scope* dengan mengirim **alamat memori** (pointer) ke fungsi. Fungsi lalu menggunakan pointer itu untuk langsung menulis ke variabel asli.

### Cara Implementasi:
1. **Ubah Parameter Menjadi Pointer:** Tambahkan `*` pada parameter fungsi.
2. **Kirim Alamat Memori:** Gunakan `&` saat memanggil fungsi dari `main()`.
3. **Gunakan Dereference dalam Fungsi:** Gunakan `*` untuk memodifikasi nilainya.

```c
#include <stdio.h>

/* 1. Parameter berupa pointer (*) */
void kurangi_health(int *health_ptr, int jumlah);

int main(void) {
    int health = 100;
    printf("Health awal: %d\n", health);

    /* 2. Kirim alamat memori dari `health` (&) */
    kurangi_health(&health, 20);

    printf("Health akhir: %d\n", health);
    return 0;
}

void kurangi_health(int *health_ptr, int jumlah) {
    /* 3. Modifikasi langsung melalui dereferencing (*) */
    *health_ptr = *health_ptr - jumlah;
}
```

**Output:**
```text
Health awal: 100
Health akhir: 80
```

> [!TIP]
> **Kapan harus menggunakan simulasi Pass By Reference?**
> 1. Ketika sebuah fungsi perlu mengubah nilai variabel yang ada di *scope* pemanggilnya.
> 2. Ketika kamu ingin sebuah fungsi "mengembalikan" lebih dari 1 nilai.
> 3. Saat meneruskan **array** ke fungsi (array secara otomatis *decay* ke pointer — lihat Modul 6.2).
> 4. Saat meneruskan **struct** besar ke fungsi: C akan menyalin seluruh struct (*pass by value*) jika dikirim langsung, yang boros memori. Kirim `struct *` agar hanya alamatnya yang disalin. *(Struct akan dibahas di bab lain.)*

---

## 🔀 Contoh Klasik: Fungsi `swap()`

Fungsi `swap` adalah contoh paling klasik mengapa *pass by reference* diperlukan.

### Versi Salah (Pass By Value):

```c
#include <stdio.h>

void swap_gagal(int a, int b) {
    int temp = a;
    a = b;
    b = temp;
    /* a dan b hanyalah salinan lokal — tidak ada efek ke luar! */
}

int main(void) {
    int x = 10, y = 20;
    printf("Sebelum: x=%d, y=%d\n", x, y);
    swap_gagal(x, y);
    printf("Sesudah: x=%d, y=%d\n", x, y);  /* Masih 10 dan 20! */
    return 0;
}
```

**Output:**
```text
Sebelum: x=10, y=20
Sesudah: x=10, y=20
```

### Versi Benar (Pointer):

```c
#include <stdio.h>

void swap(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

int main(void) {
    int x = 10, y = 20;
    printf("Sebelum: x=%d, y=%d\n", x, y);
    swap(&x, &y);
    printf("Sesudah: x=%d, y=%d\n", x, y);
    return 0;
}
```

**Output:**
```text
Sebelum: x=10, y=20
Sesudah: x=20, y=10
```

### Tracing Memori `swap(&x, &y)`:

```
SEBELUM swap():
  Memori main: x=10 (0xA100)   y=20 (0xA104)
  Parameter:   a=0xA100         b=0xA104   (salinan ALAMAT, bukan nilai!)

LANGKAH DALAM swap():
  temp = *a    → temp = 10
  *a   = *b    → nilai di 0xA100 = 20  (x sekarang 20!)
  *b   = temp  → nilai di 0xA104 = 10  (y sekarang 10!)

SESUDAH swap():
  Memori main: x=20 (0xA100)   y=10 (0xA104)
```

> [!NOTE]
> Perhatikan bahwa pointer `a` dan `b` sendiri adalah **salinan** — isinya (alamat) disalin ke parameter fungsi. Yang berubah adalah isi memori di alamat yang ditunjuk, bukan pointer-nya sendiri.

---

## 📊 Mengembalikan Lebih dari Satu Nilai

Fungsi C hanya bisa `return` satu nilai. Pointer memungkinkan kita "mengembalikan" banyak nilai melalui parameter:

```c
#include <stdio.h>

/* const int *arr: arr hanya dibaca (tidak dimodifikasi) */
void hitung_min_max(const int *arr, int n, int *min, int *max) {
    *min = arr[0];
    *max = arr[0];
    for (int i = 1; i < n; i++) {
        if (arr[i] < *min) *min = arr[i];
        if (arr[i] > *max) *max = arr[i];
    }
}

int main(void) {
    int nilai[] = {85, 92, 67, 78, 95, 71};
    int n = 6;
    int min_val, max_val;

    hitung_min_max(nilai, n, &min_val, &max_val);

    printf("Nilai  : 85 92 67 78 95 71\n");
    printf("Minimum: %d\n", min_val);
    printf("Maksimum: %d\n", max_val);

    return 0;
}
```

**Output:**
```text
Nilai  : 85 92 67 78 95 71
Minimum: 67
Maksimum: 95
```

> [!TIP]
> Gunakan `const` pada parameter input (`const int *arr`) untuk menyatakan bahwa fungsi **tidak akan memodifikasi** data tersebut. Ini membuat kode lebih aman dan niatmu lebih jelas.

---

## ☠️ Jangan Kembalikan Alamat Variabel Lokal! (*Dangling Pointer*)

Ini adalah salah satu bug paling berbahaya di C. Variabel lokal **hidup di stack** dan **hancur** saat fungsi selesai. Mengembalikan alamatnya menghasilkan ***dangling pointer*** — pointer yang menunjuk ke memori yang sudah tidak valid.

```c
#include <stdio.h>

/* ❌ SALAH: mengembalikan alamat variabel lokal! */
int *buat_nilai_salah(void) {
    int nilai = 42;
    return &nilai;   /* nilai akan hancur saat fungsi ini selesai! */
}

int main(void) {
    int *ptr = buat_nilai_salah();
    /* ptr sekarang adalah dangling pointer */
    /* Mengakses *ptr di bawah ini adalah UNDEFINED BEHAVIOR */
    printf("%d\n", *ptr);   /* Output tidak terdefinisi — bisa crash! */
    return 0;
}
```

> [!CAUTION]
> Kode di atas mungkin **tampak bekerja** di beberapa kasus, tetapi ini adalah *undefined behavior* yang sangat berbahaya. Compiler `gcc -Wall` biasanya memperingatkan: `warning: function returns address of local variable`. Jangan abaikan peringatan ini!

**Solusi yang benar:**
1. Kembalikan nilai (bukan pointer) jika datanya kecil.
2. Gunakan `malloc` agar data hidup di *heap* (dibahas di Modul 6.4).
3. Terima pointer dari pemanggil sebagai parameter (seperti pola `hitung_min_max` di atas).

```c
/* ✅ BENAR: alokasi di heap, umur tidak terbatas scope fungsi */
#include <stdlib.h>
int *buat_nilai_benar(void) {
    int *nilai = malloc(sizeof *nilai);
    if (nilai == NULL) return NULL;
    *nilai = 42;
    return nilai;  /* caller wajib free() setelah selesai */
}
```

---

## 📝 Ringkasan

- C **selalu *pass by value*** — tidak ada mekanisme pass by reference bawaan.
- "Pass by reference" di C adalah **simulasi**: kita mengirim alamat variabel (pointer) sebagai nilai.
- Pointer itu sendiri disalin saat masuk ke fungsi; fungsi menggunakannya untuk mengakses memori asli.
- Fungsi `swap` adalah contoh klasik yang membutuhkan pointer agar berfungsi benar.
- Gunakan pointer sebagai parameter output untuk "mengembalikan" lebih dari satu nilai.
- **Jangan kembalikan alamat variabel lokal** — variabel lokal hancur saat fungsi selesai (*dangling pointer*).
- Tandai parameter input dengan `const` agar compiler membantu mencegah modifikasi tidak sengaja.

---

## 🏋️ Latihan Singkat

Kerjakan latihan berikut. Jawaban lengkap ada di [Modul 6.6: Latihan dan Rangkuman](06-Latihan-dan-Rangkuman.md).

1. **(Tracing)** Telusuri panggilan `swap(&a, &b)` dengan `a=5, b=8`. Gambar tabel memori sebelum dan sesudah.

2. **(Cari Bug)** Apa yang salah dengan kode berikut?
   ```c
   int *get_result(void) {
       int result = 100;
       return &result;
   }
   ```

3. **(Cari Bug)** Mengapa fungsi `tambah` ini tidak berfungsi seperti yang diharapkan?
   ```c
   void tambah(int x, int tambahan) { x = x + tambahan; }
   int main(void) {
       int skor = 50;
       tambah(skor, 10);
       printf("%d\n", skor);  /* mengharapkan 60, dapat 50 */
   }
   ```

4. **(Buat Program)** Tulis fungsi `hitung_statistik(const int *arr, int n, int *jumlah, float *rata)` yang menghitung jumlah total dan rata-rata sebuah array, lalu tampilkan hasilnya di `main`.

---

<div align="center">

[⬅️ Sebelumnya: 6.2 Pointer dan Array](02-Pointer-dan-Array.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 6.4: DMA ➡️](04-Dynamic-Memory-Allocation.md)

</div>
