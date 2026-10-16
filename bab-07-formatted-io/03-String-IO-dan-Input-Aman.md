<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=00796b&height=150&section=header&text=7.3%20String%20I%2FO%20%26%20Input%20Aman&fontSize=32&fontColor=ffffff&animation=fadeIn&desc=fgets%2C%20sscanf%2C%20strtol%2C%20snprintf%2C%20dan%20stderr&descSize=14&descAlignY=75)

[⬅️ Modul 7.2: Scanf Formats](02-Scanf-Formats.md) &nbsp;•&nbsp; [📋 Daftar Materi](../Daftar_Materi.md) &nbsp;•&nbsp; [Modul 7.4: Latihan & Studi Kasus ➡️](04-Latihan-dan-Studi-Kasus.md)

---

</div>

## 🎯 Tujuan Pembelajaran

Setelah menyelesaikan modul ini, kamu diharapkan mampu:

1. Menjelaskan keterbatasan `scanf` pada input interaktif.
2. Membaca satu baris input dengan aman memakai `fgets` dan membersihkan karakter newline.
3. Mem-parsing isi string dengan `sscanf`, termasuk mendeteksi sisa karakter yang tidak diharapkan.
4. Mengonversi teks menjadi angka dengan `strtol`/`strtod` dan memeriksa error-nya.
5. Membentuk string berformat dengan `snprintf` tanpa risiko overflow.
6. Memisahkan keluaran normal (`stdout`) dan pesan error (`stderr`).

---

## 🤔 Mengapa Perlu Pola Lain Selain `scanf`?

Pola `scanf` + `bersihkan_buffer()` dari Modul 7.2 sudah cukup untuk banyak latihan. Namun masih ada celah:

| Masalah | Contoh | Akibat |
|:---|:---|:---|
| Sisa karakter diterima diam-diam | Input `2abc` pada `%d` | `2` diterima, `abc` terbuang tanpa peringatan |
| Angka terlalu besar | Input `99999999999` pada `%d` | Perilaku tidak terdefinisi menurut standar |
| Sinkronisasi buffer rapuh | Lupa spasi pada `%c` atau lupa membersihkan buffer | Input terlewat |
| Satu baris diproses setengah | `%d:%d:%d` dengan baris `12:30` | Program menunggu baris berikutnya |

Pola yang lebih kokoh dan diterapkan di banyak program profesional adalah **membaca satu baris penuh sebagai string, baru kemudian diproses**:

```
fgets (ambil 1 baris utuh)  →  sscanf / strtol (parse isi baris)  →  validasi
```

Keuntungannya: batas buffer jelas, tidak ada sisa input yang menggantung, dan setiap kesalahan hanya memengaruhi satu baris.

---

## 📥 1. `fgets`: Membaca Satu Baris dengan Aman

```c
char *fgets(char *buf, int n, FILE *stream);
```

- Membaca **maksimal `n − 1` karakter** dan selalu menambahkan `\0`. Tidak mungkin overflow jika `n` sesuai ukuran array.
- Berhenti di newline. Newline **ikut disimpan** di dalam `buf` (berbeda dengan `gets`).
- Mengembalikan `NULL` jika input habis (EOF) atau terjadi error.

Dua hal yang harus ditangani: (1) menghapus `\n` di akhir, dan (2) jika baris lebih panjang dari buffer, membuang sisa barisnya agar tidak "bocor" ke pembacaan berikutnya. Semuanya dibungkus dalam satu fungsi:

```c
#include <stdio.h>
#include <string.h>

/* Membaca satu baris ke buf (maks n-1 karakter), newline dibuang.
   Return 1 jika berhasil, 0 jika input habis (EOF). */
static int baca_baris(char *buf, size_t n) {
    if (fgets(buf, (int)n, stdin) == NULL) {
        return 0;
    }
    size_t len = strlen(buf);
    if (len > 0 && buf[len - 1] == '\n') {
        buf[len - 1] = '\0';                  // baris muat seluruhnya
    } else {
        int c;                                // baris terlalu panjang: buang sisanya
        while ((c = getchar()) != '\n' && c != EOF) { }
    }
    return 1;
}
```

Karena `fgets` selalu membaca sampai akhir baris, **tidak ada lagi masalah newline trap**. Setiap pemanggilan dimulai dari awal baris baru, apa pun tipe data yang kamu parse sesudahnya.

---

## 🔍 2. `sscanf`: `scanf` untuk String

`sscanf` bekerja persis seperti `scanf` (specifier, field width, scanset, suppression, return value), tetapi sumbernya adalah **string**, bukan keyboard:

```c
int sscanf(const char *sumber, const char *format, ...);
```

```c
#include <stdio.h>

int main(void) {
    const char *teks = "08:30:45";
    int jam, menit, detik;

    if (sscanf(teks, "%d:%d:%d", &jam, &menit, &detik) == 3) {
        printf("Jam=%d Menit=%d Detik=%d\n", jam, menit, detik);
    }
    return 0;
}
```

