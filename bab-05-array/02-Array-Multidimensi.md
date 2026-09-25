<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=f57c00&height=150&section=header&text=5.2%20Array%20Multidimensi&fontSize=35&fontColor=ffffff&animation=fadeIn&desc=Merepresentasikan%20Data%20Tabel%20&%20Matriks&descSize=15&descAlignY=75)

[⬅️ Modul 5.1: Pengenalan Array](01-Pengenalan-Array.md) &nbsp;•&nbsp; [Overview Bab 5](Bab5-Overview.md) &nbsp;•&nbsp; [Modul 5.3: Array Sebagai Parameter Fungsi ➡️](03-Array-Sebagai-Parameter-Fungsi.md)

---

</div>

## 💡 Dari Satu Baris Menuju Tabel Data

Array 1 dimensi yang kamu pelajari sebelumnya bisa dianalogikan sebagai **satu baris kolom loker**. Namun, bagaimana jika kamu ingin merepresentasikan:
- Tabel nilai ujian (baris = mahasiswa, kolom = mata kuliah)?
- Peta piksel sebuah gambar (baris = y, kolom = x)?
- Papan catur atau permainan tic-tac-toe?

Di sinilah **Array Multidimensi** berperan — array yang memiliki lebih dari satu dimensi indeks!

**Perbandingan Visualisasi Array 1D vs 2D:**

<div align="center">

**Array 1D `nilai[5]` (Satu Baris)**

| `[0]` | `[1]` | `[2]` | `[3]` | `[4]` |
|:---:|:---:|:---:|:---:|:---:|
| <kbd>&nbsp;85&nbsp;</kbd> | <kbd>&nbsp;92&nbsp;</kbd> | <kbd>&nbsp;78&nbsp;</kbd> | <kbd>&nbsp;90&nbsp;</kbd> | <kbd>&nbsp;65&nbsp;</kbd> |

<br>

**Array 2D `matrix[3][3]` (Tabel/Matriks)**

| Baris \ Kolom | Kolom `[0]` | Kolom `[1]` | Kolom `[2]` |
|:---:|:---:|:---:|:---:|
| **Baris `[0]`** | <kbd>&nbsp;1&nbsp;</kbd> | <kbd>&nbsp;2&nbsp;</kbd> | <kbd>&nbsp;3&nbsp;</kbd> |
| **Baris `[1]`** | <kbd>&nbsp;4&nbsp;</kbd> | <kbd>&nbsp;5&nbsp;</kbd> | <kbd>&nbsp;6&nbsp;</kbd> |
| **Baris `[2]`** | <kbd>&nbsp;7&nbsp;</kbd> | <kbd>&nbsp;8&nbsp;</kbd> | <kbd>&nbsp;9&nbsp;</kbd> |

</div>

---

## 🏗️ Deklarasi Array 2 Dimensi

Format umum:
```c
tipe_data nama_array[jumlah_baris][jumlah_kolom];
```

Contoh:
```c
int matrix[3][4];    // Matriks 3 baris × 4 kolom (total 12 elemen)
float tabel[5][3];   // Tabel 5 baris × 3 kolom
char papan[8][8];    // Papan catur 8×8
```

---

## 🌱 Inisialisasi Array 2 Dimensi

### Cara 1: Langsung Dengan Nilai (Grouped / Nested Braces)
```c
// Cara paling rapi & mudah dibaca (direkomendasikan):
int matrix[2][3] = {
    {1, 2, 3},   // Baris ke-0
    {4, 5, 6}    // Baris ke-1
};

// Cara alternatif (sama hasilnya, tapi kurang readable):
int matrix2[2][3] = {1, 2, 3, 4, 5, 6};

// Jumlah baris bisa diomit jika nilai langsung diisi:
int matrix3[][3] = {{1, 2, 3}, {4, 5, 6}}; // Otomatis 2 baris
```

### Cara 2: Satu per Satu menggunakan `[baris][kolom]`
```c
int matrix[2][2];
matrix[0][0] = 1;
matrix[0][1] = 2;
matrix[1][0] = 3;
matrix[1][1] = 4;
```

---

## 🔍 Mengakses Elemen Array 2D

Gunakan dua indeks: `nama_array[baris][kolom]`

