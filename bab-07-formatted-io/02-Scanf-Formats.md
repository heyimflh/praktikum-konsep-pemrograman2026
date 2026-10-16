<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=00695c&height=150&section=header&text=7.2%20Scanf%20Formats&fontSize=35&fontColor=ffffff&animation=fadeIn&desc=Masukan%20Berformat%2C%20Buffer%20Stdin%2C%20Scanset%2C%20dan%20Validasi&descSize=14&descAlignY=75)

[⬅️ Modul 7.1: Printf Formats](01-Printf-Formats.md) &nbsp;•&nbsp; [📋 Daftar Materi](../Daftar_Materi.md) &nbsp;•&nbsp; [Modul 7.3: String I/O & Input Aman ➡️](03-String-IO-dan-Input-Aman.md)

---

</div>

## 🎯 Tujuan Pembelajaran

Setelah menyelesaikan modul ini, kamu diharapkan mampu:

1. Menjelaskan mekanisme kerja `scanf()` dan interaksinya dengan buffer `stdin`.
2. Memilih specifier `scanf` yang tepat, termasuk perbedaan `%f` dan `%lf`.
3. Mengetahui kapan operator alamat `&` wajib dipakai dan kapan tidak boleh.
4. Mencegah *buffer overflow* dengan *field width* dan menangani sisa input yang tertinggal.
5. Memakai *assignment suppression* (`*`) dan karakter literal untuk mem-parsing input berformat.
6. Membaca teks berspasi dengan *scanset* (`%[...]` dan `%[^...]`).
7. Mengatasi "jebakan newline" dan *infinite loop* akibat input tidak valid, termasuk kondisi EOF.

---

## ⚙️ Bagaimana Cara Kerja `scanf()` Sebenarnya?

Antara keyboard dan programmu terdapat area antrean memori sementara bernama **input buffer (`stdin`)**:

```
[Pengguna mengetik di keyboard]
              │
              ▼
   ┌───────────────────────┐
   │  Buffer stdin (RAM)   │ ──► [ '4', '2', '\n' ]  (pengguna menekan Enter)
   └───────────────────────┘
              │
              ▼
  [scanf("%d", &x)]  ──► Mengambil '4' dan '2', mengubahnya jadi int 42,
                         dan MENINGGALKAN '\n' di buffer!
```

> [!IMPORTANT]
> **Karakteristik kunci `scanf`:**
> 1. Pada terminal biasa, karakter baru sampai ke program setelah pengguna menekan **Enter** (`\n`).
> 2. Specifier `%d`, `%f`, `%s` otomatis **melewati whitespace** (spasi, tab, newline) di awal input.
> 3. Specifier **`%c`** dan **`%[...]`** **tidak** melewati whitespace. Inilah akar sebagian besar bug input pada pemula.
> 4. `scanf` **berhenti** pada karakter pertama yang tidak cocok, dan karakter itu **tetap di buffer**.

---

## 🧩 Anatomi Format Specifier `scanf`

```
%[*][width][length]specifier
```

```
┌─── % ───┬───────── [*] ─────────┬──────── [width] ────────┬───── [length] ─────┬── specifier ──┐
│ Simbol  │ Assignment            │ Maksimum karakter yang  │ Ukuran memori data │ Jenis data    │
│ Pembuka │ Suppression           │ boleh dibaca dari stdin │ (h, l, ll, L)      │ yang dibaca   │
│         │ (baca tapi buang!)    │ (MENCEGAH OVERFLOW!)    │                    │ (d, f, c, s)  │
└─────────┴───────────────────────┴─────────────────────────┴────────────────────┴───────────────┘
```

---

## 📊 1. Daftar Format Specifier `scanf`

Berbeda dengan `printf` (yang menerima **nilai**), `scanf` menerima **alamat** (pointer) tujuan penyimpanan. Perhatikan kolom "Argumen":

