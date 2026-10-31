<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=004d40&height=150&section=header&text=7.4%20Latihan%20%26%20Studi%20Kasus&fontSize=32&fontColor=ffffff&animation=fadeIn&desc=Cheatsheet%2C%20Latihan%20Bertingkat%2C%20dan%20Pembahasan%20Lengkap&descSize=14&descAlignY=75)

[⬅️ Modul 7.3: String I/O & Input Aman](03-String-IO-dan-Input-Aman.md) &nbsp;•&nbsp; [📋 Daftar Materi](../Daftar_Materi.md) &nbsp;•&nbsp; [Overview Bab 7 🏠](Bab7-Overview.md)

---

</div>

## 📑 Quick Reference Cheatsheet Formatted I/O

Kompilasi semua latihan dengan: `gcc -std=c99 -Wall -Wextra -o latihan latihan.c`

### 1. Printf Specifier & Flags

Tanda `[ ]` pada kolom contoh adalah "pagar" agar batas kolom terlihat.

| Sintaks | Contoh Penggunaan | Hasil Keluaran | Catatan |
|---|---|---|---|
| `%-10s` | `printf("[%-10s]", "C")` | `[C         ]` | Rata kiri, lebar 10 |
| `%10s` | `printf("[%10s]", "C")` | `[         C]` | Rata kanan, lebar 10 |
| `%+d` | `printf("%+d", 42)` | `+42` | Selalu tampilkan tanda |
| `%05d` | `printf("%05d", 7)` | `00007` | Zero-padding hingga 5 digit |
| `%.2f` | `printf("%.2f", 3.14159)` | `3.14` | Tepat 2 angka di belakang koma |
| `%8.3f` | `printf("[%8.3f]", 3.14159)` | `[   3.142]` | Lebar 8, 3 desimal |
| `%.3s` | `printf("%.3s", "Algoritma")` | `Alg` | Maksimal 3 karakter |
| `%*d` | `printf("[%*d]", 6, 42)` | `[    42]` | Lebar dari argumen |
| `%#x` | `printf("%#x", 255)` | `0xff` | Heksadesimal dengan awalan |
| `%#06x` | `printf("%#06x", 255)` | `0x00ff` | Heksa dengan padding (lebar termasuk `0x`) |
| `%zu` | `printf("%zu", sizeof(int))` | `4` | Untuk `size_t` |
| `%%` | `printf("100%%")` | `100%` | Karakter persen literal |

### 2. Scanf Specifier & Pola

| Pola Sintaks | Contoh Penggunaan | Efek & Manfaat |
|---|---|---|
| `%d` / `%lf` | `scanf("%d", &n)` / `scanf("%lf", &x)` | `int` pakai `%d`, **`double` wajib `%lf`** |
| `%19s` | `scanf("%19s", str)` | Mencegah buffer overflow |
| ` %c` | `scanf(" %c", &ch)` | Anti newline trap |
| ` %49[^\n]` | `scanf(" %49[^\n]", teks)` | Membaca string berspasi |
| `%*c` / `%*[/-]` | `scanf("%d%*[/-]%d", &a, &b)` | Membuang pemisah |
| `d:d:d` literal | `scanf("%d:%d:%d", &j, &m, &d)` | Pemisah harus persis `:` |
| `== 1` / `== EOF` | `if (scanf("%d", &n) == 1)` | Validasi, dan bedakan `0` dengan `EOF` |

### 3. Pola Input Aman (Modul 7.3)

| Tujuan | Pola |
|---|---|
| Baca 1 baris | `fgets(buf, sizeof buf, stdin)` lalu hapus `\n` |
| Parse isi baris | `sscanf(baris, "%d:%d:%d %1s", &a, &b, &c, sisa) == 3` |
| Angka dengan cek error | `strtol` / `strtod` + `errno` + cek `*akhir` |
| Rakit string | `snprintf(buf, sizeof buf, "TRX-%04d", n)` |
| Pesan error | `fprintf(stderr, "...")` |

---

## 🎯 Soal Latihan Bertingkat

---

### 🟢 Level 1: Pemahaman Dasar & Formatting

#### Soal 1.1: Tabel Rekapitulasi Nilai Praktikum

Buat program C yang menampilkan data 3 mahasiswa dalam tabel rapi:

