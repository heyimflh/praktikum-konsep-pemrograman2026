<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=e65100&height=150&section=header&text=8.2%20Bit%20Manipulation&fontSize=35&fontColor=ffffff&animation=fadeIn&desc=Operasi%20Tingkat%20Rendah%3A%20Bitwise%20dan%20Shift%20Operators&descSize=14&descAlignY=75)

[⬅️ Modul 8.1: Struct dan Union](01-Struct-dan-Union.md) &nbsp;•&nbsp; [📋 Daftar Materi](../Daftar_Materi.md) &nbsp;•&nbsp; [Modul 8.3: Enumeration ➡️](03-Enumeration.md)

---

</div>

## 🎯 Tujuan Pembelajaran

Setelah mempelajari modul ini, kamu diharapkan mampu:
1. Memahami bagaimana bilangan bulat direpresentasikan dalam bit biner pada arsitektur komputer.
2. Membuat fungsi utilitas untuk mencetak representasi biner suatu bilangan.
3. Menggunakan operator bitwise: AND (`&`), OR (`|`), XOR (`^`), dan NOT (`~`).
4. Menggunakan operator pergeseran bit: Left Shift (`<<`) dan Right Shift (`>>`).
5. Menerapkan konsep *bitmasking* untuk memeriksa, menyalakan, mematikan, dan membalik status flag bit.

---

## ⚡ Apa itu Bit Manipulation?

**Bit Manipulation** adalah teknik memanipulasi data langsung pada level bit (0 dan 1). Karena komputer pada dasarnya adalah mesin biner, operasi bitwise dieksekusi langsung oleh CPU dalam satu instruksi siklus mesin sehingga sangat cepat dan hemat sumber daya.

Penggunaan utama manipulasi bit meliputi:
- **Flag status & permissions:** Menghemat memori dengan menyimpan hingga 32 atau 64 status boolean dalam satu variabel `int`.
- **Sistem embedded & driver:** Berkomunikasi dengan register mikrokontroler/hardware.
- **Kriptografi & hashing:** Algoritma enkripsi seperti AES, SHA-256 sangat bergantung pada operasi XOR dan rotasi bit.
- **Kompresi data:** Mengemas bit informasi tanpa terbuang pada batas *byte*.

---

## 🖥️ Mencetak Representasi Biner di C

Bahasa C tidak memiliki format specifier bawaan di `printf` untuk biner (hanya `%x` untuk heksadesimal dan `%o` untuk oktal). Oleh karena itu, kita dapat membuat fungsi utilitas sendiri menggunakan operasi bit:

```c
#include <stdio.h>

void printBinary(unsigned int n, int bit_length) {
    for (int i = bit_length - 1; i >= 0; i--) {
        int bit = (n >> i) & 1;
        printf("%d", bit);
        if (i % 4 == 0 && i != 0) printf(" "); // Pemisah setiap 4 bit (nibble)
    }
}

int main() {
    printf("5  dalam 8-bit biner = ");
    printBinary(5, 8);
    printf("\n");

    printf("18 dalam 8-bit biner = ");
    printBinary(18, 8);
    printf("\n");

    return 0;
}
```

**Output:**
```
5  dalam 8-bit biner = 0000 0101
18 dalam 8-bit biner = 0001 0010
```

---

## 🧮 Operator Bitwise di C

| Operator | Nama Operator | Cara Kerja |
|:---:|:---|:---|
| `&` | **Bitwise AND** | Menghasilkan `1` jika **kedua** bit bernilai `1` |
| `\|` | **Bitwise OR** | Menghasilkan `1` jika **salah satu atau kedua** bit bernilai `1` |
| `^` | **Bitwise XOR** | Menghasilkan `1` jika kedua bit **berbeda** nilainya |
| `~` | **Bitwise NOT (Unary)** | Membalik semua bit (`0` jadi `1`, `1` jadi `0`) |
| `<<` | **Left Shift** | Menggeser bit ke kiri (mengalikan dengan $2^n$) |
| `>>` | **Right Shift** | Menggeser bit ke kanan (membagi dengan $2^n$) |

---

### 1. Bitwise AND (`&`)

Hanya menghasilkan `1` jika bit di posisi yang sama pada kedua operan bernilai `1`:

```
   0101  (5)
   0011  (3)
   ---- &
   0001  (1)
```

```c
#include <stdio.h>

int main() {
    int a = 5, b = 3;
    printf("%d & %d = %d\n", a, b, a & b); // Output: 1
    return 0;
}
```