| Specifier | Argumen yang Diberikan | Keterangan |
|:---:|:---|:---|
| `%d` | `int *` | Bilangan bulat **desimal** (`08` dibaca `8`) |
| `%i` | `int *` | Bilangan bulat dengan **basis otomatis**: awalan `0x` = heksa, awalan `0` = oktal. (`08` **gagal** karena `8` bukan digit oktal!) |
| `%u` | `unsigned int *` | Bilangan bulat tanpa tanda. Input `-1` tidak ditolak, melainkan menjadi 4294967295 |
| `%hd` / `%hhd` | `short *` / `char *` | Bilangan bulat ukuran kecil |
| `%ld` / `%lld` | `long *` / `long long *` | Bilangan bulat ukuran besar |
| `%f` | `float *` | Pecahan **presisi tunggal** |
| `%lf` | `double *` | Pecahan **presisi ganda**. Wajib untuk variabel `double`! |
| `%Lf` | `long double *` | Pecahan presisi diperluas |
| `%e` `%g` | `float *` (`double *` dengan `l`) | Sama seperti `%f`: menerima `3.14` maupun `3.14e2` |
| `%x` / `%o` | `unsigned int *` | Bilangan heksadesimal / oktal |
| `%c` | `char *` | **Satu** karakter, termasuk whitespace |
| `%s` | `char *` (nama array) | Kata tanpa spasi; otomatis menambah `\0` |
| `%[...]` | `char *` (nama array) | String dari himpunan karakter tertentu (*scanset*) |
| `%zu` | `size_t *` | Bilangan ukuran memori |
| `%%` | *(tanpa argumen)* | Membaca karakter `%` literal |

> [!WARNING]
> **Jebakan klasik: `%f` vs `%lf`.** Pada `printf`, keduanya setara. Pada `scanf`, **tidak**:
> ```c
> double suhu;
> scanf("%f",  &suhu);   // ❌ menulis hanya 4 byte ke variabel 8 byte → nilai rusak
> scanf("%lf", &suhu);   // ✅ benar
> ```
> Aturan hafalan: untuk `scanf`, `float` → `%f`, `double` → `%lf`.

---

## 📍 2. Mengapa Kita Menggunakan Operator `&`?

```c
int angka;
char teks[20];

scanf("%d", &angka); // ✅ Wajib pakai &
scanf("%s", teks);   // ✅ TIDAK pakai &
```

### Mengapa `angka` butuh `&`?

C memakai prinsip **pass by value**: fungsi hanya menerima salinan nilai. Jika kamu menulis `scanf("%d", angka)`, yang dikirim adalah **isi** variabel `angka` (yang belum diinisialisasi, jadi berupa nilai sampah). `scanf` lalu menganggap nilai sampah itu sebagai **alamat** dan mencoba menulis ke sana, sehingga terjadi *undefined behavior* (sering berupa crash). Dengan `&angka`, kamu mengirim **alamat** variabel sehingga `scanf` bisa menulis langsung ke variabel aslimu.

### Mengapa `teks` tidak butuh `&`?

Nama array pada ekspresi akan "meluruh" (*array decay*) menjadi pointer ke elemen pertama: `teks` setara `&teks[0]`, bertipe `char *`. Itu persis tipe yang dibutuhkan `%s`.

Menulis `&teks` **bukan sekadar redundan**. Tipenya berbeda, yaitu `char (*)[20]` (pointer ke seluruh array), bukan `char *`. Alamat numeriknya kebetulan sama, tetapi tipenya tidak cocok dengan `%s`, sehingga compiler memberi *warning* dan secara standar perilakunya tidak terdefinisi.

---

## 🔁 3. Return Value `scanf`

`scanf()` mengembalikan **jumlah item yang berhasil dikonversi dan disimpan**, atau konstanta **`EOF`** (biasanya `-1`) jika input habis/gagal sebelum konversi pertama.

| Nilai kembalian | Artinya |
|:---:|:---|
| `n > 0` | `n` item berhasil dibaca (bandingkan dengan jumlah yang kamu harapkan) |
| `0` | Ada input, tetapi **tidak cocok** dengan format (misal huruf untuk `%d`) |
| `EOF` | Input berakhir (pengguna menekan `Ctrl+D` di Linux/macOS atau `Ctrl+Z` lalu Enter di Windows, atau input di-*redirect* dari file yang habis) |

```c
int a, b;
int hasil = scanf("%d %d", &a, &b);
// Input "5 7"   → hasil = 2
// Input "5 x"   → hasil = 1   (a terisi, b tidak)
// Input "x"     → hasil = 0
// Input habis   → hasil = EOF
```

> [!IMPORTANT]
> Memeriksa `status == 1` saja **belum cukup** pada perulangan. Jika nilainya `EOF`, loop yang hanya berbunyi "ulangi sampai valid" akan berputar selamanya karena input tidak akan pernah datang lagi. Selalu tangani `EOF` secara terpisah (lihat Bagian 8).

---

## 🛡️ 4. Field Width: Mencegah Buffer Overflow

```c
char username[10];
scanf("%s", username); // ❌ SANGAT BERBAHAYA!
```