1. Kolom: **NIM** (lebar 10, rata kiri), **NAMA** (lebar 18, rata kiri), **TUGAS**, **UTS**, **UAS** (masing-masing lebar 6, rata kanan), dan **AKHIR** (lebar 12 dengan 2 angka di belakang koma).
2. Nilai Akhir = `(Tugas * 0.3) + (UTS * 0.3) + (UAS * 0.4)`.
3. Garis pembatas harus **tepat sama panjang** dengan lebar tabel. *Petunjuk:* hitung dulu lebar total dari lebar kolom + spasi pemisah.

#### Soal 1.2: Multi-Base Converter Display

Minta satu bilangan bulat **tidak negatif**, lalu cetak dalam 5 representasi: desimal, heksadesimal kecil dengan awalan `0x`, heksadesimal kapital dengan awalan `0X`, oktal dengan awalan `0`, dan heksadesimal 4 digit berpadding nol dengan awalan `0x` (misal `0x00FF`).

#### Soal 1.3: Tebak Output

Tentukan keluaran tiap baris **tanpa** menjalankan program, lalu cocokkan dengan compiler.

```c
printf("[%6.2f]\n", 3.14159);          // (a)
printf("[%-8s]\n", "UNS");             // (b)
printf("[%+05d]\n", 42);               // (c)
printf("[%#o] [%#X]\n", 8, 255);       // (d)
printf("[%.3s]\n", "Algoritma");       // (e)
printf("[%*d]\n", 6, 42);              // (f)
printf("%d%%\n", 75);                  // (g)
printf("%c%c%c\n", 72, 105, 33);       // (h)
```

#### Soal 1.4: Judul Rata Tengah

Tulis fungsi `void judul(const char *teks, int lebar)` yang mencetak `teks` rata tengah dalam garis `=` sepanjang `lebar`, **tanpa** menghitung spasi manual dan tanpa perulangan untuk spasi (*petunjuk:* wildcard `%*s`).

---

### 🟡 Level 2: Manipulasi Format & Parsing

#### Soal 2.1: Parser Format Waktu (HH:MM:SS)

Minta waktu `HH:MM:SS` (misal `08:30:45`) dalam **satu baris** memakai `scanf()` dengan karakter literal `:` pada format. Validasi jam `0-23`, menit `0-59`, detik `0-59`. Jika valid, cetak total **detik sejak tengah malam**; jika tidak, cetak pesan error ke `stderr`.

#### Soal 2.2: Perbaikan Bug "The Ghost Enter"

Menu berikut selalu melewati input `member`:

```c
#include <stdio.h>

int main(void) {
    int paket;
    char member;

    printf("Pilih nomor paket (1-3): ");
    scanf("%d", &paket);

    printf("Apakah Anda memiliki kartu member (Y/N)? ");
    scanf("%c", &member); // BUG: selalu terlewati!

    printf("Paket: %d, Member: %c\n", paket, member);
    return 0;
}
```

1. Jelaskan mengapa `scanf("%c", &member)` terlewati.
2. Tuliskan **dua** cara perbaikannya.

#### Soal 2.3: Temukan Bug `double`

Program ini mencetak angka aneh untuk suhu. Cari penyebabnya dan perbaiki:

```c
#include <stdio.h>

int main(void) {
    double suhu;
    printf("Suhu (C): ");
    scanf("%f", &suhu);
    printf("Suhu: %.1f C\n", suhu);
    return 0;
}
```

#### Soal 2.4: Celah Format String

Program "echo" ini berbahaya jika pengguna mengetik `%x %x %x`. Jelaskan alasannya dan perbaiki:

```c
#include <stdio.h>

int main(void) {
    char pesan[100];
    fgets(pesan, sizeof pesan, stdin);
    printf(pesan);
    return 0;
}
```

---

### 🔴 Level 3: Studi Kasus Nyata & Mini-Project

#### Soal 3.1: Mini Billing System Toko Buku

Rancang program kasir interaktif:

1. Minta **Nama Kasir** (berspasi).
2. Minta **jumlah jenis buku** (1-5). Input huruf, angka negatif, atau di luar jangkauan harus ditolak dan diminta ulang tanpa *infinite loop*, termasuk saat input habis (EOF).
3. Untuk tiap buku, minta: judul (berspasi), jumlah eksemplar (bulat > 0), dan harga satuan (desimal > 0).
4. Hitung subtotal, PPN 11%, dan total bayar.
5. Cetak **struk resmi** dengan kolom rapi, titik desimal sejajar, dan **tanggal serta jam transaksi** saat dicetak (ambil dari jam sistem).

