<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=2e7d32&height=150&section=header&text=9.3%20Random%20Access&fontSize=35&fontColor=ffffff&animation=fadeIn&desc=Operasi%20File%20Biner%3A%20fread%2C%20fwrite%2C%20fseek%2C%20dan%20Struct%20I/O&descSize=14&descAlignY=75)

[⬅️ Modul 9.2: Sequential Access](02-Sequential-Access.md) &nbsp;•&nbsp; [📋 Daftar Materi](../Daftar_Materi.md) &nbsp;•&nbsp; [Overview Bab 9 🏠](Bab9-Overview.md)

---

</div>

## 🎯 Tujuan Pembelajaran

Setelah mempelajari modul ini, kamu diharapkan mampu:
1. Memahami konsep file biner dan keuntungan akses acak (*random access*).
2. Melakukan operasi penulisan data mentah ke file biner menggunakan `fwrite()`.
3. Melakukan operasi pembacaan blok data biner menggunakan `fread()`.
4. Menyimpan dan memuat array (statis maupun dinamis) dan variabel bertipe `struct` secara efisien.
5. Memindahkan posisi kursor file secara bebas ke posisi mana pun menggunakan `fseek()` dengan konstanta `SEEK_SET`, `SEEK_CUR`, dan `SEEK_END`.

---

## 💾 Apa itu Random Access & File Biner?

Pada **Random Access** (Akses Acak), file direpresentasikan sebagai sekumpulan blok biner (*raw bytes*) yang ukurannya terstruktur dan dapat diprediksi secara matematis. 

### Perbedaan Utama File Plaintext vs File Binary:

| Karakteristik | File Plaintext (`.txt`) | File Binary (`.bin` / `.dat`) |
|---|---|---|
| **Keterbacaan** | Terbaca langsung oleh manusia di teks editor | Hanya terbaca sebagai kode acak/heksa di teks editor |
| **Representasi Angka** | Angka `12345` disimpan sebagai 5 karakter string (`5 byte`) | Angka `12345` disimpan dalam bentuk integer biner mentah (`4 byte`) |
| **Ukuran Record** | Panjang baris bervariasi (tergantung panjang teks) | Ukuran blok selalu tetap (*fixed size* sesuai `sizeof`) |
| **Kecepatan I/O** | Lebih lambat karena memerlukan konversi string parsing | Sangat cepat karena langsung disalin memori RAM ke disk |
| **Akses Acak** | Sulit melompat ke baris ke-$N$ secara instan | Sangat mudah melompat ke record ke-$N$ dengan $N \times \text{size}$ |

---

## ✍️ Menulis File Biner: `fwrite()`

Fungsi `fwrite()` menyalin blok memori langsung dari pointer variabel ke file biner:

```c
size_t fwrite(const void *ptr, size_t size, size_t count, FILE *stream);
```

### Parameter:
1. `ptr`: Alamat memori data yang akan disalin ke file.
2. `size`: Ukuran tiap blok data dalam byte (biasanya menggunakan `sizeof(...)`).
3. `count`: Jumlah blok data yang akan ditulis.
4. `stream`: Pointer `FILE *` tujuan (yang dibuka dalam mode `"wb"` atau `"ab"`).

### Contoh Program: Menyimpan Data ke `biodata.bin`

```c
#include <stdio.h>

int main() {
    FILE *f;
    char nama[100];
    char prodi[50];
    int angkatan;

    printf("Masukkan nama     : ");
    scanf("%99s", nama);

    printf("Masukkan prodi    : ");
    scanf("%49s", prodi);

    printf("Masukkan angkatan : ");
    scanf("%d", &angkatan);

    f = fopen("biodata.bin", "wb");
    if (f == NULL) {
        printf("[ERROR] Gagal membuka biodata.bin!\n");
        return 1;
    }

    // Menulis blok biner persis sesuai ukuran variabel
    fwrite(nama, sizeof(char), 100, f);
    fwrite(prodi, sizeof(char), 50, f);
    fwrite(&angkatan, sizeof(int), 1, f);

    fclose(f);
    printf("[SUKSES] Data biner berhasil disimpan ke biodata.bin!\n");
    return 0;
}
```

> [!NOTE]
> Ukuran file `biodata.bin` yang dihasilkan akan selalu **tepat 154 byte** (`100 + 50 + 4 byte`), berapa pun panjang teks nama atau nilai angkatan yang kamu masukkan.

---

## 📖 Membaca File Biner: `fread()`

Fungsi `fread()` membaca blok memori dari file biner langsung ke dalam alamat memori variabel penampung:

