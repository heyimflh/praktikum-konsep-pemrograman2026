<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=00838f&height=150&section=header&text=7.1%20Printf%20Formats&fontSize=35&fontColor=ffffff&animation=fadeIn&desc=Format%20Specifier%2C%20Flag%2C%20Width%2C%20dan%20Precision&descSize=14&descAlignY=75)

[⬅️ Overview Bab 7](Bab7-Overview.md) &nbsp;•&nbsp; [📋 Daftar Materi](../Daftar_Materi.md) &nbsp;•&nbsp; [Modul 7.2: Scanf Formats ➡️](02-Scanf-Formats.md)

---

</div>

## 🎯 Tujuan Pembelajaran

Setelah mempelajari modul ini, kamu diharapkan mampu:

1. Menjelaskan anatomi lengkap sintaks penentu format (*format specifier*) pada fungsi `printf()`.
2. Memilih penentu format yang tepat untuk berbagai tipe data (bilangan bulat, desimal, karakter, string, pointer, heksadesimal).
3. Mengatur tata letak tampilan teks dan angka menggunakan *flag* (`-`, `+`, `0`, `#`, spasi), *width*, dan *precision*.
4. Memanfaatkan *wildcard* (`*`) untuk menentukan lebar dan presisi keluaran secara dinamis saat *runtime*.
5. Menjelaskan akibat specifier yang **tidak cocok** dengan argumen, dan menghindari celah *format string*.
6. Merancang keluaran terminal yang rapi seperti tabel laporan atau struk transaksi.

> [!NOTE]
> **Cara mengompilasi semua contoh di bab ini.** Biasakan selalu menyalakan peringatan compiler:
> ```bash
> gcc -std=c99 -Wall -Wextra -o program program.c
> ```
> Compiler akan memberi tahu jika specifier tidak cocok dengan tipe argumen (`-Wformat` sudah aktif di `-Wall`).

---

## 🔍 Mengapa `printf` Membutuhkan Format?

Fungsi `printf()` berasal dari kata **print formatted**. Saat kamu memanggil:

```c
printf("Halo %s, skor kamu adalah %d dengan akurasi %.2f%%\n", nama, skor, akurasi);
```

`printf` adalah fungsi **variadic** (jumlah argumennya tidak tetap). Akibatnya, fungsi ini **tidak punya cara untuk mengetahui tipe maupun jumlah argumen** selain dari string format yang kamu berikan. Saat program berjalan, `printf` membaca string format dari kiri ke kanan, dan setiap kali bertemu specifier, ia mengambil argumen berikutnya dengan *menganggap* argumen itu bertipe sesuai specifier tersebut.

Jadi format specifier berfungsi sebagai:

- **Penunjuk tipe:** berapa *byte* yang dibaca dan bagaimana representasi biner itu diterjemahkan menjadi teks.
- **Placeholder berurutan:** specifier pertama diisi argumen pertama, specifier kedua diisi argumen kedua, dan seterusnya.

---

## 🧩 Anatomi Format Specifier

Setiap penentu format pada fungsi `printf()` mengikuti urutan aturan berikut:

```
%[flags][width][.precision][length]specifier
```

```
┌─── % ───┬─────── [flags] ───────┬────── [width] ──────┬──── [.precision] ────┬───── [length] ─────┬── specifier ──┐
│ Simbol  │ Penyesuaian tampilan  │ Lebar minimum kolom │ Jumlah digit desimal │ Ukuran memori data │ Jenis data    │
│ Pembuka │ (- , + , 0 , # , ' ') │ (misal: 10, *)      │ / max karakter (.2)  │ (h, l, ll, z, dll) │ (d, f, s, x)  │
└─────────┴───────────────────────┴─────────────────────┴──────────────────────┴────────────────────┴───────────────┘
```

> [!NOTE]
> Semua bagian di dalam tanda kurung siku `[...]` bersifat **opsional**. Hanya simbol persentase `%` dan `specifier` yang wajib ada!

---

## 📊 1. Daftar Format Specifier