#### Soal 3.2: Parser Data CSV Sederhana

Diberikan data teks berformat `NIM,Nama,Nilai`. Gunakan `sscanf` dengan scanset untuk mem-parsing setiap baris, cetak tabel rapi untuk baris yang valid, laporkan baris yang tidak valid ke `stderr`, dan hitung rata-rata nilai dari baris valid.

```c
const char *data[] = {
    "M0526001,Aditya Pratama,85.5",
    "M0526014,Bella Safitri,90.75",
    "M0526099,,70",
    "M0526032,Dimas Wicaksono,74.7",
    "M0526050,Cahya Putri,abc"
};
```

---

## 💡 Kunci Solusi & Pembahasan

Kerjakan soal secara mandiri dulu sebelum membuka kunci jawaban.

<details>
<summary><b>🔍 Buka Kunci Jawaban Level 1</b></summary>

### Solusi 1.1: Tabel Nilai Praktikum

Lebar total = `10 + 1 + 18 + 1 + 6 + 1 + 6 + 1 + 6 + 1 + 12 = 63` karakter.

```c
#include <stdio.h>

#define LEBAR 63

static void garis(char c) {
    for (int i = 0; i < LEBAR; i++) putchar(c);
    putchar('\n');
}

int main(void) {
    const char *nim[]  = {"M0526001", "M0526014", "M0526032"};
    const char *nama[] = {"Aditya Pratama", "Bella Safitri", "Dimas Wicaksono"};
    int tugas[] = {85, 90, 75};
    int uts[]   = {80, 95, 70};
    int uas[]   = {90, 88, 78};

    garis('=');
    printf("%-10s %-18s %6s %6s %6s %12s\n", "NIM", "NAMA", "TUGAS", "UTS", "UAS", "AKHIR");
    garis('-');

    for (int i = 0; i < 3; i++) {
        double akhir = (tugas[i] * 0.3) + (uts[i] * 0.3) + (uas[i] * 0.4);
        printf("%-10s %-18s %6d %6d %6d %12.2f\n",
               nim[i], nama[i], tugas[i], uts[i], uas[i], akhir);
    }

    garis('=');
    return 0;
}
```

**Hasil:**

```text
===============================================================
NIM        NAMA                TUGAS    UTS    UAS        AKHIR
---------------------------------------------------------------
M0526001   Aditya Pratama         85     80     90        85.50
M0526014   Bella Safitri          90     95     88        90.70
M0526032   Dimas Wicaksono        75     70     78        74.70
===============================================================
```

### Solusi 1.2: Multi-Base Converter

Memakai `%u` langsung akan menerima `-1` sebagai 4294967295. Karena itu angka dibaca sebagai `long long` lalu dicek tidak negatif:

```c
#include <stdio.h>

int main(void) {
    long long masukan;

    printf("Masukkan angka bulat tidak negatif: ");
    if (scanf("%lld", &masukan) != 1 || masukan < 0 || masukan > 4294967295LL) {
        fprintf(stderr, "Input harus bilangan bulat 0 sampai 4294967295!\n");
        return 1;
    }
    unsigned int nilai = (unsigned int)masukan;

    printf("\n--- Representasi Nilai ---\n");
    printf("Desimal             : %u\n", nilai);
    printf("Hex (kecil)         : %#x\n", nilai);
    printf("Hex (kapital)       : %#X\n", nilai);
    printf("Oktal               : %#o\n", nilai);
    printf("Hex 4 digit padding : 0x%04X\n", nilai);

    return 0;
}
```

Contoh untuk input `255`: `255`, `0xff`, `0XFF`, `0377`, `0x00FF`.

### Solusi 1.3: Tebak Output

| Baris | Keluaran | Penjelasan |
|:---:|:---|:---|
| (a) | `[  3.14]` | Lebar 6, `3.14` hanya 4 karakter sehingga ada 2 spasi di depan |
| (b) | `[UNS     ]` | Rata kiri, lebar 8 sehingga ada 5 spasi di belakang |
| (c) | `[+0042]` | Lebar 5 termasuk tanda `+`, sisanya diisi nol |
| (d) | `[010] [0XFF]` | `#` menambah awalan `0` pada oktal dan `0X` pada heksa kapital |
| (e) | `[Alg]` | Precision pada string memotong menjadi 3 karakter |
| (f) | `[    42]` | `*` mengambil lebar 6 dari argumen |
| (g) | `75%` | `%%` mencetak satu tanda persen |
| (h) | `Hi!` | `%c` mencetak kode ASCII 72, 105, 33 |

