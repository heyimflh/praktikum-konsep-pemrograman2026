<div align="center">

# 🌀 4.4 Rekursi (Recursion)
### Bab 4: C Function · Seni Memanggil Diri Sendiri yang Memukau

[⬅️ Modul 4.3: Aturan Scope](03-Aturan-Scope.md) &nbsp;•&nbsp; [Overview Bab 4](Bab4-Overview.md) &nbsp;•&nbsp; [📋 Kembali ke Daftar Materi](../Daftar_Materi.md)

---

</div>

## 💡 Apa itu Rekursi? (Analogi Boneka Matryoshka)

Pernahkah kamu melihat boneka kayu tradisional Rusia (*Matryoshka*)? Ketika kamu membuka boneka yang paling besar, di dalamnya ada boneka yang lebih kecil. Buka lagi, ada boneka yang lebih kecil lagi, hingga akhirnya kamu menemukan boneka terkecil yang **tidak bisa dibuka lagi**.

Dalam ilmu komputer, **Rekursi** adalah teknik pemrograman di mana sebuah fungsi **memanggil dirinya sendiri** untuk menyelesaikan versi masalah yang lebih kecil, sampai mencapai kondisi paling sederhana yang disebut **Base Case**.

---

## 🏛️ Dua Hukum Mutlak Rekursi

Sebuah fungsi rekursif yang benar **WAJIB** memiliki dua komponen ini:

1. **Base Case (Kasus Dasar / Titik Henti):** Kondisi di mana fungsi langsung memberikan jawaban tanpa memanggil dirinya lagi. Tanpa base case, program akan mengalami **Stack Overflow (Crash)**!
2. **Recursive Step (Langkah Rekursif):** Pemanggilan fungsi itu sendiri dengan argumen yang **semakin kecil / semakin dekat** menuju base case.

```mermaid
flowchart TD
    subgraph Fase Penumpukan (Winding / Panggilan)
        F5["hitung(5) = hitung(4) + 5"] --> F4["hitung(4) = hitung(3) + 4"]
        F4 --> F3["hitung(3) = hitung(2) + 3"]
        F3 --> F2["hitung(2) = hitung(1) + 2"]
        F2 --> F1["hitung(1) = 1 (BASE CASE!)"]
    end
    
    subgraph Fase Pengembalian (Unwinding / Hasil)
        F1 -->|Return 1| R2["1 + 2 = 3"]
        R2 -->|Return 3| R3["3 + 3 = 6"]
        R3 -->|Return 6| R4["6 + 4 = 10"]
        R4 -->|Return 10| R5["10 + 5 = 15 🎉"]
    end
```

---

## 💻 Studi Kasus Klasik 1: Faktorial Matematika ($n!$)

Rumus matematis faktorial:
$$n! = n \times (n - 1)!$$
$$0! = 1 \quad \text{dan} \quad 1! = 1 \quad \text{(Base Case)}$$

Contoh: $5! = 5 \times 4 \times 3 \times 2 \times 1 = 120$.

```c
#include <stdio.h>

// Fungsi rekursif menghitung faktorial
long long faktorial(int n) {
    // 1. BASE CASE: Titik henti
    if (n <= 1) {
        return 1;
    }
    // 2. RECURSIVE STEP: Mendekati base case
    return n * faktorial(n - 1);
}

int main() {
    int angka = 5;
    printf("Nilai dari %d! adalah: %lld\n", angka, faktorial(angka));
    return 0;
}
```

---

## 💻 Studi Kasus 2: Penjumlahan Deret Bilangan Asli

Mari kita bedah program untuk menghitung jumlah deret: $1 + 2 + 3 + \dots + n$.

```c
#include <stdio.h>

int hitungDeret(int n) {
    // Base Case: jika n bernilai 1, hentikan pemanggilan
    if (n == 1) {
        return 1;
    }
    // Recursive Step
    return n + hitungDeret(n - 1);
}

int main() {
    int n;

    printf("==============================\n");
    printf("   PENGHITUNG DERET REKURSIF  \n");
    printf("==============================\n");
    printf("Masukkan nilai batas (n): ");
    scanf("%d", &n);

    if (n < 1) {
        printf("Masukkan bilangan bulat positif >= 1!\n");
        return 1;
    }

    int total = hitungDeret(n);
    printf("Total 1 + 2 + ... + %d = %d\n", n, total);

    return 0;
}
```

---

## ⚠️ Bahaya Fatal: *Stack Overflow Error*

> [!CAUTION]
> Setiap kali fungsi memanggil dirinya sendiri, komputer mengalokasikan memori baru di dalam **Call Stack** untuk menyimpan variabel dan alamat kembali fungsi tersebut.  
> Jika kamu:
> 1. Lupa membuat *Base Case*, atau
> 2. Argumen rekursif justru menjauhi *Base Case* (misal: malah menulis `n + 1`),
>
> Maka fungsi akan terus memanggil dirinya tanpa henti sampai **memori Stack komputer habis**. Programmu akan langsung berhenti paksa (*crash*) dengan pesan kesalahan **Segmentation Fault (Stack Overflow)**!

---

## ⚖️ Rekursi vs Perulangan Biasa (Iterasi)

| Aspek | Rekursi | Iterasi (`for` / `while`) |
|---|---|---|
| **Struktur Kode** | Sangat elegan, ringkas, dan matematis. | Terkadang membutuhkan variabel counter ekstra. |
| **Penggunaan Memori** | Mengonsumsi memori Stack untuk tiap panggilan fungsi. | Sangat hemat memori (variabel tetap di tempat). |
| **Kecepatan Eksekusi** | Lebih lambat karena ada *overhead* pemanggilan fungsi. | Lebih cepat dan efisien di level instruksi CPU. |
| **Paling Cocok Untuk** | Struktur data pohon (*tree*), graf, algoritma *Divide & Conquer*. | Perulangan sekuensial sederhana dan pemrosesan array. |

---

## 🥊 Tantangan & Mini Kuis

### Soal: Deret Fibonacci Rekursif
Deret Fibonacci didefinisikan sebagai:
- $F(0) = 0$
- $F(1) = 1$
- $F(n) = F(n-1) + F(n-2)$ untuk $n \ge 2$

Deret: `0, 1, 1, 2, 3, 5, 8, 13, 21, ...`  
Coba rancang fungsi rekursif `fibonacci(int n)` di bawah ini!

<details>
<summary>🔍 <b>Klik untuk Buka Kunci Solusi</b></summary>

```c
#include <stdio.h>

int fibonacci(int n) {
    // Base Case ganda
    if (n == 0) return 0;
    if (n == 1) return 1;

    // Recursive Step bercabang dua
    return fibonacci(n - 1) + fibonacci(n - 2);
}

int main() {
    int n = 7;
    printf("Bilangan Fibonacci ke-%d adalah: %d\n", n, fibonacci(n));
    return 0;
}
```
*(Output: `Bilangan Fibonacci ke-7 adalah: 13`)*
</details>

---

<div align="center">

### 🏆 Selamat! Kamu Telah Menyelesaikan Seluruh Modul Bab 4!

[⬅️ Sebelumnya: 4.3 Aturan Scope](03-Aturan-Scope.md) &nbsp;&nbsp;|&nbsp;&nbsp; [📋 Kembali ke Daftar Materi Utama](../Daftar_Materi.md)

</div>
