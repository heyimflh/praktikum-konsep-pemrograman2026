<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=2e7d32&height=150&section=header&text=9.1%20Pengantar%20File%20Processing&fontSize=35&fontColor=ffffff&animation=fadeIn&desc=Pointer%20FILE%2C%20fopen%2C%20Mode%20Akses%2C%20dan%20fclose&descSize=14&descAlignY=75)

[⬅️ Overview Bab 9](Bab9-Overview.md) &nbsp;•&nbsp; [📋 Daftar Materi](../Daftar_Materi.md) &nbsp;•&nbsp; [Modul 9.2: Sequential Access ➡️](02-Sequential-Access.md)

---

</div>

## 🎯 Tujuan Pembelajaran

Setelah mempelajari modul ini, kamu diharapkan mampu:
1. Memahami konsep stream file dan representasi tipe data `FILE *` di bahasa C.
2. Membedakan karakteristik antara file teks (*plaintext*) dan file biner (*binary*).
3. Menggunakan fungsi `fopen()` dengan mode akses yang tepat (`r`, `w`, `a`, dan varian biner `b`).
4. Melakukan validasi error saat membuka file (`NULL pointer check`).
5. Menutup file dengan aman menggunakan fungsi `fclose()` untuk mencegah kebocoran *resource*.

---

## 📂 Mengapa File Processing?

Program pada komputer tidak dapat dipisahkan dari file untuk mendukung keberjalanannya. Sebagai contoh:
- Saat memainkan video game, terdapat fitur untuk menyimpan (*save game*) dan memuat (*load game*) progres petualanganmu.
- Saat kamu menyimpan setelan (*settings / preferences*) suatu aplikasi ke dalam file lalu memuatnya kembali saat aplikasi dibuka esok hari.
- Saat server mencatat log error atau transaksi keuangan ke penyimpanan jangka panjang.

Di dalam sistem komputer, file terbagi menjadi dua kategori utama:
1. **Plaintext (Sequential-Access):** Kumpulan karakter teks (ASCII/UTF-8) yang dapat dibaca dan diedit langsung oleh manusia menggunakan text editor seperti Notepad atau VS Code (contoh: `.txt`, `.csv`, `.log`).
2. **Binary (Random-Access):** Kumpulan raw byte biner yang merepresentasikan struktur data internal program atau format multimedia (contoh: `.dat`, `.bin`, `.png`, `.exe`). File biner hanya dapat dibaca oleh program yang memahami format tata letaknya.

Seluruh operasi file di bahasa C difasilitasi oleh header **`<stdio.h>`**.

---

## 📄 Representasi File: `FILE *`

Dalam bahasa C, file yang sedang dibuka diwakili oleh tipe data pointer **`FILE *`** (*pointer to FILE structure*). Secara internal, `FILE` adalah sebuah struct yang didefinisikan oleh implementasi compiler standar C:

```c
typedef struct {
    /* Anggota internal compiler: penunjuk buffer, posisi kursor, status EOF/error, dsb. */
} FILE;
```

Sebagai programmer, kita tidak perlu (dan tidak boleh) memodifikasi member di dalam struct `FILE` secara langsung. Kita cukup mengelolanya melalui pointer `FILE *` menggunakan fungsi-fungsi standar C.

---

## 🔓 Membuka File: `fopen()`

Untuk membuka suatu file, gunakan fungsi **`fopen()`**:

```c
FILE *fopen(const char *filename, const char *mode);
```

### Parameter:
1. `filename`: String yang berisi path file (bisa relatif seperti `"data.txt"` atau absolut seperti `"C:/Users/data.txt"`).
2. `mode`: String yang menentukan bagaimana file akan diakses (baca, tulis, tambah, teks, atau biner).

### Nilai Kembalian:
- Pointer `FILE *` yang valid jika file berhasil dibuka.
- Pointer **`NULL`** jika file gagal dibuka (misalnya file tidak ditemukan, tidak ada izin tulis, atau disk penuh).

---

## 📋 Tabel Lengkap Mode Akses File

| Mode Teks | Mode Biner | Deskripsi Operasi | Jika File Sudah Ada | Jika File Belum Ada |
|:---:|:---:|---|---|---|
| `"r"` | `"rb"` | **Read-only**: Membaca file dari awal | Berhasil dibuka | Mengembalikan `NULL` |
| `"w"` | `"wb"` | **Write-only**: Menulis file baru | Isi lama dihapus (*truncate* ke 0 byte) | File baru otomatis dibuat |
| `"a"` | `"ab"` | **Append-only**: Menulis hanya di akhir file | Pointer diarahkan ke akhir file | File baru otomatis dibuat |
| `"r+"` | `"rb+"` | **Read & Write**: Membaca dan menimpa dari awal | Berhasil dibuka | Mengembalikan `NULL` |
| `"w+"` | `"wb+"` | **Read & Write**: Membaca dan menulis ulang | Isi lama dihapus (*truncate* ke 0 byte) | File baru otomatis dibuat |
| `"a+"` | `"ab+"` | **Read & Append**: Membaca seluruh file, menulis di akhir | Berhasil dibuka | File baru otomatis dibuat |

> [!IMPORTANT]
> **Selalu sertakan karakter `'b'` saat bekerja dengan file biner!**  
> Di sistem operasi Windows, mode teks secara implisit mengonversi `\n` menjadi `\r\n`. Pada file biner, byte `0x0A` (`\n`) adalah data biner mentah yang jika diubah menjadi `0x0D 0x0A` akan merusak keseluruhan isi file biner!

---

## 🔒 Menutup File: `fclose()`

Setelah selesai menggunakan file, kita **wajib** menutupnya menggunakan fungsi **`fclose()`**:

```c
int fclose(FILE *stream);
```

### Mengapa `fclose()` sangat penting?
1. **Flushing Buffer:** C menggunakan buffer memori perantara saat menulis ke disk. Pemanggilan `fclose()` memastikan seluruh data yang masih tertahan di memori segera ditulis (*flush*) ke media penyimpanan fisik.
2. **Resource Management:** Sistem operasi memiliki batas maksimal file yang dapat dibuka oleh sebuah proses (*file descriptor limit*). Menutup file membebaskan *handle* tersebut kembali ke OS.

---

## 💡 Contoh Praktik: Pola Pembukaan File yang Aman

Berikut adalah pola standar penulisan kode pembukaan dan penutupan file di bahasa C:

```c
#include <stdio.h>

int main() {
    FILE *f;

    // Membuka file biner untuk operasi tulis
    f = fopen("settings.dat", "wb");

    // Langkah KRUSIAL: Selalu validasi pointer NULL
    if (f == NULL) {
        printf("[ERROR] Gagal membuka/membuat file settings.dat!\n");
        return 1;
    }

    printf("[OK] File settings.dat berhasil dibuka.\n");

    /* Lakukan operasi penulisan atau manipulasi data di sini */

    // Selalu tutup file setelah selesai
    fclose(f);
    printf("[OK] File berhasil disimpan dan ditutup.\n");

    return 0;
}
```

---

<div align="center">

[⬅️ Sebelumnya: Overview Bab 9](Bab9-Overview.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 9.2: Sequential Access ➡️](02-Sequential-Access.md)

</div>