### Solusi 1.4: Judul Rata Tengah

```c
#include <stdio.h>
#include <string.h>

static void judul(const char *teks, int lebar) {
    int pad = (lebar - (int)strlen(teks)) / 2;
    if (pad < 0) pad = 0;

    for (int i = 0; i < lebar; i++) putchar('=');
    putchar('\n');
    printf("%*s%s\n", pad, "", teks);     // %*s + "" = cetak 'pad' spasi
    for (int i = 0; i < lebar; i++) putchar('=');
    putchar('\n');
}

int main(void) {
    judul("LAPORAN PRAKTIKUM", 40);
    return 0;
}
```

</details>

<details>
<summary><b>🔍 Buka Kunci Jawaban Level 2</b></summary>

### Solusi 2.1: Parser Waktu

```c
#include <stdio.h>

int main(void) {
    int jam, menit, detik;

    printf("Masukkan waktu (HH:MM:SS): ");
    int hasil = scanf("%d:%d:%d", &jam, &menit, &detik);   // ':' literal harus cocok

    if (hasil != 3) {
        fprintf(stderr, "Format tidak valid! Gunakan HH:MM:SS.\n");
        return 1;
    }

    if (jam < 0 || jam > 23 || menit < 0 || menit > 59 || detik < 0 || detik > 59) {
        fprintf(stderr, "Jam (0-23), menit (0-59), atau detik (0-59) di luar jangkauan!\n");
        return 1;
    }

    long total_detik = (jam * 3600L) + (menit * 60L) + detik;
    printf("Waktu terkonfirmasi: %02d:%02d:%02d\n", jam, menit, detik);
    printf("Total sejak tengah malam: %ld detik\n", total_detik);
    return 0;
}
```

**Catatan:**

- Karakter literal `:` lebih ketat daripada `%*c`. Input `08x30x45` ditolak (hasil 1).
- `%*c` lebih cocok jika pemisah boleh bervariasi. Untuk itu `%*[:.]` lebih aman daripada `%*c`.
- Jika pengguna mengetik `12:30` lalu Enter, `scanf` **menunggu** angka ketiga di baris berikutnya (lihat Modul 7.2 Bagian 5). Versi lebih tangguh memakai `fgets` + `sscanf`.

### Solusi 2.2: Penjelasan & Perbaikan "The Ghost Enter"

**Penyebab:** saat pengguna mengetik `1` lalu Enter, `stdin` menerima `'1'` dan `'\n'`. `scanf("%d")` hanya mengambil `'1'`, sehingga `'\n'` tertinggal. Specifier `%c` **tidak** melewati whitespace, jadi langsung membaca `'\n'` itu sebagai nilai `member`.

**Perbaikan 1: spasi sebelum `%c`**
```c
scanf(" %c", &member);
```

**Perbaikan 2: buang sisa baris setelah membaca angka**
```c
scanf("%d", &paket);
int c;
while ((c = getchar()) != '\n' && c != EOF) { }   // cek EOF agar tidak macet
scanf("%c", &member);
```

### Solusi 2.3: Bug `double`

**Penyebab:** `%f` pada `scanf` mengharapkan `float *` dan hanya menulis 4 byte, sedangkan `suhu` bertipe `double` (8 byte). Hasilnya nilai rusak. GCC dengan `-Wall` akan memberi peringatan `format '%f' expects argument of type 'float *'`.

**Perbaikan:**
```c
scanf("%lf", &suhu);    // double → %lf pada scanf
```

### Solusi 2.4: Celah Format String

**Penyebab:** `printf(pesan)` memperlakukan **input pengguna** sebagai string format. Jika pengguna mengetik `%x %x %x`, `printf` mencari argumen yang tidak pernah diberikan dan membaca data acak dari stack. Specifier lain (seperti `%s`) bahkan bisa membuat program crash.

**Perbaikan:**
```c
printf("%s", pesan);      // atau: fputs(pesan, stdout);
```

</details>

<details>
<summary><b>🔍 Buka Kunci Jawaban Level 3</b></summary>

### Solusi 3.1: Mini Billing System Toko Buku