| Specifier | Tipe Argumen | Keterangan & Contoh Tampilan |
|:---:|:---|:---|
| `%d` atau `%i` | `int` | Bilangan bulat desimal bertanda (`-42`, `100`). Pada `printf`, `%d` dan `%i` identik |
| `%u` | `unsigned int` | Bilangan bulat tanpa tanda (`3000000000`) |
| `%f` | `double` | Pecahan desimal (default 6 digit di belakang koma: `3.141593`) |
| `%lf` | `double` | Pada `printf`, setara dengan `%f` karena `float` otomatis dipromosikan menjadi `double`. *(Awas: pada `scanf` keduanya **berbeda**, lihat Modul 7.2!)* |
| `%e` / `%E` | `double` | Notasi ilmiah (`1.234000e+04`) |
| `%g` / `%G` | `double` | Memilih `%f` atau `%e` yang lebih ringkas (maksimal 6 angka signifikan) tanpa nol tak berguna di belakang |
| `%c` | `int` (char) | Karakter tunggal (`'A'`, `'7'`, `'#'`) |
| `%s` | `char *` | String berakhiran karakter null `\0` (`"Informatika"`) |
| `%p` | `void *` | Alamat memori dalam heksadesimal (`0x7ffde5b2a340`). Wajib di-*cast*: `(void *)ptr` |
| `%x` / `%X` | `unsigned int` | Heksadesimal huruf kecil (`1a3f`) atau kapital (`1A3F`) |
| `%o` | `unsigned int` | Oktal basis 8 (`755`) |
| `%%` | *(tanpa argumen)* | Mencetak karakter `%` itu sendiri |

Contoh singkat tiga specifier yang sering membingungkan:

```c
printf("%e\n", 12340.0);       // 1.234000e+04
printf("%g\n", 0.0001234);     // 0.0001234
printf("%g\n", 1234567.0);     // 1.23457e+06  (hanya 6 angka signifikan)
```

---

## 🚩 2. Menguasai Flags (Modifikasi Tampilan)

Flags ditempatkan tepat setelah tanda `%` untuk mengubah perilaku format. Tanda kurung siku `[ ]` pada contoh di bawah dipakai sebagai "pagar" supaya kamu bisa melihat batas kolom dengan jelas.

| Flag | Arti & Fungsi | Contoh Kode | Output |
|:---:|:---|:---|:---|
| `-` | **Rata kiri.** Default `printf` adalah rata kanan | `printf("[%-10s]", "Budi");` | `[Budi      ]` |
| `+` | **Paksa tanda.** Tampilkan `+` untuk nilai positif | `printf("%+d %+d", 42, -42);` | `+42 -42` |
| *(spasi)* | **Spasi untuk tanda.** Bilangan positif diberi spasi di depan agar sejajar dengan bilangan negatif | `printf("[% d] [% d]", 42, -42);` | `[ 42] [-42]` |
| `0` | **Zero-padding.** Sisa lebar kolom diisi `0`, bukan spasi | `printf("%05d", 7);` | `00007` |
| `#` | **Bentuk alternatif**<br>• `%x`/`%X`: awalan `0x`/`0X`<br>• `%o`: awalan `0`<br>• `%f`: selalu tampilkan titik desimal | `printf("%#x", 255);` | `0xff` |

Beberapa aturan kombinasi yang perlu diingat:

- Jika `+` dan spasi dipakai bersamaan, flag `+` yang menang.
- Jika `-` dan `0` dipakai bersamaan, flag `0` diabaikan (rata kiri tidak diisi nol).
- Flag `0` pada bilangan bulat diabaikan jika *precision* ikut ditulis (misal `%05.3d`).

> [!WARNING]
> **Koreksi miskonsepsi:** tanpa flag `-`, keluaran secara default **rata kanan**. Flag `-` dipakai ketika kamu ingin kolom **rata kiri**.

---

## 📏 3. Field Width (Lebar Kolom) & Precision (Presisi)

### A. Field Width (Lebar Minimum)

Angka bulat setelah flag menentukan **jumlah karakter minimum** yang dialokasikan. Jika data lebih pendek, sisanya diisi spasi (atau nol jika flag `0` aktif). Jika data lebih panjang, `printf` **tidak memotong data**, melainkan melebarkan kolom otomatis. Karena itu tabel bisa "bergeser" kalau ada data yang melebihi lebar kolom.

