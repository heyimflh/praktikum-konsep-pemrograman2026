<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=e65100&height=150&section=header&text=8.3%20Enumeration&fontSize=35&fontColor=ffffff&animation=fadeIn&desc=Mendefinisikan%20Tipe%20Data%20Konstan%20yang%20Bersih%20dan%20Ekspresif&descSize=14&descAlignY=75)

[⬅️ Modul 8.2: Bit Manipulation](02-Bit-Manipulation.md) &nbsp;•&nbsp; [📋 Daftar Materi](../Daftar_Materi.md) &nbsp;•&nbsp; [Overview Bab 8 🏠](Bab8-Overview.md)

---

</div>

## 🎯 Tujuan Pembelajaran

Setelah mempelajari modul ini, kamu diharapkan mampu:
1. Menjelaskan konsep dan tujuan penggunaan *enumeration* (`enum`) di bahasa C.
2. Membedakan kode berbasis *magic numbers* dengan kode yang menggunakan enum.
3. Mendeklarasikan dan mendefinisikan enum dengan nilai otomatis maupun nilai kustom.
4. Mengintegrasikan enum bersama pernyataan `switch-case` untuk kontrol alur program yang terstruktur.
5. Memahami karakteristik memori dan representasi integer dari enum.

---

## 🏷️ Pengantar: Menghapus "Magic Numbers"

Perhatikan potongan kode menu berikut:

```c
int user_choice;
printf("Pilih operasi:\n[0] Keluar\n[1] Login\n[2] Register\n> ");
scanf("%d", &user_choice);

if (user_choice == 0) {
    // ...
} else if (user_choice == 1) {
    // ...
} else if (user_choice == 2) {
    // ...
}
```

Ketika kode program sudah mencapai ribuan baris, angka-angka seperti `0`, `1`, `2` di atas disebut sebagai **magic numbers**. Siapa pun yang membaca kode harus bolak-balik melihat dokumentasi atau bagian atas kode untuk mengingat apa arti angka `1` atau `2`.

Bandingkan jika kita menggunakan **Enumeration (`enum`)**:

```c
enum MenuOption {
    eKeluar = 0,
    eLogin,
    eRegister
};

if (user_choice == eKeluar) {
    // Keluar program
} else if (user_choice == eLogin) {
    // Proses login
} else if (user_choice == eRegister) {
    // Proses pendaftaran
}
```

Kode menjadi *self-documenting* (menjelaskan dirinya sendiri), mudah dibaca oleh tim, dan meminimalkan resiko salah mengetik angka!

---

## ⚙️ Deklarasi & Definisi Enum

Sintaks dasar:
```c
enum NamaEnum {
    NAMA_KONSTANTA_1,
    NAMA_KONSTANTA_2,
    NAMA_KONSTANTA_3
};
```

### 1. Nilai Otomatis (Default Mulai dari 0)

Secara default, elemen pertama bernilai `0`, elemen kedua bernilai `1`, dan seterusnya bertambah satu:

```c
enum Hari {
    SENIN,   // 0
    SELASA,  // 1
    RABU,    // 2
    KAMIS,   // 3
    JUMAT,   // 4
    SABTU,   // 5
    MINGGU   // 6
};
```

### 2. Menentukan Nilai Kustom

Kita dapat menentukan nilai awal atau nilai individual secara bebas:

```c
enum Hari {
    SENIN = 1, // Dimulai dari 1
    SELASA,    // 2
    RABU,      // 3
    KAMIS,     // 4
    JUMAT,     // 5
    SABTU,     // 6
    MINGGU     // 7
};
```

Atau nilai acak untuk kode status HTTP:
```c
enum HttpStatus {
    HTTP_OK = 200,
    HTTP_BAD_REQUEST = 400,
    HTTP_NOT_FOUND = 404,
    HTTP_SERVER_ERROR = 500
};
```

---

## 💡 Menggabungkan `typedef` dengan Enum

Agar tidak perlu mengetik kata `enum` berulang kali, gunakan `typedef`:

```c
typedef enum {
    ROLE_MAHASISWA,
    ROLE_DOSEN,
    ROLE_ASISTEN,
    ROLE_ADMIN
} UserRole;

int main() {
    UserRole current_user = ROLE_ASISTEN;

    if (current_user == ROLE_ASISTEN) {
        printf("Selamat datang, Asisten Praktikum!\n");
    }

    return 0;
}
```

---

## 🚦 Studi Kasus: Enum bersama `switch-case`

Salah satu kombinasi paling elegan di bahasa C adalah menggunakan enum sebagai kondisi dalam blok `switch`:

```c
#include <stdio.h>

typedef enum {
    RUANG_KELAS_A,
    RUANG_KELAS_B,
    RUANG_AULA_BESAR,
    RUANG_LAB_KOMPUTER
} RuanganUjian;

void cetakInfoRuangan(RuanganUjian r) {
    switch (r) {
    case RUANG_KELAS_A:
        printf("Kapasitas: 40 orang | Gedung A\n");
        break;
    case RUANG_KELAS_B:
        printf("Kapasitas: 40 orang | Gedung B\n");
        break;
    case RUANG_AULA_BESAR:
        printf("Kapasitas: 200 orang | Gedung Rektorat\n");
        break;
    case RUANG_LAB_KOMPUTER:
        printf("Kapasitas: 50 unit PC | Gedung Lab Informatika\n");
        break;
    default:
        printf("Ruangan tidak dikenali.\n");
        break;
    }
}

int main() {
    RuanganUjian pilihan = RUANG_LAB_KOMPUTER;
    cetakInfoRuangan(pilihan);
    return 0;
}
```

> [!TIP]
> Compiler modern (seperti GCC dengan flag `-Wall`) akan memberikan *warning* jika kamu lupa menangani salah satu konstanta enum di dalam blok `switch`! Ini sangat membantu mencegah bug logika.

---

## ⚖️ Keunggulan Efisiensi Enum vs String

Tanpa enum, seorang programmer pemula mungkin akan menggunakan string:
```c
char status[] = "RUANG_LAB_KOMPUTER";
if (strcmp(status, "RUANG_LAB_KOMPUTER") == 0) { ... }
```
Pendekatan string di atas:
- **Lambat:** Membutuhkan pemanggilan fungsi `strcmp()` yang membandingkan karakter demi karakter.
- **Boros Memori:** Memerlukan array byte untuk setiap karakter string.
- **Rentan Kesalahan Ketik:** Jika ada typo kecil seperti `"RUANG_LAB_KOMPTER"`, compiler tidak akan memberi tahu.

Dengan enum:
- **Cepat:** Perbandingan dilakukan antar bilangan bulat satu siklus CPU (`if (status == RUANG_LAB_KOMPUTER)`).
- **Hemat:** Hanya memakan 4 byte integer.
- **Aman:** Jika ada typo, compiler langsung melaporkan error saat proses kompilasi!

---

<div align="center">

[⬅️ Sebelumnya: Modul 8.2 Bit Manipulation](02-Bit-Manipulation.md) &nbsp;&nbsp;|&nbsp;&nbsp; [🏠 Overview Bab 8](Bab8-Overview.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke Bab 9: File Processing ➡️](../bab-09-file-processing/Bab9-Overview.md)

</div>

