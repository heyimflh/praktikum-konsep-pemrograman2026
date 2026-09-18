<div align="center">

# ⚡ 2.3 Operator Aritmatika & Assignment
### Bab 2: Structured Programming · Mesin Hitung Komputer

[⬅️ Modul 2.2: Algoritma & Pseudocode](02-Algoritma-Pseudocode-SourceCode.md) &nbsp;•&nbsp; [Overview Bab 2](Bab2-Overview.md) &nbsp;•&nbsp; [Modul 2.4: Pemilihan & Perulangan ➡️](04-PemilihandanPerulanganSederhana.md)

---

</div>

## 🧮 Dunia Operator dalam Bahasa C

Komputer pada dasarnya adalah kalkulator raksasa berkecepatan miliaran instruksi per detik. Untuk melakukan kalkulasi dan manipulasi data, bahasa C menyediakan berbagai macam **operator**. 

Di modul ini, kita akan mengupas tuntas:
1. Operator Assignment (Penugasan)
2. Operator Aritmatika & Jebakan Integer Division
3. Perbedaan Kritis Pre-increment vs Post-increment
4. Hierarki Prioritas Operator (*Precedence*)

---

## 1️⃣ Operator Assignment (Penugasan)

Operator assignment dasar adalah tanda sama dengan (`=`). Simbol ini **bukan** menyatakan kesamaan matematis, melainkan instruksi: *"Ambil nilai dari ruas kanan, lalu simpan ke dalam variabel di ruas kiri!"*

```c
int skor = 100;  // Masukkan angka 100 ke dalam kotak bernama skor
```

### Compound Assignment (Penulisan Cepat)

Bahasa C menyediakan singkatan (*shorthand*) elegan untuk memodifikasi nilai variabel dengan nilai lamanya:

| Operator | Contoh Penggunaan | Makna / Setara Dengan | Contoh Kasus (Awal: `x = 10`) | Hasil Akhir `x` |
|:---:|---|---|---|:---:|
| `+=` | `x += 5;` | `x = x + 5;` | Nilai `x` ditambah 5 | `15` |
| `-=` | `x -= 3;` | `x = x - 3;` | Nilai `x` dikurang 3 | `7` |
| `*=` | `x *= 2;` | `x = x * 2;` | Nilai `x` dikali 2 | `20` |
| `/=` | `x /= 4;` | `x = x / 4;` | Nilai `x` dibagi 4 | `2` |
| `%=` | `x %= 3;` | `x = x % 3;` | Sisa bagi `x` dengan 3 | `1` |

---

## 2️⃣ Operator Aritmatika

| Simbol | Nama Operasi | Contoh Kode | Hasil |
|:---:|---|---|:---:|
| `+` | Penjumlahan | `7 + 3` | `10` |
| `-` | Pengurangan | `7 - 3` | `4` |
| `*` | Perkalian | `7 * 3` | `21` |
| `/` | Pembagian | `7 / 2` | `3` *(Awas: bukan 3.5!)* |
| `%` | Modulo (Sisa Bagi Bulat) | `7 % 3` | `1` *(karena $7 = 3 \times 2 + 1$)* |

> [!WARNING]
> ### 🚨 Jebakan Batman: Pembagian Integer & Solusi Type Casting
> Di dalam bahasa C:
> - `integer / integer` menghasilkan **integer** (seluruh pecahan desimal dipotong habis / dibuang ke bawah).
> - `float / integer` atau `integer / float` menghasilkan **float**.
>
> ```c
> int a = 7, b = 2;
> printf("%d\n", a / b);            // Output: 3  (Bukan 3.5!)
> printf("%.2f\n", (float)a / b);   // Output: 3.50 (Solusi: Type Casting!)
> ```
> Dengan menuliskan `(float)a`, kita mengubah nilai variabel `a` menjadi desimal `7.0f` untuk operasi tersebut, sehingga komputasi menjadi `7.0f / 2 = 3.5f`.

---

## 3️⃣ Increment & Decrement: Pre vs Post

Operator `++` (tambah 1) dan `--` (kurang 1) sangat populer dalam perulangan loop. Namun, posisinya menentukan kapan penambahan nilai terjadi!

```
a++ (Post-Increment) : "Gunakan nilainya sekarang, baru tambahkan setelahnya."
++a (Pre-Increment)  : "Tambahkan nilainya dulu, baru gunakan nilai barunya."
```