### Trik: Mendeteksi Sisa Karakter yang Tidak Diharapkan

Tambahkan satu konversi "pengintai" di akhir. Jika konversi ekstra itu **berhasil**, berarti ada sisa karakter yang tidak diharapkan:

```c
char baris[64];
int angka;
char sisa[2];                      // tempat menampung 1 karakter sisa (jika ada)

if (!baca_baris(baris, sizeof baris)) return 1;

if (sscanf(baris, "%d %1s", &angka, sisa) == 1) {
    printf("Valid: %d\n", angka);          // tepat 1 konversi: tidak ada sisa
} else {
    printf("Input tidak valid!\n");        // 0/EOF = bukan angka, 2 = ada sisa ("2abc")
}
```

Pada input `2abc` hasilnya 2 (angka `2` dan sisa `a`), jadi ditolak. Pada input `42` atau `  42  ` hasilnya 1, jadi diterima. Pada baris kosong hasilnya `EOF`, jadi ditolak.

---

## 🔢 3. `strtol` & `strtod`: Konversi Angka dengan Deteksi Error

`sscanf("%d")` masih tidak terdefinisi pada angka yang terlalu besar. Fungsi `strtol` (string → `long`) dan `strtod` (string → `double`) di `<stdlib.h>` memberi tahu **dengan tepat** di mana pembacaan berhenti dan apakah terjadi *overflow*.

```c
long strtol(const char *s, char **akhir, int basis);
double strtod(const char *s, char **akhir);
```

Pola pemakaian yang benar:

```c
char *akhir;
errno = 0;                                   // reset sebelum memanggil
long v = strtol(baris, &akhir, 10);

if (akhir == baris)          { /* tidak ada digit sama sekali */ }
else if (errno == ERANGE)    { /* angka di luar jangkauan long */ }
else if (*akhir != '\0')     { /* ada karakter sisa setelah angka */ }
else                         { /* valid */ }
```

---

## 🧰 4. Pustaka Mini Input Aman (Siap Pakai)

Gabungkan `baca_baris` + `strtol`/`strtod` menjadi dua fungsi pembaca angka yang **meminta ulang** sampai valid, dan **berhenti baik-baik** saat input habis. Fungsi-fungsi ini dipakai ulang di Modul 7.4.

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <errno.h>

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

/* Minta bilangan bulat dalam [min, max]. Return 1 = sukses, 0 = EOF. */
static int baca_int(const char *prompt, int min, int max, int *hasil) {
    char baris[64];
    for (;;) {
        printf("%s", prompt);
        if (!baca_baris(baris, sizeof baris)) return 0;

        char *akhir;
        errno = 0;
        long v = strtol(baris, &akhir, 10);

        if (akhir == baris) {
            printf("   [!] Itu bukan angka.\n");
            continue;
        }
        while (isspace((unsigned char)*akhir)) akhir++;      // toleransi spasi di ujung
        if (*akhir != '\0') {
            printf("   [!] Ada karakter tak valid setelah angka.\n");
            continue;
        }
        if (errno == ERANGE || v < min || v > max) {
            printf("   [!] Angka harus antara %d dan %d.\n", min, max);
            continue;
        }
        *hasil = (int)v;
        return 1;
    }
}

/* Minta bilangan desimal dalam [min, max]. Return 1 = sukses, 0 = EOF. */
static int baca_double(const char *prompt, double min, double max, double *hasil) {
    char baris[64];
    for (;;) {
        printf("%s", prompt);
        if (!baca_baris(baris, sizeof baris)) return 0;

        char *akhir;
        errno = 0;
        double v = strtod(baris, &akhir);

        if (akhir == baris) {
            printf("   [!] Itu bukan angka.\n");
            continue;
        }
        while (isspace((unsigned char)*akhir)) akhir++;
        if (*akhir != '\0') {
            printf("   [!] Ada karakter tak valid setelah angka.\n");
            continue;
        }
        /* Bentuk !(v >= min && v <= max) juga menolak NaN, yang lolos dari "v < min || v > max" */
        if (errno == ERANGE || !(v >= min && v <= max)) {
            printf("   [!] Nilai harus antara %.2f dan %.2f.\n", min, max);
            continue;
        }
        *hasil = v;
        return 1;
    }
}

