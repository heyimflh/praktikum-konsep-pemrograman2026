[⬅️ Kembali ke README Bab 2](README.md)

# 2.3 Operator Assignment & Aritmatika

## Operator Assignment

Operator paling dasar yang sudah sering dipakai tanpa disadari:

```c
int x = 10;   // '=' adalah operator assignment: "berikan nilai 10 ke x"
```

C juga menyediakan **compound assignment** — penulisan singkat untuk operasi + assignment sekaligus:

| Operator | Contoh | Setara Dengan |
|---|---|---|
| `+=` | `x += 5;` | `x = x + 5;` |
| `-=` | `x -= 3;` | `x = x - 3;` |
| `*=` | `x *= 2;` | `x = x * 2;` |
| `/=` | `x /= 4;` | `x = x / 4;` |
| `%=` | `x %= 3;` | `x = x % 3;` |

## Operator Aritmatika

| Operator | Arti | Contoh | Hasil |
|---|---|---|---|
| `+` | Penjumlahan | `5 + 3` | `8` |
| `-` | Pengurangan | `5 - 3` | `2` |
| `*` | Perkalian | `5 * 3` | `15` |
| `/` | Pembagian | `5 / 2` | `2` (integer!) |
| `%` | Modulo (sisa bagi) | `5 % 2` | `1` |

### ⚠️ Jebakan Pembagian Integer

Ini salah satu sumber bug paling sering ditemui pemula:

```c
int a = 7, b = 2;
printf("%d\n", a / b);            // Output: 3  (bukan 3.5!)
printf("%.2f\n", (float)a / b);   // Output: 3.50 -> pakai type casting
```

Karena `a` dan `b` sama-sama `int`, hasil pembagian **dibulatkan ke bawah** (dibuang bagian desimalnya), bukan dibulatkan matematis. Untuk mendapat hasil desimal, **cast** salah satu operand menjadi `float`/`double` dengan `(float)` sebelum pembagian.

### Operator Increment & Decrement

```c
int i = 5;
i++;   // sama dengan i = i + 1  -> i menjadi 6
i--;   // sama dengan i = i - 1  -> i menjadi 5 lagi
```

Ada dua bentuk yang perilakunya berbeda saat digunakan langsung dalam ekspresi:

```c
int a = 5;
printf("%d\n", a++);  // Output: 5 (nilai dipakai DULU, baru ditambah -> post-increment)
printf("%d\n", a);    // Output: 6

int b = 5;
printf("%d\n", ++b);  // Output: 6 (ditambah DULU, baru dipakai -> pre-increment)
```

## Operator Precedence (Urutan Operasi)

Sama seperti matematika, C punya urutan prioritas operator. Urutan singkatnya (dari tertinggi):

```
1. ( )               -> tanda kurung
2. ++ --              -> increment/decrement
3. * / %              -> perkalian, pembagian, modulo
4. + -                -> penjumlahan, pengurangan
5. =  +=  -=  dst      -> assignment (paling akhir dieksekusi)
```

```c
int hasil = 2 + 3 * 4;      // = 2 + 12 = 14 (BUKAN 20)
int hasil2 = (2 + 3) * 4;   // = 5 * 4 = 20  (kurung mengubah urutan)
```

> 💡 Kalau ragu urutan operasi rumit, **selalu gunakan kurung eksplisit**. Kode jadi lebih mudah dibaca dan bebas dari bug precedence.

## 🧪 Latihan Cepat

1. Tebak dulu output kode berikut sebelum menjalankannya:
   ```c
   int a = 10, b = 3;
   printf("%d\n", a % b);
   printf("%d\n", a / b);
   printf("%.2f\n", (float)a / b);
   ```
2. Buat program konversi suhu Celsius ke Fahrenheit: `F = (C * 9/5) + 32`. Perhatikan potensi jebakan pembagian integer pada `9/5`!
3. Buat program yang menghitung luas dan keliling lingkaran dari jari-jari yang diinput pengguna (`π` gunakan `3.14159`).
4. Buat program sederhana kalkulator (`+ - * /`) dua bilangan bulat yang diinput pengguna, dengan hasil pembagian ditampilkan sebagai desimal 2 angka di belakang koma.

---
⬅️ [Sebelumnya: Algoritma, Pseudocode & Source Code](02-Algoritma-Pseudocode-SourceCode.md) | ➡️ [Lanjut: Percabangan](04-Percabangan.md)
