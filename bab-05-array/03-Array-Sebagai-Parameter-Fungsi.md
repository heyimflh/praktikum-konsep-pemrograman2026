<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=7b1fa2&height=150&section=header&text=5.3%20Array%20sbg%20Parameter&fontSize=32&fontColor=ffffff&animation=fadeIn&desc=Menggabungkan%20Kekuatan%20Array%20&%20Function&descSize=15&descAlignY=75)

[⬅️ Modul 5.2: Array Multidimensi](02-Array-Multidimensi.md) &nbsp;•&nbsp; [Overview Bab 5](Bab5-Overview.md) &nbsp;•&nbsp; [📋 Kembali ke Daftar Materi](../Daftar_Materi.md)

---

</div>

## 💡 Mengapa Array Perlu Dikirim ke Fungsi?

Sejauh ini, seluruh logika pengolahan array (input, proses, output) kamu lakukan di dalam satu fungsi `main()` yang panjang. Ini melanggar prinsip **Modularitas** yang kita pelajari di Bab 4!

Dengan mengirimkan array ke fungsi terpisah, kamu bisa:
- Memiliki fungsi `isiArray()`, `tampilkanArray()`, `cariMaksimum()`, `hitungRataRata()` yang bersih dan reusable.
- Membuat `main()` hanya berisi panggilan fungsi yang mudah dibaca seperti membaca alur cerita.

---

## ⚔️ Dua Cara Mengirim Data ke Fungsi: *Pass by Value* vs *Pass by Reference*

Sebelum membahas array, pahami dulu perbedaan fundamental ini:

| Aspek | Pass by Value | Pass by Reference (Array) |
|---|---|---|
| **Yang dikirim** | **Salinan (copy)** nilai variabel | **Alamat memori** data aslinya |
| **Pengaruh perubahan di fungsi** | Tidak mengubah variabel asli di `main()` | **Langsung mengubah** data asli! |
| **Digunakan untuk** | `int`, `float`, `char`, dll. | `int[]`, `float[]`, array apa pun |
| **Analogi** | Memberi fotokopi dokumen | Memberi kunci loker dokumen asli |

```c
// Pass by Value: perubahan di fungsi TIDAK mempengaruhi variabel asli
void cobaUbah(int x) {
    x = 999; // Hanya mengubah 'salinan' di dalam fungsi ini
}

int main() {
    int angka = 10;
    cobaUbah(angka);
    printf("%d\n", angka); // Tetap 10! Tidak berubah.
}
```

---

## 📬 Cara 1: Mengirim Seluruh Array ke Fungsi

Deklarasikan parameter fungsi dengan `tipe_data nama_array[]` dan **wajib** sertakan parameter `int panjang` karena fungsi tidak bisa mengetahui ukuran array secara otomatis.

```c
// Prototipe:
void tampilkanArray(int arr[], int panjang);
// atau dengan pointer (keduanya setara):
void tampilkanArray(int *arr, int panjang);
```

**Saat memanggil:** cukup tulis **nama array saja** (tanpa tanda kurung siku `[]`):
```c
int data[5] = {10, 20, 30, 40, 50};
tampilkanArray(data, 5); // ✅ Benar: cukup tulis 'data'
tampilkanArray(data[], 5); // ❌ Salah!
```

### Contoh Implementasi Lengkap:

```c
#include <stdio.h>

// --- Prototipe Fungsi ---
void tampilkanArray(int arr[], int panjang);
void isiArray(int arr[], int panjang);
int cariMaksimum(int arr[], int panjang);
float hitungRataRata(int arr[], int panjang);

int main() {
    int nilai[5];

    printf("=== Sistem Pengolah Nilai Mahasiswa ===\n\n");

    // Isi array menggunakan fungsi khusus
    isiArray(nilai, 5);

    // Tampilkan semua nilai
    printf("\nData Nilai yang Dimasukkan:\n");
    tampilkanArray(nilai, 5);

    // Cetak statistik
    printf("\nNilai Tertinggi : %d\n", cariMaksimum(nilai, 5));
    printf("Rata-rata Nilai : %.2f\n", hitungRataRata(nilai, 5));

    return 0;
}

// --- Definisi Fungsi ---

void isiArray(int arr[], int panjang) {
    int i;
    for (i = 0; i < panjang; i++) {
        printf("Masukkan nilai ke-%d: ", i + 1);
        scanf("%d", &arr[i]); // Langsung mengubah array asli!
    }
}

void tampilkanArray(int arr[], int panjang) {
    int i;
    for (i = 0; i < panjang; i++) {
        printf("  [%d] = %d\n", i, arr[i]);
    }
}

int cariMaksimum(int arr[], int panjang) {
    int maks = arr[0]; // Asumsi awal: elemen pertama adalah maksimum
    int i;
    for (i = 1; i < panjang; i++) {
        if (arr[i] > maks) {
            maks = arr[i];
        }
    }
    return maks;
}

float hitungRataRata(int arr[], int panjang) {
    int total = 0;
    int i;
    for (i = 0; i < panjang; i++) {
        total += arr[i];
    }
    return (float)total / panjang;
}
```