```c
//                       Col:  [0]  [1]  [2]
int grid[3][3] = {
    /* Baris [0] */           {1,   2,   3},
    /* Baris [1] */           {4,   5,   6},
    /* Baris [2] */           {7,   8,   9}
};

printf("%d\n", grid[0][1]); // Output: 2  (baris 0, kolom 1)
printf("%d\n", grid[1][2]); // Output: 6  (baris 1, kolom 2)
printf("%d\n", grid[2][0]); // Output: 7  (baris 2, kolom 0)
```

---

## 🔁 Mengiterasi Array 2D: Nested For Loop

Untuk memproses seluruh elemen array 2D, kita membutuhkan **dua perulangan `for` bersarang (nested)**:
- **Loop luar** → mengiterasi baris
- **Loop dalam** → mengiterasi kolom dalam satu baris

```c
#include <stdio.h>

int main() {
    int matrix[3][3] = {
        {1, 2, 3},
        {4, 5, 6},
        {7, 8, 9}
    };
    int i, j;

    printf("Isi Matriks 3x3:\n");
    for (i = 0; i < 3; i++) {         // Loop baris
        for (j = 0; j < 3; j++) {     // Loop kolom
            printf("%3d ", matrix[i][j]);
        }
        printf("\n"); // Pindah baris setiap baris matriks selesai
    }

    return 0;
}
```

**Output:**
```text
Isi Matriks 3x3:
  1   2   3 
  4   5   6 
  7   8   9 
```

---

## 📐 Array 3 Dimensi

Array 3D bisa dibayangkan sebagai **tumpukan lapisan (*layer*) tabel 2D**. Sering digunakan untuk representasi ruang 3D, voxel, atau data video (frame × baris × kolom).

```c
int kubus[2][2][2] = {
    {               // Layer [0]
        {1, 2},     // Baris [0] dari Layer [0]
        {3, 4}      // Baris [1] dari Layer [0]
    },
    {               // Layer [1]
        {5, 6},     // Baris [0] dari Layer [1]
        {7, 8}      // Baris [1] dari Layer [1]
    }
};

printf("%d\n", kubus[0][0][0]); // Output: 1
printf("%d\n", kubus[1][0][1]); // Output: 6
printf("%d\n", kubus[0][1][1]); // Output: 4
```

---

## 💻 Studi Kasus Lengkap: Aplikasi Pembuat Matriks Interaktif

<details>
<summary>📊 <b>Klik untuk Lihat Program Matriks Interaktif Lengkap</b></summary>

```c
#include <stdio.h>

int main() {
    int baris, kolom, matrix[100][100];
    int i, j;

    printf("====================================\n");
    printf("   APLIKASI MATRIKS INTERAKTIF      \n");
    printf("====================================\n");

    printf("Jumlah baris  : ");
    scanf("%d", &baris);
    printf("Jumlah kolom  : ");
    scanf("%d", &kolom);

    if (baris > 100 || kolom > 100) {
        printf("Error: Maksimum 100 baris dan 100 kolom!\n");
        return 1;
    }

    // Input matriks
    printf("\nMasukkan nilai matriks %dx%d (baris per baris):\n", baris, kolom);
    for (i = 0; i < baris; i++) {
        printf("Baris %d: ", i + 1);
        for (j = 0; j < kolom; j++) {
            scanf("%d", &matrix[i][j]);
        }
    }

    // Tampilkan matriks
    printf("\nMatriks Anda:\n");
    printf("┌");
    for (j = 0; j < kolom; j++) printf("──────");
    printf("┐\n");

    for (i = 0; i < baris; i++) {
        printf("│");
        for (j = 0; j < kolom; j++) {
            printf(" %4d ", matrix[i][j]);
        }
        printf("│\n");
    }

    printf("└");
    for (j = 0; j < kolom; j++) printf("──────");
    printf("┘\n");

    return 0;
}
```
</details>

---

## 🥊 Latihan Mandiri

Buatlah program yang:
1. Meminta input matriks berukuran **2×3** dari pengguna.
2. Menghitung dan mencetak **total seluruh elemen** matriks tersebut.
3. *(Bonus)* Mencari elemen dengan **nilai terbesar** beserta posisi `[baris][kolom]`-nya.

---

<div align="center">

[⬅️ Sebelumnya: 5.1 Pengenalan Array](01-Pengenalan-Array.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 5.3: Array Sebagai Parameter Fungsi ➡️](03-Array-Sebagai-Parameter-Fungsi.md)

</div>
