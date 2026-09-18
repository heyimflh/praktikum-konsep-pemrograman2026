<div align="center">

# 📚 4.2 Fungsi-Fungsi Dasar Library C
### Bab 4: C Function · Kekuatan Pustaka Standar (Built-in Libraries)

[⬅️ Modul 4.1: Pengenalan Function](01-Pengenalan-Function.md) &nbsp;•&nbsp; [Overview Bab 4](Bab4-Overview.md) &nbsp;•&nbsp; [Modul 4.3: Aturan Scope ➡️](03-Aturan-Scope.md)

---

</div>

## 💡 Jangan Membuat Roda dari Nol!

Di dunia nyata, programmer yang hebat bukanlah yang menulis segala sesuatunya dari nol (*re-inventing the wheel*), melainkan yang tahu **kapan dan bagaimana memanfaatkan pustaka standar (*standard library*)** yang sudah teruji, cepat, dan bebas bug.

Bahasa C menyertakan puluhan file pustaka bawaan yang disebut **Header Files** (berekstensi `.h`). Di modul ini, kita akan menjelajahi 4 pustaka paling sakti yang wajib dikuasai setiap mahasiswa:
1. **`<stdio.h>`** — Operasi Input dan Output data.
2. **`<math.h>`** — Kalkulasi fungsi matematika lanjutan.
3. **`<string.h>`** — Manipulasi teks dan array karakter.
4. **`<stdlib.h>`** — Utilitas sistem, konversi, dan angka acak.

---

## 1️⃣ Pustaka Input/Output: `<stdio.h>`

Pustaka ini adalah jantung komunikasi antara programmu dengan pengguna melalui konsol maupun manipulasi string internal.

| Nama Fungsi | Deskripsi Tugas | Bentuk Deklarasi Prototipe |
|---|---|---|
| `printf()` | Mencetak teks berformat ke layar terminal konsol. | `int printf(const char *format, ...);` |
| `scanf()` | Membaca data berformat dari input keyboard. | `int scanf(const char *format, ...);` |
| `sprintf()` | Menuliskan hasil format teks **ke dalam variabel string**, bukan ke layar! | `int sprintf(char *str, const char *format, ...);` |
| `sscanf()` | Membaca data berformat **dari sebuah string** yang sudah ada. | `int sscanf(const char *str, const char *format, ...);` |

### 🔍 Contoh Sakti `sprintf` dan `sscanf`:

```c
#include <stdio.h>

int main() {
    // sprintf: Merangkai beberapa variabel menjadi satu string utuh
    char bufferWaktu[100];
    int tgl = 18, thn = 2026;
    char bln[] = "September";

    sprintf(bufferWaktu, "%02d %s %d", tgl, bln, thn);
    printf("String hasil format: %s\n", bufferWaktu);

    // sscanf: Membongkar string teks kembali menjadi variabel terpisah
    char teks[] = "Budi 21 3.85";
    char nama[50];
    int umur;
    float ipk;

    sscanf(teks, "%s %d %f", nama, &umur, &ipk);
    printf("Hasil Ekstraksi -> Nama: %s | Umur: %d thn | IPK: %.2f\n", nama, umur, ipk);

    return 0;
}
```

---

## 2️⃣ Pustaka Matematika: `<math.h>`

Menyediakan fungsi kalkulasi ilmiah yang tidak memiliki operator simbol langsung di keyboard.

| Fungsi | Deskripsi | Contoh | Hasil |
|---|---|---|:---:|
| `pow(x, y)` | Menghitung perpangkatan $x^y$ | `pow(2.0, 3.0)` | `8.00` |
| `sqrt(x)` | Menghitung akar kuadrat $\sqrt{x}$ | `sqrt(49.0)` | `7.00` |
| `ceil(x)` | Pembulatan ke atas (*ceiling*) | `ceil(4.1)` | `5.00` |
| `floor(x)` | Pembulatan ke bawah (*lantai*) | `floor(4.9)` | `4.00` |
| `fabs(x)` | Nilai mutlak / absolut desimal $|x|$ | `fabs(-12.5)` | `12.50` |
| `sin(rad)` | Nilai sinus sudut (dalam radian) | `sin(3.14159 / 2)` | `1.00` |
| `log10(x)` | Logaritma basis 10 | `log10(100.0)` | `2.00` |

> [!TIP]
> ### 🐧 Catatan Kompilasi di Linux / Git Bash
> Jika kamu meng-compile program yang menggunakan `<math.h>` di Linux dan menemui error `undefined reference to 'sqrt'`, tambahkan flag linker **`-lm`** di akhir perintah kompilasi:
> ```bash
> gcc program.c -o program -lm
> ```

---

## 3️⃣ Pustaka Manipulasi String: `<string.h>`