---

## 📬 Cara 2: Mengirim Elemen Tunggal dari Array

Kamu juga bisa mengirim hanya **satu elemen** array ke fungsi, layaknya mengirim variabel biasa (*pass by value*). Gunakan notasi `arr[indeks]` sebagai argumen:

```c
void cetakNilai(int nilai) {
    printf("Nilai: %d\n", nilai);
}

int main() {
    int data[5] = {85, 92, 78, 90, 65};
    cetakNilai(data[2]); // Mengirim elemen ke-3 (nilai 78) saja
    return 0;
}
```

> [!NOTE]
> Karena ini **pass by value**, perubahan terhadap parameter `nilai` di dalam fungsi **tidak akan mempengaruhi** elemen asli di array `data`.

---

## 🛡️ Penggunaan `const` untuk Melindungi Array

Jika fungsimu hanya perlu **membaca** isi array tanpa boleh mengubahnya, tambahkan kata kunci `const` di depan parameter array. Ini seperti "kunci baca saja" yang mencegah modifikasi tidak sengaja.

```c
// Fungsi ini hanya membaca, tidak boleh mengubah array!
void tampilkanArray(const int arr[], int panjang) {
    int i;
    for (i = 0; i < panjang; i++) {
        printf("%d ", arr[i]);
        // arr[i] = 0; // ❌ Compiler akan error jika mencoba mengubah!
    }
    printf("\n");
}
```

> [!TIP]
> Jadikan kebiasaan untuk menggunakan `const` pada parameter array yang hanya ingin kamu baca. Ini melindungi data asli dari kesalahan modifikasi yang tidak disengaja, terutama dalam program besar yang melibatkan banyak fungsi.

---

## 💻 Studi Kasus: Modifikasi Array via Fungsi (Pass by Reference)

Program berikut membuktikan bahwa array yang dimodifikasi di dalam fungsi akan mengubah data aslinya di `main()`:

```c
#include <stdio.h>

void gandakanSemua(int arr[], int panjang) {
    int i;
    for (i = 0; i < panjang; i++) {
        arr[i] *= 2; // Mengubah elemen asli langsung!
    }
}

void tampilkan(const int arr[], int panjang) {
    int i;
    for (i = 0; i < panjang; i++) {
        printf("%d ", arr[i]);
    }
    printf("\n");
}

int main() {
    int data[4] = {5, 10, 15, 20};

    printf("Sebelum: ");
    tampilkan(data, 4); // Output: 5 10 15 20

    gandakanSemua(data, 4); // Modifikasi terjadi di fungsi...

    printf("Sesudah: ");
    tampilkan(data, 4); // Output: 10 20 30 40 (Array asli BERUBAH!)

    return 0;
}
```

---

## 🥊 Latihan Mandiri

Buatlah program dengan fungsi-fungsi berikut untuk mengolah array nilai 5 mahasiswa:

1. `void inputNilai(int arr[], int n)` — Meminta input nilai.
2. `int cariMinimum(int arr[], int n)` — Mengembalikan nilai terendah.
3. `void urutkanNaik(int arr[], int n)` — Mengurutkan nilai dari kecil ke besar *(Petunjuk: gunakan algoritma Bubble Sort sederhana)*.
4. `void tampilkan(const int arr[], int n)` — Menampilkan seluruh isi array.

---

<div align="center">

### 🎓 Selamat! Kamu Telah Menuntaskan Seluruh Materi Bab 5!

[⬅️ Sebelumnya: 5.2 Array Multidimensi](02-Array-Multidimensi.md) &nbsp;&nbsp;|&nbsp;&nbsp; [📋 Daftar Materi Utama](../Daftar_Materi.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke Bab 6: Pointer ➡️](../Daftar_Materi.md)

</div>