Solusi memakai pustaka mini dari Modul 7.3 (`baca_baris`, `baca_int`, `baca_double`). Lebar tabel: `3 + 1 + 25 + 1 + 4 + 1 + 12 + 1 + 12 = 60` karakter.

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <errno.h>
#include <time.h>

#define LEBAR 60
#define MAKS_BUKU 5

/* ---------- Pustaka mini input aman (dari Modul 7.3) ---------- */
static int baca_baris(char *buf, size_t n) {
    if (fgets(buf, (int)n, stdin) == NULL) return 0;
    size_t len = strlen(buf);
    if (len > 0 && buf[len - 1] == '\n') {
        buf[len - 1] = '\0';
    } else {
        int c;
        while ((c = getchar()) != '\n' && c != EOF) { }
    }
    return 1;
}

static int baca_int(const char *prompt, int min, int max, int *hasil) {
    char baris[64];
    for (;;) {
        printf("%s", prompt);
        if (!baca_baris(baris, sizeof baris)) return 0;
        char *akhir;
        errno = 0;
        long v = strtol(baris, &akhir, 10);
        if (akhir == baris) { printf("   [!] Itu bukan angka.\n"); continue; }
        while (isspace((unsigned char)*akhir)) akhir++;
        if (*akhir != '\0') { printf("   [!] Ada karakter tak valid setelah angka.\n"); continue; }
        if (errno == ERANGE || v < min || v > max) {
            printf("   [!] Angka harus antara %d dan %d.\n", min, max);
            continue;
        }
        *hasil = (int)v;
        return 1;
    }
}

static int baca_double(const char *prompt, double min, double max, double *hasil) {
    char baris[64];
    for (;;) {
        printf("%s", prompt);
        if (!baca_baris(baris, sizeof baris)) return 0;
        char *akhir;
        errno = 0;
        double v = strtod(baris, &akhir);
        if (akhir == baris) { printf("   [!] Itu bukan angka.\n"); continue; }
        while (isspace((unsigned char)*akhir)) akhir++;
        if (*akhir != '\0') { printf("   [!] Ada karakter tak valid setelah angka.\n"); continue; }
        if (errno == ERANGE || !(v >= min && v <= max)) {
            printf("   [!] Nilai harus antara %.2f dan %.2f.\n", min, max);
            continue;
        }
        *hasil = v;
        return 1;
    }
}
/* --------------------------------------------------------------- */

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
    char nama_kasir[50];
    int jumlah_item;
    char judul[MAKS_BUKU][40];
    int qty[MAKS_BUKU];
    double harga[MAKS_BUKU];
    double total_kotor = 0.0;

    garis('=');
    cetak_tengah("SISTEM KASIR TOKO BUKU ILMU");
    garis('=');

    printf("Nama Kasir: ");
    if (!baca_baris(nama_kasir, sizeof nama_kasir)) return 1;

    if (!baca_int("Jumlah jenis buku (1-5): ", 1, MAKS_BUKU, &jumlah_item)) return 1;

    for (int i = 0; i < jumlah_item; i++) {
        printf("\n--- Data Buku ke-%d ---\n", i + 1);
        printf("Judul Buku      : ");
        if (!baca_baris(judul[i], sizeof judul[i])) return 1;
        if (!baca_int("Jumlah (Qty)    : ", 1, 1000, &qty[i])) return 1;
        if (!baca_double("Harga Satuan Rp : ", 0.01, 100000000.0, &harga[i])) return 1;
        total_kotor += qty[i] * harga[i];
    }

    double ppn = total_kotor * 0.11;
    double total_bayar = total_kotor + ppn;

    // Tanggal dan jam transaksi dari jam sistem
    char tanggal[32];
    time_t sekarang = time(NULL);
    struct tm *lokal = localtime(&sekarang);
    strftime(tanggal, sizeof tanggal, "%d/%m/%Y %H:%M", lokal);

    printf("\n\n");
    garis('=');
    cetak_tengah("NOTA RESMI PEMBELIAN");
    cetak_tengah("TOKO BUKU ILMU");
    garis('=');
    printf("Kasir  : %s\n", nama_kasir);
    printf("Tanggal: %s\n", tanggal);
    garis('-');
    printf("%-3s %-25s %4s %12s %12s\n", "NO", "ITEM BUKU", "QTY", "HARGA (RP)", "SUBTOTAL");
    garis('-');

    for (int i = 0; i < jumlah_item; i++) {
        double sub = qty[i] * harga[i];
        printf("%-3d %-25.25s %4d %12.2f %12.2f\n", i + 1, judul[i], qty[i], harga[i], sub);
    }

    garis('-');
    printf("%-44s Rp %12.2f\n", "SUBTOTAL PEMBELIAN", total_kotor);
    printf("%-44s Rp %12.2f\n", "PPN (11%)", ppn);
    garis('=');
    printf("%-44s Rp %12.2f\n", "TOTAL PEMBAYARAN", total_bayar);
    garis('=');
    cetak_tengah("Terima kasih atas kunjungan Anda!");

    return 0;
}
```

**Poin pembahasan:**

- Semua input memakai `fgets` + `strtol`/`strtod`, sehingga huruf, angka negatif, `2abc`, dan angka raksasa semuanya ditolak tanpa infinite loop.
- Setiap pembacaan mengembalikan `0` pada EOF, lalu program berhenti dengan baik (`return 1`), bukan mengulang selamanya.
- `%-25.25s` memotong judul yang terlalu panjang agar kolom tidak melebar.
- `PPN (11%)` aman karena berupa **argumen** `%s`, bukan bagian dari string format.
- Tanggal diambil dengan `time()` dan `strftime()`. (Catatan: `%d`, `%m`, `%Y` di dalam `strftime` adalah format milik `strftime`, bukan `printf`.)
- Uang disimpan sebagai `double` demi kesederhanaan. Pada aplikasi nyata, simpan dalam satuan bulat (rupiah/sen) untuk menghindari galat pembulatan.

### Solusi 3.2: Parser CSV Sederhana

```c
#include <stdio.h>

