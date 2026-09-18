<div align="center">

# 🌐 4.3 Aturan Scope (Ruang Lingkup Variabel)
### Bab 4: C Function · Visibilitas & Masa Hidup Variabel di Memori

[⬅️ Modul 4.2: Library Bawaan C](02-Fungsi-Library-C.md) &nbsp;•&nbsp; [Overview Bab 4](Bab4-Overview.md) &nbsp;•&nbsp; [Modul 4.4: Rekursi ➡️](04-Rekursi.md)

---

</div>

## 💡 Apa itu Scope? (Analogi KTP vs Paspor)

Pernahkah kamu mendefinisikan sebuah variabel di dalam blok `if` atau fungsi tertentu, lalu saat mencoba mencetaknya di tempat lain, program justru protes: `error: 'variabel' undeclared`?

Itulah aturan **Scope (Ruang Lingkup)**:
- **Variabel Lokal (Analogi Kartu Warga RT/RW):** Hanya berlaku dan dikenal di dalam lingkungan RT-nya sendiri. Orang dari luar wilayah tidak mengenalnya.
- **Variabel Global (Analogi Paspor Internasional):** Berlaku dan dapat diakses di mana pun di seluruh penjuru program!

Selain scope (wilayah visibilitas), ada juga konsep **Lifetime (Masa Hidup)**: kapan variabel itu lahir di memori RAM, dan kapan variabel itu dihancurkan (*didealokasi*).

---

## 🏠 1. Local Scope (Block Scope)

Semua variabel yang dideklarasikan di dalam pasangan kurung kurawal `{ ... }` (baik di dalam fungsi, loop `for`, atau percabangan `if`) bersifat **LOKAL**.

```c
#include <stdio.h>

void sebuahFungsi() {
    int rahasia = 999; // Variabel lokal milik fungsi 'sebuahFungsi'
    printf("Rahasia di dalam fungsi: %d\n", rahasia);
}

int main() {
    sebuahFungsi();

    // 🚨 KODE DI BAWAH INI AKAN MENYEBABKAN ERROR KOMPILASI!
    // printf("Rahasia di main: %d\n", rahasia); 
    // Error: 'rahasia' undeclared here!

    if (1) {
        int angkaBlok = 42;
        printf("Angka blok: %d\n", angkaBlok); // ✅ Sah!
    }
    // printf("%d\n", angkaBlok); // ❌ Error! angkaBlok sudah mati saat kurung kurawal tutup!

    return 0;
}
```

> [!NOTE]
> Variabel lokal **diciptakan di memori Stack** saat kurung kurawal buka `{` dilewati, dan **langsung dihancurkan/dihapus** saat kurung kurawal tutup `}` tercapai.

---

## 🌍 2. Global Scope

Variabel yang dideklarasikan **di luar seluruh fungsi** (biasanya di bagian paling atas file, di bawah `#include`) memiliki sifat **GLOBAL**. Variabel ini dapat dibaca dan dimodifikasi oleh fungsi manapun di file tersebut!

```c
#include <stdio.h>

// Variabel Global: Hidup selama program masih berjalan dari awal hingga akhir
int skorGlobal = 100;

void tambahSkor() {
    skorGlobal += 50; // Mengubah variabel global
    printf("[tambahSkor] Skor sekarang: %d\n", skorGlobal);
}

int main() {
    printf("[main] Skor awal: %d\n", skorGlobal); // Output: 100
    tambahSkor();                                  // Output: 150
    printf("[main] Skor akhir: %d\n", skorGlobal);// Output: 150 (Nilai ikut berubah!)
    return 0;
}
```

> [!WARNING]
> ### 🛑 Bahaya Variabel Global: Efek Samping Tak Terduga (Side Effects)
> Meskipun variabel global terlihat praktis karena bisa diakses di mana saja, **terlalu banyak variabel global adalah tanda kode yang buruk (*bad practice*)**:
> 1. Siapapun bisa mengubah nilainya, sehingga jika terjadi bug nilai salah, sangat sulit melacak fungsi mana yang mengacaukannya.
> 2. Gunakanlah **parameter fungsi** dan **return value** untuk mengirimkan data antar fungsi secara aman dan terkontrol!

---

## 👥 3. Fenomena Variable Shadowing (Penyamaran)

Apa yang terjadi jika variabel lokal memiliki nama yang **sama persis** dengan variabel global?

```c
#include <stdio.h>

int x = 10; // 1. Variabel Global

void ujiCoba() {
    int x = 99; // 2. Variabel Lokal (Namanya sama persis!)
    printf("Nilai x di dalam fungsi: %d\n", x); // Mengakses variabel lokal (99)
}

int main() {
    ujiCoba();
    printf("Nilai x di main: %d\n", x); // Tetap mengakses variabel global (10)
    return 0;
}
```

**Output:**
```text
Nilai x di dalam fungsi: 99
Nilai x di main: 10
```

> [!IMPORTANT]
> Ketika terjadi benturan nama, **variabel lokal yang paling dekat akan "menutupi" (*shadowing*) variabel global**. Compiler akan memprioritaskan variabel yang berada di dalam scope terdekat.

---

## 🥊 Tantangan & Mini Kuis

### Kuis: Lacak Nilai Output
Perhatikan urutan eksekusi kode di bawah ini:
```c
#include <stdio.h>

int nilai = 5;

void ubah() {
    nilai = 20;
}

int main() {
    int nilai = 10;
    ubah();
    printf("Nilai: %d\n", nilai);
    return 0;
}
```
Berapakah angka yang dicetak oleh `printf` di dalam fungsi `main()`? Apakah `5`, `10`, atau `20`?

<details>
<summary>🔍 <b>Klik untuk Buka Kunci Jawaban</b></summary>

**Jawaban:** **`10`**!  
**Penjelasan Logika:**
1. Di dalam `main()`, dideklarasikan variabel **lokal** bernama `nilai` yang diisi `10`.
2. Fungsi `ubah()` dipanggil. Karena di dalam `ubah()` tidak ada variabel lokal bernama `nilai`, maka yang diubah menjadi `20` adalah variabel **global**.
3. Saat kembali ke `main()`, baris `printf` mencetak variabel `nilai` milik lokal `main()` itu sendiri, yaitu tetap bernilai **`10`**!
</details>

---

<div align="center">

[⬅️ Sebelumnya: 4.2 Fungsi Library C](02-Fungsi-Library-C.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 4.4: Rekursi ➡️](04-Rekursi.md)

</div>