int main(void) {
    char nama[50];
    int umur;
    double ipk;

    printf("Nama           : ");
    if (!baca_baris(nama, sizeof nama)) return 1;       // nama berspasi: tanpa scanset
    if (!baca_int("Umur (15-60)   : ", 15, 60, &umur)) return 1;
    if (!baca_double("IPK (0.00-4.00): ", 0.0, 4.0, &ipk)) return 1;

    printf("\n%-20s %3d %5.2f\n", nama, umur, ipk);
    return 0;
}
```

> [!TIP]
> Perhatikan bahwa `fgets` membaca nama berspasi **tanpa scanset dan tanpa spasi ajaib**. Inilah alasan banyak programmer lebih menyukai pola ini untuk input interaktif.

---

## 🧵 5. `snprintf`: Merakit String Berformat

`sprintf` menulis hasil `printf` ke **string**, tetapi tidak membatasi panjang sehingga sangat rawan overflow. Gunakan selalu versi aman **`snprintf`**:

```c
int snprintf(char *tujuan, size_t ukuran, const char *format, ...);
```

- Menulis maksimal `ukuran − 1` karakter dan selalu menambahkan `\0`.
- Mengembalikan **panjang yang seharusnya** ditulis (tanpa `\0`), meskipun terpotong. Nilai ini dipakai untuk mendeteksi pemotongan.

```c
#include <stdio.h>

int main(void) {
    char kode[16];
    int n = snprintf(kode, sizeof kode, "TRX-%04d", 42);
    printf("%s (%d karakter)\n", kode, n);         // TRX-0042 (8 karakter)

    char kecil[6];
    n = snprintf(kecil, sizeof kecil, "%s", "Informatika");
    if (n < 0 || (size_t)n >= sizeof kecil) {
        printf("Terpotong: \"%s\" (butuh %d karakter)\n", kecil, n);
        // Terpotong: "Infor" (butuh 11 karakter)
    }
    return 0;
}
```

Kegunaan yang sering dipakai: membuat nama file (`laporan_%02d.txt`), merakit kode transaksi, atau menyusun baris tabel sebelum dicetak/disimpan.

---

## 🚨 6. `stdout` vs `stderr`: Memisahkan Pesan Error

Program punya dua jalur keluaran: **`stdout`** untuk hasil normal dan **`stderr`** untuk pesan kesalahan. Keduanya tampil di terminal, tetapi bisa dialihkan terpisah.

```c
printf("Hasil perhitungan: %d\n", hasil);             // → stdout
fprintf(stderr, "Error: input tidak valid!\n");        // → stderr
```

```bash
./program > hasil.txt 2> error.txt     # hasil ke hasil.txt, error ke error.txt
```

`stdout` biasanya di-*buffer* (ditahan sementara). Jika kamu perlu memastikan teks sudah benar-benar tampil sekarang (misalnya prompt tanpa `\n` sebelum program berhenti atau saat debugging), panggil `fflush(stdout);`. `stderr` tidak di-buffer sehingga pesannya langsung muncul.

---

## 🔐 7. Ringkasan Keamanan Format String

| Jangan | Gunakan |
|:---|:---|
| `printf(teks_pengguna);` | `printf("%s", teks_pengguna);` atau `fputs(teks_pengguna, stdout);` |
| `gets(buf);` (sudah dihapus dari C11) | `fgets(buf, sizeof buf, stdin);` |
| `scanf("%s", buf);` | `scanf("%19s", buf);` atau `fgets` |
| `sprintf(buf, ...);` | `snprintf(buf, sizeof buf, ...);` |
| Mengabaikan return value `scanf`/`sscanf` | Selalu periksa, dan bedakan `0` dengan `EOF` |
| `atoi(teks)` untuk input pengguna | `strtol(teks, &akhir, 10)` dengan pengecekan error |

---

## 🧭 8. Kapan Memakai yang Mana?

| Situasi | Pilihan terbaik |
|:---|:---|
| Latihan sederhana, input dijamin benar | `scanf` dengan return value dicek |
| Input interaktif dari pengguna sungguhan | `fgets` + `strtol`/`strtod`/`sscanf` |
| Teks berspasi (nama, alamat) | `fgets` (atau `scanf(" %49[^\n]")`) |
| Memecah satu baris berformat (CSV, tanggal, log) | `fgets` + `sscanf` dengan scanset |
| Merakit string berformat | `snprintf` |
| Pesan error | `fprintf(stderr, ...)` |

---

## 📌 Rangkuman

1. Baca **satu baris utuh** dengan `fgets`, lalu proses isinya. Newline trap hilang sendiri.
2. `sscanf` = `scanf` untuk string. Tambahkan konversi `%1s` di ujung untuk mendeteksi sisa karakter.
3. `strtol`/`strtod` memberi deteksi *overflow* dan karakter sisa yang tidak dimiliki `scanf`.
4. Gunakan `snprintf`, bukan `sprintf`.
5. Kirim pesan error ke `stderr`.
6. Jangan pernah memakai input pengguna sebagai string format `printf`.

---

<div align="center">

[⬅️ Sebelumnya: Modul 7.2 Scanf Formats](02-Scanf-Formats.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke Modul 7.4: Latihan & Studi Kasus ➡️](04-Latihan-dan-Studi-Kasus.md)

</div>