Jika pengguna mengetik 20 karakter, karakter ke-10 dan seterusnya menimpa memori lain (crash atau celah keamanan). Solusinya: batasi dengan **field width = kapasitas array − 1** (1 slot disisakan untuk `\0`):

```c
char username[10];
scanf("%9s", username); // ✅ membaca maksimal 9 karakter
```

> [!WARNING]
> **Field width mencegah overflow, tetapi tidak membuang sisa input.** Jika pengguna mengetik `ABCDEFGHIJKLMNOPQRST` (20 karakter), `%9s` hanya mengambil `ABCDEFGHI`, dan 11 karakter sisanya **tetap di buffer** lalu "dimakan" oleh `scanf` berikutnya. Karena itu, setelah membaca string, buang sisa barisnya dengan fungsi pembersih (lihat Bagian 8).

Perlu diingat juga bahwa lebar harus ditulis literal di string format (`%9s`). Tidak ada versi `*` untuk width di `scanf` (karena `*` di sini berarti suppression), sehingga nilai lebar tidak bisa dinamis kecuali string formatnya dirakit dengan `snprintf`.

---

## 🧱 5. Aturan Whitespace & Karakter Literal dalam Format

String format `scanf` bukan sekadar daftar specifier. Ada tiga jenis elemen:

1. **Specifier** (`%d`, `%s`, dst.): membaca dan menyimpan nilai.
2. **Whitespace** (spasi, `\n`, `\t`) di format: cocok dengan **nol atau lebih** whitespace di input. Itulah sebabnya `" %c"` bisa melewati newline yang tersisa.
3. **Karakter biasa** (misal `:` atau `/`): harus **persis sama** dengan karakter di input, jika tidak, pembacaan **berhenti**.

```c
int j, m, d;
// Input "08:30:45"
scanf("%d:%d:%d", &j, &m, &d);   // ✅ hasil 3
// Input "08-30-45"
scanf("%d:%d:%d", &j, &m, &d);   // hasil 1 (':' tidak ditemukan, berhenti)
```

> [!NOTE]
> **Efek samping `%d` melewati newline.** Karena `%d` melewati whitespace termasuk `\n`, jika pengguna mengetik `12:30` lalu Enter pada `scanf("%d:%d:%d", ...)`, program **menunggu** input di baris berikutnya untuk angka ketiga, bukan langsung error. Pemula sering mengira program "hang". Untuk input yang harus berada dalam satu baris, gunakan pola `fgets` + `sscanf` di Modul 7.3.

---

## 🚫 6. Karakter Pengabaian / Suppression (`*`)

Tanda `*` setelah persen berarti: **baca karakter yang cocok tetapi JANGAN simpan ke variabel mana pun**. Berguna untuk membuang pemisah.

```c
#include <stdio.h>

int main(void) {
    int hari, bulan, tahun;

    printf("Masukkan tanggal (DD/MM/YYYY atau DD-MM-YYYY): ");
    // %*[/-] : baca satu atau lebih karakter '/' atau '-' lalu buang
    if (scanf("%d%*[/-]%d%*[/-]%d", &hari, &bulan, &tahun) != 3) {
        printf("Format tanggal tidak valid!\n");
        return 1;
    }

    printf("Tersimpan: Hari=%02d, Bulan=%02d, Tahun=%d\n", hari, bulan, tahun);
    return 0;
}
```

Perbandingan cara membuang pemisah:

| Pola | Menerima | Catatan |
|:---|:---|:---|
| `%d%*c%d` | Pemisah **apa saja** (`/`, `-`, `x`, bahkan huruf) | Longgar. Input `17x08x1945` ikut lolos |
| `%d%*[/-]%d` | Hanya `/` atau `-` | Lebih ketat. Cocok bila pemisah boleh bervariasi |
| `%d/%d` | Hanya `/` | Paling ketat. Cocok bila format tetap |

> [!TIP]
> Pada scanset, tanda `-` ditulis di **awal atau akhir** (`[/-]`) agar dianggap karakter biasa, bukan penanda rentang. Untuk tanggal pakai `%d`, **bukan** `%i`, karena `%i` menganggap `08` dan `09` sebagai oktal yang salah.

---

## 🔠 7. Membaca Teks Berspasi Menggunakan Scanset (`%[...]`)

`scanf("%s", nama)` hanya membaca sampai spasi pertama. Jadi input `Muhammad Fakhri` hanya menghasilkan `Muhammad`. (Fungsi `gets()` sudah **dihapus dari standar C11** karena tidak mungkin dipakai dengan aman.) Dengan `scanf`, caranya adalah **scanset**.