> [!TIP]
> **Trik Cepat:** Untuk mengecek apakah suatu bilangan `n` ganjil atau genap, cukup lakukan `n & 1`. Jika hasilnya `1` maka ganjil, jika `0` maka genap!

---

### 2. Bitwise OR (`|`)

Menghasilkan `1` jika ada setidaknya satu bit bernilai `1`:

```
   0101  (5)
   0011  (3)
   ---- |
   0111  (7)
```

```c
#include <stdio.h>

int main() {
    int a = 5, b = 3;
    printf("%d | %d = %d\n", a, b, a | b); // Output: 7
    return 0;
}
```

---

### 3. Bitwise XOR (`^`)

Menghasilkan `1` jika kedua bit memiliki nilai yang berbeda (*exclusive OR*):

```
   0101  (5)
   0011  (3)
   ---- ^
   0110  (6)
```

```c
#include <stdio.h>

int main() {
    int a = 5, b = 3;
    printf("%d ^ %d = %d\n", a, b, a ^ b); // Output: 6
    return 0;
}
```

> [!NOTE]
> Properti istimewa XOR:
> - `x ^ 0 = x`
> - `x ^ x = 0`
> - Menukar dua variabel tanpa variabel penampung:
>   ```c
>   a ^= b; b ^= a; a ^= b;
>   ```

---

### 4. Bitwise NOT (`~`)

Membalik semua bit (operasi komplemen satu). Ingat representasi bilangan bertanda menggunakan *two's complement*, sehingga `~x = -(x + 1)`.

```c
#include <stdio.h>

int main() {
    unsigned char x = 5; // 0000 0101
    // ~x menjadi 1111 1010 (250 dalam unsigned char)
    printf("~%u = %u\n", x, (unsigned char)(~x));
    return 0;
}
```

---

### 5. Operator Pergeseran Bit (Shift Operators)

#### Left Shift (`<<`)
Menggeser bit ke arah kiri sejauh $n$ posisi. Bit kosong di sebelah kanan diisi nol. Menggeser ke kiri 1 kali setara dengan mengalikan bilangan dengan 2.

```
Bilangan 51 (8-bit): 0011 0011
51 << 1             : 0110 0110 (102)
51 << 2             : 1100 1100 (204)
```

#### Right Shift (`>>`)
Menggeser bit ke arah kanan sejauh $n$ posisi. Menggeser ke kanan 1 kali setara dengan membagi bilangan dengan 2 (pembagian bulat).

```
Bilangan 51 (8-bit): 0011 0011
51 >> 1             : 0001 1001 (25)
51 >> 2             : 0000 1100 (12)
```

```c
#include <stdio.h>

int main() {
    int val = 51;

    printf("%d << 1 = %d (dikali 2)\n", val, val << 1);
    printf("%d >> 1 = %d (dibagi 2)\n", val, val >> 1);

    return 0;
}
```

---

## 🛠️ Praktik Nyata: Bitmasking (Flag & Permission)

Bayangkan sebuah game RPG di mana status pemain disimpan dalam satu byte:
- Bit 0: Beracun (`POISONED` = `1 << 0`) -> `0001`
- Bit 1: Terbakar (`BURNING` = `1 << 1`)  -> `0010`
- Bit 2: Beku (`FROZEN` = `1 << 2`)       -> `0100`
- Bit 3: Kebal (`INVINCIBLE` = `1 << 3`)  -> `1000`

```c
#include <stdio.h>

#define STATUS_POISONED   (1 << 0) // 0001
#define STATUS_BURNING    (1 << 1) // 0010
#define STATUS_FROZEN     (1 << 2) // 0100
#define STATUS_INVINCIBLE (1 << 3) // 1000

int main() {
    unsigned char playerStatus = 0; // Tidak ada efek aktif

    // 1. Menyalakan status BURNING dan POISONED (menggunakan OR |)
    playerStatus |= STATUS_BURNING | STATUS_POISONED;

    // 2. Memeriksa apakah player terkena efek BURNING (menggunakan AND &)
    if (playerStatus & STATUS_BURNING) {
        printf("[!] Player sedang terbakar!\n");
    }

    // 3. Menghilangkan status POISONED setelah minum obat (menggunakan AND ~)
    playerStatus &= ~STATUS_POISONED;

    // 4. Membalik status FROZEN (menggunakan XOR ^)
    playerStatus ^= STATUS_FROZEN;

    return 0;
}
```

---

<div align="center">

[⬅️ Sebelumnya: Modul 8.1 Struct dan Union](01-Struct-dan-Union.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 8.3: Enumeration ➡️](03-Enumeration.md)

</div>