```c
printf("|%10s|\n", "C");      // |         C|  (rata kanan, 10 slot)
printf("|%-10s|\n", "C");     // |C         |  (rata kiri, 10 slot)
printf("|%08d|\n", 1234);     // |00001234|    (zero padding, 8 slot)
printf("|%3d|\n", 123456);    // |123456|      (lebih panjang dari 3: tidak dipotong)
```

### B. Precision (Titik `.` diikuti angka)

Efek precision berbeda tergantung tipe datanya:

1. **Bilangan pecahan (`%f`, `%e`):** jumlah digit **di belakang koma** (default 6). Hasilnya dibulatkan.
   ```c
   double pi = 3.1415926535;
   printf("%.2f\n", pi); // 3.14
   printf("%.4f\n", pi); // 3.1416
   printf("%.0f\n", pi); // 3
   ```

2. **String (`%s`):** jumlah karakter **maksimum** yang dicetak (memotong string).
   ```c
   printf("%.4s\n", "Informatika"); // Info
   ```

3. **Bilangan bulat (`%d`):** jumlah digit **minimum**; kekurangan diisi nol di depan.
   ```c
   printf("%.4d\n", 5); // 0005
   ```

Width dan precision bisa digabung. Perhatikan bahwa width menghitung **seluruh** karakter termasuk titik desimal dan tanda:

```c
printf("[%8.3f]\n", 3.14159);   // [   3.142]   (8 karakter total)
printf("[%-8.1f]\n", 3.14159);  // [3.1     ]
printf("[%08.2f]\n", -3.14159); // [-0003.14]
```

> [!WARNING]
> **Hati-hati dengan pembulatan pecahan.** Komputer menyimpan `double` dalam biner, sehingga sebagian besar pecahan desimal tersimpan sebagai pendekatan.
> ```c
> printf("%.2f\n", 2.675);        // 2.67  (bukan 2.68!)
> printf("%.17f\n", 0.1 + 0.2);   // 0.30000000000000004
> ```
> Karena itu, untuk perhitungan uang di program sungguhan, banyak developer menyimpan nilai dalam satuan bulat (misal sen/rupiah) alih-alih `double`. Di materi ini kita tetap memakai `double` demi kesederhanaan.

---

## ⚡ 4. Dynamic Width & Precision dengan Wildcard `*`

Gunakan tanda bintang `*` jika lebar kolom atau presisi ditentukan variabel saat program berjalan. Nilai `*` diambil dari argumen **sebelum** nilai yang dicetak, dengan urutan: width, lalu precision, lalu data.

```c
#include <stdio.h>

int main(void) {
    int lebar = 12;
    int desimal = 3;
    double nilai = 45.67891;

    printf("|%*.*f|\n", lebar, desimal, nilai);
    // |      45.679|   (lebar 12, 3 angka di belakang koma)

    printf("|%.*s|\n", 4, "Informatika");
    // |Info|           (precision pada string juga bisa dinamis)

    return 0;
}
```

### Trik: Mencetak Teks Rata Tengah

`printf` tidak punya flag untuk rata tengah, tetapi kamu bisa membuatnya dengan `%*s` dan string kosong sebagai "spasi sebanyak N":

```c
#include <stdio.h>
#include <string.h>

void cetak_tengah(const char *teks, int lebar_total) {
    int pad = (lebar_total - (int)strlen(teks)) / 2;
    if (pad < 0) pad = 0;
    printf("%*s%s\n", pad, "", teks);   // %*s dengan "" = cetak 'pad' spasi
}
```

---

## 🎛️ 5. Length Modifiers (Pengubah Ukuran Tipe)

Ketika tipe data berbeda dari `int`/`double` standar, tambahkan *length modifier* sebelum huruf specifier:

| Modifier | Digunakan Bersama | Tipe Data yang Dituju | Contoh |
|:---:|:---:|:---|:---|
| `hh` | `d`, `i`, `u`, `x` | `signed char` / `unsigned char` | `%hhd` |
| `h` | `d`, `i`, `u`, `x` | `short` / `unsigned short` | `%hd` |
| `l` | `d`, `i`, `u`, `x` | `long` / `unsigned long` | `%ld`, `%lu`, `%lx` |
| `ll` | `d`, `i`, `u`, `x` | `long long` (64-bit) | `%lld`, `%llu` |
| `L` | `f`, `e`, `g` | `long double` | `%Lf` |
| `z` | `d`, `u`, `x` | `size_t` (hasil `sizeof`, `strlen`) | `%zu` |