### A. `%[himpunan]`: hanya membaca karakter yang diizinkan

```c
char vokal[20];
scanf("%19[aiueoAIUEO]", vokal);   // berhenti di karakter pertama yang bukan vokal
```

Rentang seperti `%[a-z]` didukung oleh compiler umum (GCC, Clang, MSVC), tetapi standar C menyatakan arti rentang bergantung implementasi. Untuk kode yang portabel, tulis himpunan secara eksplisit atau pakai rentang dengan sadar akan batasan ini.

### B. `%[^himpunan]` (Negated): baca sampai bertemu karakter tertentu

Karakter `^` berarti "baca semua karakter **kecuali** ini":

```c
char nama_lengkap[50];
printf("Masukkan Nama Lengkap: ");
scanf(" %49[^\n]", nama_lengkap);   // baca sampai Enter (maks 49 karakter)
```

Tiga hal yang wajib dipahami dari `" %49[^\n]"`:

- **Spasi di depan** membuang whitespace tersisa (termasuk `\n` dari input sebelumnya). Tanpa spasi ini, `%[` tidak melewati whitespace sehingga langsung berhenti di `\n` dan gagal membaca apa pun (hasil `0`).
- **`49`** adalah batas aman untuk array berukuran 50.
- **`\n` tidak ikut dibaca.** Ia tetap tinggal di buffer. Aman bila berikutnya adalah `%d` atau ` %c`, tetapi akan "mengacaukan" `scanf("%c", ...)` tanpa spasi atau pemanggilan `fgets`.

---

## 🪤 8. Jebakan Maut `scanf` dan Cara Mengatasinya

### Fungsi Pembersih Buffer (dipakai berulang kali)

Semua perbaikan di bawah memakai satu fungsi kecil ini. Perhatikan pengecekan `EOF`: tanpa itu, loop akan berputar selamanya bila input habis.

```c
static void bersihkan_buffer(void) {
    int c;
    while ((c = getchar()) != '\n' && c != EOF) {
        /* buang karakter sampai akhir baris */
    }
}
```

> [!WARNING]
> Jangan tulis `while (getchar() != '\n');` tanpa cek `EOF`. Pada input yang di-*redirect* atau saat pengguna menekan `Ctrl+D`/`Ctrl+Z`, `getchar()` terus mengembalikan `EOF`, tidak pernah `'\n'`, sehingga program **macet selamanya**.

Fungsi ini aman dipanggil hanya jika masih ada sisa baris di buffer (biasanya ada, karena pengguna menekan Enter). Jika buffer sudah kosong dan kamu memanggilnya, ia akan menunggu input baru.

---

### ⚠️ Jebakan 1: *The Infamous Newline Trap*

**Gejala:** meminta `int`, lalu `char`, tetapi program melewati input karakter.

```c
int umur;
char gender;

printf("Masukkan umur: ");
scanf("%d", &umur);     // Pengguna mengetik '20' lalu Enter ('\n')
                        // 20 diambil, tetapi '\n' MASIH TERTINGGAL di buffer

printf("Masukkan gender (L/P): ");
scanf("%c", &gender);   // 💥 %c langsung melahap '\n' sisa tadi
```

**Solusi 1: spasi sebelum `%c`**

```c
scanf(" %c", &gender);  // spasi di format melewati semua whitespace, termasuk '\n'
```

**Solusi 2: buang sisa baris setelah setiap input**

```c
scanf("%d", &umur);
bersihkan_buffer();     // sisa baris dibuang
scanf("%c", &gender);   // sekarang aman
```

---

### ⚠️ Jebakan 2: *Infinite Loop* Akibat Input Tidak Valid

**Gejala:** program meminta angka, pengguna mengetik `abc`, lalu program berputar tanpa henti.

**Penyebab:** `abc` tidak cocok dengan `%d`, sehingga `scanf` berhenti dan **meninggalkan `abc` di buffer**. Putaran berikutnya `scanf` membaca `abc` yang sama, gagal lagi, begitu seterusnya.

**Solusi:** periksa return value, bersihkan buffer, dan tangani `EOF`.