Karena di bahasa C tipe data string adalah array karakter (`char[]`), kamu **tidak bisa** membandingkan atau menyalin string dengan operator biasa seperti `str1 = str2` atau `str1 == str2`. Di sinilah fungsi `<string.h>` berperan:

| Fungsi | Tugas | Contoh Sintaks | Keterangan |
|---|---|---|---|
| `strlen(s)` | Menghitung panjang karakter | `int p = strlen("Halo");` | Hasil = `4` (karakter null `\0` tidak dihitung) |
| `strcpy(dest, src)` | Menyalin isi string `src` ke `dest` | `strcpy(tujuan, sumber);` | Menggantikan assignment `=` |
| `strcat(dest, src)` | Menggabungkan (*concatenate*) `src` ke ujung `dest` | `strcat(salam, " Dunia");` | Menempelkan di belakang |
| `strcmp(s1, s2)` | Membandingkan kesamaan dua string | `if (strcmp(a, b) == 0)` | **Mengembalikan 0 jika SAMA PERSIS** |
| `strstr(hay, needle)` | Mencari posisi kata dalam kalimat | `strstr("Informatika", "mat")` | Mengembalikan pointer posisi kata |

### 🔍 Contoh Operasi String:

```c
#include <stdio.h>
#include <string.h>

int main() {
    char passwordBenar[] = "rahasia123";
    char inputUser[50];

    printf("Masukkan password sistem: ");
    scanf("%s", inputUser);

    // strcmp menghasilkan 0 jika kedua string identik!
    if (strcmp(inputUser, passwordBenar) == 0) {
        printf("✅ Akses Diterima! Selamat datang.\n");
    } else {
        printf("❌ Password salah! Panjang inputmu: %lu karakter.\n", strlen(inputUser));
    }

    return 0;
}
```

---

## 4️⃣ Pustaka Standar & Angka Acak: `<stdlib.h>`

| Fungsi | Deskripsi Singkat |
|---|---|
| `rand()` | Menghasilkan bilangan bulat acak (*pseudorandom*) antara `0` s.d. `RAND_MAX`. |
| `srand(seed)` | Mengatur "biji benih" (*seed*) pembangkit angka acak agar hasil selalu bervariasi. |
| `exit(status)` | Menghentikan paksa jalannya program seketika itu juga. |
| `system("command")`| Menjalankan instruksi terminal/CMD (contoh: `cls` di Windows atau `clear` di Linux). |

### 🎲 Cara Menghasilkan Angka Acak Sempurna (Random Range):

Fungsi `rand()` jika dipanggil sendiri akan selalu menghasilkan urutan angka yang sama persis setiap program dijalankan. Agar angkanya benar-benar acak, kita harus menginisialisasi benihnya menggunakan waktu sistem saat ini melalui pustaka `<time.h>`:

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int main() {
    // 1. Inisialisasi seed dengan waktu saat ini
    srand(time(NULL));

    // Rumus rentang angka acak [MIN s.d. MAX]:
    // angka = MIN + (rand() % (MAX - MIN + 1))
    
    // Contoh: Acak dadu 6 sisi (1 s.d. 6)
    int dadu = 1 + (rand() % 6);
    printf("🎲 Lemparan Dadu Anda: %d\n", dadu);

    // Contoh: Angka acak antara 10 s.d. 50
    int nilaiAcak = 10 + (rand() % (50 - 10 + 1));
    printf("🎯 Angka Acak (10 - 50): %d\n", nilaiAcak);

    return 0;
}
```

---

## 🥊 Tantangan & Mini Kuis

### Soal: Menghitung Sisi Miring Segitiga (Teorema Pythagoras)
Buatlah fungsi C yang meminta panjang dua sisi tegak segitiga siku-siku ($a$ dan $b$), lalu menghitung sisi miring ($c$) menggunakan rumus:
$$c = \sqrt{a^2 + b^2}$$
*Gunakan fungsi `sqrt()` dan `pow()` dari `<math.h>`!*

<details>
<summary>🔍 <b>Klik untuk Buka Kunci Solusi</b></summary>

```c
#include <stdio.h>
#include <math.h>

int main() {
    double a, b, c;

    printf("Masukkan panjang sisi A: ");
    scanf("%lf", &a);
    printf("Masukkan panjang sisi B: ");
    scanf("%lf", &b);

    c = sqrt(pow(a, 2.0) + pow(b, 2.0));

    printf("Panjang sisi miring C = %.2lf cm\n", c);
    return 0;
}
```
</details>

---

<div align="center">

[⬅️ Sebelumnya: 4.1 Pengenalan Function](01-Pengenalan-Function.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 4.3: Aturan Scope ➡️](03-Aturan-Scope.md)

</div>
