[⬅️ Kembali ke README Bab 2](README.md)

# 2.1 Pengantar Program C & Variabel

## Struktur Dasar Program C

Setiap program C, sesederhana apapun, punya kerangka yang sama. Perhatikan program *Hello World* berikut:

```c

int main() {            // (2) Fungsi utama
    printf("Hello, World!\n");  // (3) Statement
    return 0;            // (4) Nilai kembalian
}
```

Penjelasan tiap bagian:

1. **`#include <stdio.h>`** — memuat *library* standar input/output. Tanpa baris ini, `printf` dan `scanf` tidak bisa dipakai. Perhatikan: baris ini **tidak diakhiri titik koma**, karena bukan statement C biasa, melainkan instruksi untuk preprocessor.
2. **`int main()`** — titik masuk (*entry point*) program. Compiler selalu mulai mengeksekusi dari sini, apapun urutan fungsi lain di file.
3. **Statement** — instruksi yang dijalankan program, selalu diakhiri titik koma (`;`).
4. **`return 0;`** — mengembalikan kode status ke sistem operasi. `0` berarti program berjalan sukses.

> 💡 Komentar ditulis dengan `//` untuk satu baris, atau `/* ... */` untuk banyak baris. Compiler mengabaikan komentar sepenuhnya — gunakan untuk menjelaskan *kenapa*, bukan sekadar *apa*.

## Variabel dan Tipe Data

Variabel adalah "kotak" bernama yang menyimpan nilai di memori. Di C, **setiap variabel wajib punya tipe data** dan harus dideklarasikan sebelum dipakai.

| Kategori | Tipe Data | Ukuran (umum) | Contoh Nilai |
|---|---|---|---|
| Bilangan bulat | `int` | 4 byte | `42`, `-7` |
| Bilangan bulat kecil | `short` | 2 byte | `100` |
| Bilangan bulat besar | `long` | 8 byte | `9000000000` |
| Karakter | `char` | 1 byte | `'A'`, `'z'` |
| Bilangan real (presisi tunggal) | `float` | 4 byte | `3.14f` |
| Bilangan real (presisi ganda) | `double` | 8 byte | `3.14159265` |
| Bilangan bulat non-negatif | `unsigned int` | 4 byte | `0`, `255` |

### Deklarasi & Inisialisasi

```c
int umur;              // deklarasi tanpa nilai (isinya "sampah"/undefined)
int umur = 20;          // deklarasi + inisialisasi langsung
int a = 5, b = 10, c;   // banyak variabel sekaligus, tipe sama
char inisial = 'F';     // char pakai petik satu, bukan dua
float tinggi = 168.5f;  // huruf 'f' menandakan literal float, bukan double
```

**Aturan penamaan variabel:**
- Boleh berisi huruf, angka, underscore (`_`) — **tidak boleh diawali angka**
- Case-sensitive: `nilai` ≠ `Nilai`
- Tidak boleh pakai kata kunci C (`int`, `for`, `return`, dst)
- Gunakan nama yang deskriptif: `jumlahSiswa` jauh lebih baik daripada `x`

## Basic Output — `printf()`

```c
#include <stdio.h>
int main() {
    char nama[20] = "Fakhri";
    int umur = 21;
    float ipk = 3.75f;

    printf("Nama: %s, Umur: %d, IPK: %.2f\n", nama, umur, ipk);
    return 0;
}
```

**Output:**
```
Nama: Fakhri, Umur: 21, IPK: 3.75
```

Format specifier (placeholder) yang wajib dihafal:

| Specifier | Tipe Data | Keterangan |
|---|---|---|
| `%d` | `int` | Bilangan bulat |
| `%f` | `float`/`double` | Bilangan real (default 6 desimal) |
| `%.2f` | `float`/`double` | Bilangan real dibulatkan 2 desimal |
| `%c` | `char` | Satu karakter |
| `%s` | `char[]` (string) | Rangkaian karakter |
| `%ld` | `long` | Bilangan bulat panjang |
| `\n` | — | Pindah baris (newline) |

## Basic Input — `scanf()`

```c
#include <stdio.h>
int main() {
    int angka;
    float harga;

    printf("Masukkan angka: ");
    scanf("%d", &angka);          // wajib pakai & (alamat memori variabel)

    printf("Masukkan harga: ");
    scanf("%f", &harga);          // scanf TIDAK pakai & untuk float/double? -> tetap pakai &!

    printf("Anda memasukkan: %d dan %.2f\n", angka, harga);
    return 0;
}
```

> ⚠️ **Kesalahan paling umum pemula:** lupa tanda `&` (address-of operator) di `scanf()` untuk tipe numerik. Tanpa `&`, program bisa crash atau memberi hasil aneh, karena `scanf` butuh **alamat memori** variabel, bukan nilainya. String (`char[]`) adalah pengecualian — nama array sudah otomatis merujuk ke alamatnya.

## 🧪 Latihan Cepat

Coba kerjakan langsung di editor kalian sebelum lanjut ke sub-materi berikutnya:

1. Buat program yang mendeklarasikan variabel nama (string), NIM (int), dan IPK (float) milikmu sendiri, lalu cetak ketiganya dalam satu baris menggunakan `printf`.
2. Buat program yang meminta pengguna memasukkan dua bilangan bulat, lalu tampilkan hasil penjumlahan, pengurangan, perkalian, dan pembagiannya.
3. Perhatikan program berikut — tebak dulu outputnya di kertas, baru jalankan untuk verifikasi:
   ```c
   int a = 7;
   int b = 2;
   printf("%d\n", a / b);     // pembagian integer!
   printf("%.2f\n", (float)a / b);  // type casting
   ```
   Kenapa hasil keduanya berbeda? (Jawaban ada di bagian Operator, sub-materi 2.3)

---
⬅️ [Kembali ke README Bab 2](README.md) | ➡️ [Lanjut: Algoritma, Pseudocode & Source Code](02-Algoritma-Pseudocode-SourceCode.md)
