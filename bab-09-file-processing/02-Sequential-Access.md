<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=2e7d32&height=150&section=header&text=9.2%20Sequential%20Access&fontSize=35&fontColor=ffffff&animation=fadeIn&desc=Operasi%20File%20Plaintext%3A%20fscanf%2C%20fprintf%2C%20dan%20rewind&descSize=14&descAlignY=75)

[⬅️ Modul 9.1: Pengantar](01-Pengantar-File-Processing.md) &nbsp;•&nbsp; [📋 Daftar Materi](../Daftar_Materi.md) &nbsp;•&nbsp; [Modul 9.3: Random Access ➡️](03-Random-Access.md)

---

</div>

## 🎯 Tujuan Pembelajaran

Setelah mempelajari modul ini, kamu diharapkan mampu:
1. Memahami karakteristik akses sekuensial (*sequential access*) pada file teks.
2. Membaca data berformat dari file teks menggunakan `fscanf()` dan `fgets()`.
3. Menulis data berformat ke file teks menggunakan `fprintf()`.
4. Mengembalikan posisi kursor baca/tulis ke awal file menggunakan fungsi `rewind()`.
5. Menangani kondisi akhir file (*End of File / EOF*) dengan benar dan aman.

---

## 📜 Apa itu Sequential Access?

**Sequential Access** (Akses Sekuensial) adalah metode pemrosesan file di mana data dibaca atau ditulis secara berurutan, baris demi baris atau karakter demi karakter, dari awal hingga akhir file.

File berjenis **plaintext** (`.txt`, `.csv`, `.log`) umumnya diakses secara sekuensial. Kita tidak bisa langsung melompat ke baris ke-100 tanpa terlebih dahulu membaca atau melewati 99 baris sebelumnya.

---

## ✍️ Operasi Penulisan (Write): `fprintf()`

Fungsi `fprintf()` bekerja persis seperti `printf()`, perbedaannya adalah `fprintf()` mengarahkan keluaran teks ke *stream* file `FILE *`:

```c
int fprintf(FILE *stream, const char *format, ...);
```

### Contoh Program: Menyimpan Biodata ke `biodata.txt`

```c
#include <stdio.h>

int main() {
    FILE *f;
    char nama[100];
    char prodi[50];
    int angkatan;

    printf("Masukkan nama (tanpa spasi) : ");
    scanf("%99s", nama);

    printf("Masukkan prodi (tanpa spasi): ");
    scanf("%49s", prodi);

    printf("Masukkan angkatan           : ");
    scanf("%d", &angkatan);

    printf("\nMenyimpan ke biodata.txt ...\n");
    f = fopen("biodata.txt", "w");
    if (f == NULL) {
        printf("[ERROR] Tidak dapat membuka file biodata.txt!\n");
        return 1;
    }

    // Menulis data terformat ke dalam file
    fprintf(f, "%s\n", nama);
    fprintf(f, "%s\n", prodi);
    fprintf(f, "%d\n", angkatan);

    fclose(f);
    printf("[SUKSES] Data biodata berhasil disimpan!\n");

    return 0;
}
```

**Hasil isi file `biodata.txt`:**
```text
Adriel_Alfeus
Informatika
2026
```

---

## 📖 Operasi Pembacaan (Read): `fscanf()`

Fungsi `fscanf()` bekerja persis seperti `scanf()`, tetapi membaca karakter dari *stream* file alih-alih dari keyboard (`stdin`):

```c
int fscanf(FILE *stream, const char *format, ...);
```

### Contoh Program: Membaca Biodata dari `biodata.txt`

```c
#include <stdio.h>

int main() {
    FILE *f;
    char nama[100];
    char prodi[50];
    int angkatan;

    printf("Memuat data dari biodata.txt ...\n\n");
    f = fopen("biodata.txt", "r");
    if (f == NULL) {
        printf("[ERROR] File biodata.txt tidak ditemukan atau gagal dibuka.\n");
        return 1;
    }

    // Membaca data sesuai urutan baris
    fscanf(f, "%99s", nama);
    fscanf(f, "%49s", prodi);
    fscanf(f, "%d", &angkatan);

    fclose(f);

    printf("=== HASIL BACA FILE ===\n");
    printf("Nama     : %s\n", nama);
    printf("Prodi    : %s\n", prodi);
    printf("Angkatan : %d\n", angkatan);

    return 0;
}
```

**Output:**
```
Memuat data dari biodata.txt ...

=== HASIL BACA FILE ===
Nama     : Adriel_Alfeus
Prodi    : Informatika
Angkatan : 2026
```

---

## 🔄 Membaca Seluruh Isi File Hingga EOF

Saat membaca file teks yang panjang barisnya tidak diketahui, kita dapat memanfaatkan nilai kembalian `fscanf` yang menghasilkan konstanta **`EOF`** ketika mencapai akhir file:

```c
#include <stdio.h>

int main() {
    FILE *f = fopen("daftar_angka.txt", "r");
    if (f == NULL) return 1;

    int angka;
    int total = 0;

    // Loop berjalan sampai fscanf tidak menemukan data lagi (EOF)
    while (fscanf(f, "%d", &angka) == 1) {
        printf("Terbaca: %d\n", angka);
        total += angka;
    }

    printf("Total penjumlahan: %d\n", total);
    fclose(f);
    return 0;
}
```

---

## ⏪ Mereset Kursor File: `rewind()`

Setiap kali operasi baca atau tulis dilakukan, kursor internal file (*file position indicator*) akan bergerak maju. Jika kita ingin membaca kembali isi file dari baris pertama tanpa harus menutup dan membuka ulang file, gunakan fungsi **`rewind()`**:

```c
void rewind(FILE *stream);
```

```c
#include <stdio.h>

int main() {
    FILE *f = fopen("catatan.txt", "w+"); // Mode read and write
    if (f == NULL) return 1;

    fprintf(f, "Baris Pertama\n");
    fprintf(f, "Baris Kedua\n");

    // Mengembalikan posisi kursor ke karakter paling awal file
    rewind(f);

    char kata[50];
    fscanf(f, "%49s", kata);
    printf("Kata pertama yang dibaca ulang: %s\n", kata); // "Baris"

    fclose(f);
    return 0;
}
```

> [!WARNING]
> Berhati-hatilah saat melakukan penulisan (`fprintf`) tepat setelah `rewind()` pada file yang sudah ada, karena karakter baru akan menimpa karakter lama bit demi bit pada posisi tersebut!

---

<div align="center">

[⬅️ Sebelumnya: Modul 9.1 Pengantar File Processing](01-Pengantar-File-Processing.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 9.3: Random Access ➡️](03-Random-Access.md)

</div>