int main(void) {
    const char *data[] = {
        "M0526001,Aditya Pratama,85.5",
        "M0526014,Bella Safitri,90.75",
        "M0526099,,70",
        "M0526032,Dimas Wicaksono,74.7",
        "M0526050,Cahya Putri,abc"
    };
    int jumlah = (int)(sizeof data / sizeof data[0]);
    int valid = 0;
    double total = 0.0;

    printf("%-10s %-18s %8s\n", "NIM", "NAMA", "NILAI");
    printf("---------------------------------------\n");

    for (int i = 0; i < jumlah; i++) {
        char nim[15];
        char nama[50];
        double nilai;

        // %14[^,] : baca sampai koma (maks 14 karakter), lalu ',' literal harus cocok
        if (sscanf(data[i], "%14[^,],%49[^,],%lf", nim, nama, &nilai) != 3) {
            fprintf(stderr, "Baris %d tidak valid: \"%s\"\n", i + 1, data[i]);
            continue;
        }

        printf("%-10s %-18s %8.2f\n", nim, nama, nilai);
        total += nilai;
        valid++;
    }

    printf("---------------------------------------\n");
    if (valid > 0) {
        printf("Rata-rata dari %d data valid: %.2f\n", valid, total / valid);
    }
    return 0;
}
```

**Keluaran di terminal** (dua pesan `stderr` muncul bercampur pada layar yang sama):

```text
NIM        NAMA                  NILAI
---------------------------------------
M0526001   Aditya Pratama        85.50
M0526014   Bella Safitri         90.75
Baris 3 tidak valid: "M0526099,,70"
M0526032   Dimas Wicaksono       74.70
Baris 5 tidak valid: "M0526050,Cahya Putri,abc"
---------------------------------------
Rata-rata dari 3 data valid: 83.65
```

**Poin pembahasan:**

- Baris 3 ditolak: `%49[^,]` tidak bisa mencocokkan **nol** karakter (field nama kosong), sehingga `sscanf` berhenti dengan hasil 1.
- Baris 5 ditolak: `%lf` gagal membaca `abc`, sehingga hasilnya 2.
- Pola `%[^,]` + `,` literal adalah dasar parsing CSV sederhana. Untuk CSV sungguhan (field berkutip, koma di dalam teks), diperlukan parser khusus.

</details>

---

<div align="center">

[⬅️ Sebelumnya: Modul 7.3 String I/O & Input Aman](03-String-IO-dan-Input-Aman.md) &nbsp;&nbsp;|&nbsp;&nbsp; [🏠 Kembali ke Overview Bab 7](Bab7-Overview.md) &nbsp;&nbsp;|&nbsp;&nbsp; [📋 Daftar Materi Utama](../Daftar_Materi.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke Bab 8: Topik C Lanjutan ➡️](../bab-08-topik-c-lanjutan/Bab8-Overview.md)

</div>