```c
long long int populasi_dunia = 8000000000LL;
size_t ukuran = sizeof(populasi_dunia);

printf("Populasi: %lld jiwa\n", populasi_dunia);
printf("Ukuran variabel: %zu byte\n", ukuran);
```

> [!TIP]
> Memakai `%d` untuk `long long` adalah bug klasik: nilai besar akan terpotong dan tercetak salah. Biasakan mencocokkan modifier dengan tipe variabelnya.

---

## 🔢 6. Heksadesimal & Oktal dengan Padding

Kode warna, alamat memori, dan nilai register sering ditampilkan dengan jumlah digit tetap (`0x00FF`). Ada dua cara:

```c
printf("%#06x\n", 255);   // 0x00ff   (lebar 6 SUDAH termasuk awalan "0x")
printf("0x%04X\n", 255);  // 0x00FF   (awalan ditulis manual, 4 digit hex)
printf("%08X\n", 48879);  // 0000BEEF
printf("%#o\n", 8);       // 010
```

Perhatikan perbedaannya: pada `%#06x`, angka `6` menghitung `0x` juga, sehingga hanya tersisa 4 digit untuk angkanya. Jika ingin awalan huruf kapital sekaligus, cara kedua (`0x%04X`) lebih mudah dikontrol.

Contoh format waktu dua digit:

```c
printf("%02d:%02d:%02d\n", 9, 5, 1);   // 09:05:01
```

---

## 🔄 7. Return Value dari Fungsi `printf()`

`printf()` mengembalikan **jumlah karakter yang berhasil dicetak**. Jika terjadi kegagalan output, nilai kembaliannya negatif.

```c
#include <stdio.h>

int main(void) {
    int jumlah_karakter = printf("Halo Informatika UNS!\n");
    printf("Total karakter yang baru saja dicetak: %d karakter\n", jumlah_karakter);
    return 0;
}
```

**Output:**
```text
Halo Informatika UNS!
Total karakter yang baru saja dicetak: 22 karakter
```

*(Karakter `\n` juga dihitung sebagai 1 karakter.)*

---

## 🛑 8. Ketika Format Tidak Cocok: *Undefined Behavior*

Karena `printf` hanya "percaya" pada string format, kesalahan pasangan specifier dan argumen **tidak dicegah** saat program berjalan. Hasilnya *undefined behavior* (perilaku tak terdefinisi): bisa mencetak angka sampah, salah membaca argumen berikutnya, atau program *crash*.

```c
printf("%d\n", 3.14);        // ❌ %d dipasangkan dengan double → angka sampah
printf("%f\n", 42);          // ❌ %f dipasangkan dengan int    → angka sampah
printf("%s\n", 42);          // ❌ %s menganggap 42 sebagai alamat → kemungkinan crash
printf("%d %d\n", 10);       // ❌ argumen kurang → membaca sampah
printf("%lld\n", 5);         // ❌ 5 bertipe int, bukan long long
```

Cara mencegahnya:

1. Kompilasi dengan `-Wall -Wextra`. GCC akan memberi peringatan seperti `format '%d' expects argument of type 'int', but argument 2 has type 'double'`.
2. Jangan abaikan *warning*. Anggap setiap peringatan format sebagai *bug* yang harus diperbaiki.
3. Gunakan *casting* eksplisit jika perlu, misalnya `printf("%d\n", (int)3.14)` atau `printf("%p\n", (void *)&x)`.

### Celah Keamanan: *Format String Vulnerability*

Jangan pernah menjadikan **input pengguna** sebagai string format:

```c
char teks[100];
fgets(teks, sizeof teks, stdin);

printf(teks);          // ❌ BERBAHAYA: jika pengguna mengetik "%s%s%s" atau "%x %x",
                       //    printf akan membaca memori yang tidak seharusnya
printf("%s", teks);    // ✅ AMAN: teks diperlakukan sebagai data, bukan format
fputs(teks, stdout);   // ✅ AMAN juga
```