```c
#include <stdio.h>

static void bersihkan_buffer(void) {
    int c;
    while ((c = getchar()) != '\n' && c != EOF) { }
}

int main(void) {
    int pilihan;

    while (1) {
        printf("Masukkan menu pilihan (1-3): ");
        int status = scanf("%d", &pilihan);

        if (status == EOF) {                       // input habis: JANGAN mengulang
            printf("\nInput berakhir. Program dihentikan.\n");
            return 1;
        }

        bersihkan_buffer();                        // buang sisa baris (termasuk '\n')

        if (status != 1) {                         // 0 = bukan angka
            printf("[!] Input salah! Harap masukkan bilangan bulat.\n");
            continue;
        }

        if (pilihan >= 1 && pilihan <= 3) {
            printf("[OK] Pilihan %d diterima!\n", pilihan);
            break;
        }
        printf("[!] Angka di luar jangkauan! Masukkan 1 sampai 3.\n");
    }

    return 0;
}
```

> [!NOTE]
> Pola ini masih menerima `2abc` sebagai `2` (karena `abc` dibuang diam-diam), dan perilaku `%d` saat angkanya melebihi batas `int` tidak terdefinisi. Untuk validasi yang benar-benar ketat, Modul 7.3 memperkenalkan pola `fgets` + `strtol`.

---

## 💻 9. Studi Kasus: Form Pendaftaran Mahasiswa Anti-Crash

Program ini menggabungkan field width, scanset, pembersihan sisa input, penanganan `EOF`, dan validasi angka:

```c
#include <stdio.h>
#include <ctype.h>

static void bersihkan_buffer(void) {
    int c;
    while ((c = getchar()) != '\n' && c != EOF) { }
}

int main(void) {
    char nim[15];
    char nama_lengkap[50];
    int semester;
    char kelas;

    printf("==========================================\n");
    printf("      FORMULIR REGISTRASI PRAKTIKUM       \n");
    printf("==========================================\n");

    // 1. NIM: maksimal 14 karakter, tanpa spasi
    printf("Masukkan NIM           : ");
    if (scanf("%14s", nim) != 1) return 1;
    bersihkan_buffer();                       // buang kelebihan jika NIM > 14 karakter

    // 2. Nama lengkap (berspasi), dibaca sampai Enter
    printf("Masukkan Nama Lengkap  : ");
    if (scanf(" %49[^\n]", nama_lengkap) != 1) return 1;
    bersihkan_buffer();

    // 3. Semester 1-8 dengan validasi
    while (1) {
        printf("Masukkan Semester (1-8): ");
        int status = scanf("%d", &semester);
        if (status == EOF) return 1;          // input habis: keluar, jangan mengulang
        bersihkan_buffer();
        if (status == 1 && semester >= 1 && semester <= 8) break;
        printf("   [!] Semester harus berupa angka 1 s.d. 8!\n");
    }

    // 4. Kelas A/B/C
    while (1) {
        printf("Masukkan Kelas (A/B/C) : ");
        if (scanf(" %c", &kelas) != 1) return 1;
        bersihkan_buffer();
        kelas = (char)toupper((unsigned char)kelas);
        if (kelas >= 'A' && kelas <= 'C') break;
        printf("   [!] Kelas harus A, B, atau C!\n");
    }

    printf("\n==========================================\n");
    printf("         DATA BERHASIL TERSIMPAN          \n");
    printf("==========================================\n");
    printf("NIM          : %s\n", nim);
    printf("Nama Lengkap : %s\n", nama_lengkap);
    printf("Semester     : %d\n", semester);
    printf("Kelas        : %c\n", kelas);
    printf("==========================================\n");

    return 0;
}
```

---

## 📌 Checklist Penggunaan `scanf` untuk Praktikan

- [ ] Sertakan `&` untuk variabel tunggal (`int`, `float`, `double`, `char`). **Jangan** pakai `&` untuk array `char`.
- [ ] Cocokkan specifier dengan tipe: `float` → `%f`, **`double` → `%lf`**.
- [ ] Jangan pernah `scanf("%s", buf)` tanpa batas, gunakan `scanf("%19s", buf)`.
- [ ] Beri spasi sebelum karakter tunggal: `scanf(" %c", &ch)`.
- [ ] Baca teks berspasi dengan `scanf(" %49[^\n]", teks)`.
- [ ] Selalu periksa return value, dan bedakan `0` (salah format) dari `EOF` (input habis).
- [ ] Buang sisa baris dengan `bersihkan_buffer()` yang **memeriksa `EOF`**.
- [ ] Untuk input krusial, pelajari pola `fgets` + `sscanf`/`strtol` di Modul 7.3.

---

<div align="center">

[⬅️ Sebelumnya: Modul 7.1 Printf Formats](01-Printf-Formats.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke Modul 7.3: String I/O & Input Aman ➡️](03-String-IO-dan-Input-Aman.md)

</div>