```c
size_t fread(void *ptr, size_t size, size_t count, FILE *stream);
```

### Contoh Program: Membaca dari `biodata.bin`

```c
#include <stdio.h>

int main() {
    FILE *f;
    char nama[100];
    char prodi[50];
    int angkatan;

    f = fopen("biodata.bin", "rb");
    if (f == NULL) {
        printf("[ERROR] File biodata.bin tidak ditemukan!\n");
        return 1;
    }

    // Membaca blok biner kembali ke variabel
    fread(nama, sizeof(char), 100, f);
    fread(prodi, sizeof(char), 50, f);
    fread(&angkatan, sizeof(int), 1, f);

    fclose(f);

    printf("=== DATA DARI FILE BINER ===\n");
    printf("Nama     : %s\n", nama);
    printf("Prodi    : %s\n", prodi);
    printf("Angkatan : %d\n", angkatan);

    return 0;
}
```

---

## 📦 Baca & Tulis Struct ke File Biner

Salah satu kehebatan terbesar `fwrite()` dan `fread()` adalah kemampuannya menyimpan **seluruh obyek struct dalam 1 baris kode saja**:

```c
#include <stdio.h>
#include <string.h>

typedef struct {
    char nama[50];
    int harga;
    int damage;
    int peluru;
} Senjata;

int main() {
    // 1. Menulis Struct ke File
    Senjata deagle = {"Desert Eagle", 750, 35, 7};
    FILE *fOut = fopen("weapon.dat", "wb");
    if (fOut != NULL) {
        fwrite(&deagle, sizeof(Senjata), 1, fOut);
        fclose(fOut);
        printf("[OK] Struct berhasil disimpan.\n");
    }

    // 2. Membaca Struct dari File
    Senjata senjataBaca;
    FILE *fIn = fopen("weapon.dat", "rb");
    if (fIn != NULL) {
        fread(&senjataBaca, sizeof(Senjata), 1, fIn);
        fclose(fIn);

        printf("\n=== HASIL BACA STRUCT ===\n");
        printf("Nama   : %s\n", senjataBaca.nama);
        printf("Harga  : $%d\n", senjataBaca.harga);
        printf("Damage : %d%%\n", senjataBaca.damage);
        printf("Peluru : %d\n", senjataBaca.peluru);
    }

    return 0;
}
```

---

## 🎯 Navigasi Kursor Acak: `fseek()`

Dalam file biner dengan ukuran record tetap, kita tidak perlu membaca dari awal jika hanya ingin mengambil data ke-10 atau ke-100. Kita bisa melompat langsung (*seek*) menggunakan fungsi **`fseek()`**:

```c
int fseek(FILE *stream, long int offset, int origin);
```

### Parameter Origin:
- **`SEEK_SET`**: Dihitung dari **awal file** (offset $\ge 0$).
- **`SEEK_CUR`**: Dihitung dari **posisi kursor saat ini** (offset bisa positif atau negatif).
- **`SEEK_END`**: Dihitung dari **akhir file** (offset biasanya $\le 0$).

### Contoh: Mengubah Elemen Array Tertentu di File Tanpa Mengubah yang Lain

```c
#include <stdio.h>

int main() {
    FILE *f;
    int nilai[5] = {10, 20, 30, 40, 50};

    // Buat file dan tulis 5 integer
    f = fopen("nilai.bin", "wb+");
    if (f == NULL) return 1;
    fwrite(nilai, sizeof(int), 5, f);

    // Kita ingin mengubah elemen indeks ke-2 (angka 30) menjadi angka 99
    // Lompat langsung ke blok ke-2 (berjarak 2 * sizeof(int) dari awal file)
    fseek(f, 2 * sizeof(int), SEEK_SET);

    int nilaiBaru = 99;
    fwrite(&nilaiBaru, sizeof(int), 1, f);

    // Baca ulang seluruh isi file untuk membuktikan
    rewind(f);
    int hasil[5];
    fread(hasil, sizeof(int), 5, f);

    printf("Isi array setelah fseek & rewrite:\n");
    for (int i = 0; i < 5; i++) {
        printf("Indeks %d: %d\n", i, hasil[i]);
    }
    // Output: 10, 20, 99, 40, 50

    fclose(f);
    return 0;
}
```

---

<div align="center">

[⬅️ Sebelumnya: Modul 9.2 Sequential Access](02-Sequential-Access.md) &nbsp;&nbsp;|&nbsp;&nbsp; [🏠 Overview Bab 9](Bab9-Overview.md) &nbsp;&nbsp;|&nbsp;&nbsp; [📋 Daftar Materi Utama](../Daftar_Materi.md)

</div>