Aturan emasnya: **string format sebaiknya selalu berupa literal tetap** yang kamu tulis sendiri. Untuk kompilasi yang lebih ketat, tambahkan `-Wformat-security`.

---

## 💻 9. Studi Kasus: Struk Kasir yang Sejajar Sempurna

Dengan kombinasi `%-Ns`, `%Nd`, dan `%N.2f`, tabel menjadi mudah dibuat. Trik penting: **lebar total tabel adalah jumlah semua lebar kolom ditambah spasi pemisah**. Pada contoh ini `4 + 1 + 20 + 1 + 5 + 1 + 12 + 1 + 12 = 57` karakter, sehingga garis pembatas juga harus 57 karakter. Agar tidak salah hitung, garis dibuat oleh fungsi dengan konstanta `LEBAR`.

```c
#include <stdio.h>
#include <string.h>

#define LEBAR 57

static void garis(char c) {
    for (int i = 0; i < LEBAR; i++) putchar(c);
    putchar('\n');
}

static void cetak_tengah(const char *teks) {
    int pad = (LEBAR - (int)strlen(teks)) / 2;
    if (pad < 0) pad = 0;
    printf("%*s%s\n", pad, "", teks);
}

int main(void) {
    const char *item[]  = {"Flashdisk 64GB", "Mouse Wireless", "Kabel HDMI 2.0"};
    int qty[]           = {2, 1, 3};
    double harga[]      = {85000.00, 125500.50, 45000.00};
    int n = 3;
    double total_bayar = 0.0;

    garis('=');
    cetak_tengah("STRUK TRANSAKSI TOKO ELEKTRONIK");
    garis('=');

    // Header: teks rata kiri, angka rata kanan (sama seperti data di bawahnya)
    printf("%-4s %-20s %5s %12s %12s\n", "NO", "NAMA BARANG", "QTY", "HARGA (RP)", "TOTAL (RP)");
    garis('-');

    for (int i = 0; i < n; i++) {
        double subtotal = qty[i] * harga[i];
        total_bayar += subtotal;
        printf("%-4d %-20s %5d %12.2f %12.2f\n", i + 1, item[i], qty[i], harga[i], subtotal);
    }

    garis('-');
    printf("%-31s %12s %12.2f\n", "TOTAL KESELURUHAN", "Rp", total_bayar);
    garis('=');

    return 0;
}
```

**Tampilan hasil di terminal:**

```text
=========================================================
             STRUK TRANSAKSI TOKO ELEKTRONIK
=========================================================
NO   NAMA BARANG            QTY   HARGA (RP)   TOTAL (RP)
---------------------------------------------------------
1    Flashdisk 64GB           2     85000.00    170000.00
2    Mouse Wireless           1    125500.50    125500.50
3    Kabel HDMI 2.0           3     45000.00    135000.00
---------------------------------------------------------
TOTAL KESELURUHAN                         Rp    430500.50
=========================================================
```

Semua titik desimal sejajar lurus secara vertikal karena tiap kolom angka memakai lebar tetap dan rata kanan.

---

## 📌 Rangkuman & Tips Praktikum

1. Gunakan `%d` untuk `int`, `%f` untuk pecahan, `%c` untuk karakter, `%s` untuk string, dan **pasangkan dengan tipe yang benar**.
2. Tanpa flag = rata kanan; dengan flag `-` = rata kiri.
3. Batasi digit pecahan dengan `%.2f` agar keluaran rapi.
4. Gunakan `%02d` untuk format waktu/tanggal, dan `%#06x` atau `0x%04X` untuk heksadesimal berpadding.
5. Cetak karakter `%` dengan `%%`.
6. Lebar tabel = jumlah lebar kolom + spasi pemisah. Bungkus garis pembatas dalam fungsi atau konstanta.
7. Selalu kompilasi dengan `-Wall -Wextra`, dan jangan pernah memakai input pengguna sebagai string format.

---

<div align="center">

[⬅️ Kembali ke Overview Bab 7](Bab7-Overview.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke Modul 7.2: Scanf Formats ➡️](02-Scanf-Formats.md)

</div>