### Visualisasi Perbandingan:

```c
// Kasus 1: Post-Increment (a++)
int a = 5;
int hasil1 = a++; 
// Langkah 1: hasil1 diisi nilai a saat ini (5)
// Langkah 2: a naik menjadi 6
// Nilai akhir: hasil1 = 5, a = 6

// Kasus 2: Pre-Increment (++b)
int b = 5;
int hasil2 = ++b; 
// Langkah 1: b langsung naik menjadi 6
// Langkah 2: hasil2 diisi nilai b yang baru (6)
// Nilai akhir: hasil2 = 6, b = 6
```

---

## 4️⃣ Operator Precedence (Hierarki Prioritas)

Sama seperti aturan matematika PEMDAS / Kabataku, bahasa C memiliki tingkatan prioritas kapan suatu operator dievaluasi:

| Prioritas | Kategori Operator | Simbol | Arah Asosiasi |
|:---:|---|---|:---:|
| **1 (Tertinggi)** | Kurung Grouping | `( )` | Kiri ke Kanan |
| **2** | Unary / Increment | `++`, `--`, `+`, `-` | Kanan ke Kiri |
| **3** | Perkalian / Pembagian / Sisa | `*`, `/`, `%` | Kiri ke Kanan |
| **4** | Penjumlahan / Pengurangan | `+`, `-` | Kiri ke Kanan |
| **5 (Terendah)** | Penugasan (*Assignment*) | `=`, `+=`, `-=`, `*=`, `/=` | Kanan ke Kiri |

> [!TIP]
> **Pro-Tip:** Jika kamu tidak yakin urutan prioritas suatu rumus yang panjang, **selalu pasang tanda kurung `( )` secara eksplisit**. Kodinganmu akan jauh lebih mudah dibaca rekan satu tim dan aman dari bug tersembunyi!

---

## 🥊 Tantangan & Tebak Output

### Kuis 1: Analisis Ekspresi
Tebak nilai dari variabel `z` berikut sebelum melihat pembahasannya:
```c
int x = 4;
int y = 3;
int z = (x++) * 2 + (--y);
```
Berapakah nilai `z`, `x`, dan `y` di akhir?

<details>
<summary>🔍 <b>Klik untuk Buka Kunci Jawaban</b></summary>

**Jawaban:**
- Nilai `z` = **10**
- Nilai `x` = **5**
- Nilai `y` = **2**

**Langkah Penjelasan:**
1. `(x++)` mengevaluasi nilai lama `x` yaitu `4`, lalu menjadwalkan penambahan `x` menjadi `5`.
2. `(--y)` langsung mengurangi `y` dari `3` menjadi `2`, dan menggunakan nilai `2`.
3. Komputasi: `4 * 2 + 2 = 8 + 2 = 10`.
4. Hasil `10` disimpan ke variabel `z`.
</details>

---

### Kuis 2: Latihan Konversi Suhu
Buatlah program C bernama `suhu.c` yang meminta input suhu dalam derajat **Celcius** (tipe data `float`), lalu menghitung dan mencetak suhunya dalam **Fahrenheit** dengan rumus:
$$F = \left(C \times \frac{9}{5}\right) + 32$$

> ⚠️ **Peringatan:** Jika kamu menulis `9/5` di C, hasilnya adalah `1` (karena pembagian integer). Pastikan menulis `9.0f / 5.0f` atau `(float)9 / 5`!

<details>
<summary>💻 <b>Klik untuk Melihat Solusi Program C</b></summary>

```c
#include <stdio.h>

int main() {
    float celsius, fahrenheit;

    printf("Masukkan suhu dalam Celsius: ");
    scanf("%f", &celsius);

    // Menggunakan 9.0f / 5.0f untuk menjamin komputasi desimal
    fahrenheit = (celsius * (9.0f / 5.0f)) + 32.0f;

    printf("%.2f °C = %.2f °F\n", celsius, fahrenheit);
    return 0;
}
```
</details>

---

<div align="center">

[⬅️ Sebelumnya: 2.2 Algoritma & Pseudocode](02-Algoritma-Pseudocode-SourceCode.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 2.4: Pemilihan & Perulangan Sederhana ➡️](04-PemilihandanPerulanganSederhana.md)

</div>
