<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=0288d1&height=150&section=header&text=5.1%20Pengenalan%20Array&fontSize=35&fontColor=ffffff&animation=fadeIn&desc=Struktur%20Data%20Pertamamu&descSize=15&descAlignY=75)

[⬅️ Overview Bab 5](Bab5-Overview.md) &nbsp;•&nbsp; [📋 Daftar Materi](../Daftar_Materi.md) &nbsp;•&nbsp; [Modul 5.2: Array Multidimensi ➡️](02-Array-Multidimensi.md)

---

</div>

## 💡 Apa itu Array? (Analogi Loker Apartemen)

Bayangkan sebuah gedung apartemen dengan lorong loker bernomor. Setiap loker (elemen) memiliki:
1. **Nomor urut / alamat loker** → Ini adalah **indeks** array.
2. **Isi di dalamnya** → Ini adalah **nilai** elemen array.

**Visualisasi `Array nilai[5]` di Memori:**

<div align="center">

| Indeks | `[0]` | `[1]` | `[2]` | `[3]` | `[4]` |
|:---:|:---:|:---:|:---:|:---:|:---:|
| **Isi / Nilai** | <kbd>&nbsp;85&nbsp;</kbd> | <kbd>&nbsp;92&nbsp;</kbd> | <kbd>&nbsp;78&nbsp;</kbd> | <kbd>&nbsp;90&nbsp;</kbd> | <kbd>&nbsp;65&nbsp;</kbd> |
| **Alamat (Ilustrasi)** | `0x100` | `0x104` | `0x108` | `0x10C` | `0x110` |

</div>

**Array** adalah kumpulan data bertipe sama yang disimpan secara berurutan di memori dan diakses menggunakan satu nama variabel + nomor indeks.

> [!IMPORTANT]
> **Aturan Emas No. 1:** Indeks array di C selalu dimulai dari **`0`**, bukan `1`!
> Untuk array berkapasitas `N`, indeks yang valid adalah `0` s.d. `N-1`.

---

## 🏗️ Deklarasi Array

Format penulisan:
```c
tipe_data nama_array[kapasitas];
```

Contoh:
```c
int nilai[5];          // Array 5 bilangan bulat
float suhu[12];        // Array 12 bilangan desimal (suhu 12 bulan)
char inisial[10];      // Array 10 karakter
```

> [!WARNING]
> Di bahasa C standar, **kapasitas array WAJIB berupa konstanta angka tetap**, bukan variabel.
> ```c
> int n = 5;
> int arr[n]; // ❌ Tidak aman! Tidak kompatibel di semua compiler (khususnya MSVC).
> int arr[5]; // ✅ Aman dan selalu berhasil di-compile.
> ```

---

## 🌱 Inisialisasi Array (Dua Cara)

### Cara 1: Langsung Saat Deklarasi
```c
int nilai[5] = {85, 92, 78, 90, 65};

// Kapasitas bisa dihilangkan (otomatis dihitung compiler):
int nilai[] = {85, 92, 78, 90, 65}; // Sama saja, otomatis berukuran 5
```

### Cara 2: Satu per Satu (Manual)
```c
int nilai[5];   // Deklarasi dulu

nilai[0] = 85;  // Elemen pertama
nilai[1] = 92;  // Elemen kedua
nilai[2] = 78;
nilai[3] = 90;
nilai[4] = 65;  // Elemen terakhir (indeks = kapasitas - 1)
```

> [!TIP]
> **Inisialisasi Nol Cepat:** Untuk mengisi seluruh elemen array dengan nilai `0`, kamu bisa menggunakan cara singkat ini:
> ```c
> int nilai[100] = {0}; // Semua 100 elemen langsung bernilai 0!
> ```

---

## 🔍 Membaca & Mengubah Nilai Elemen

```c
int nilai[5] = {85, 92, 78, 90, 65};

// Membaca nilai elemen ke-2 (indeks 1)
int nilaiKedua = nilai[1];
printf("Nilai ke-2: %d\n", nilaiKedua); // Output: 92

// Mengubah nilai elemen ke-3 (indeks 2)
nilai[2] = 100;
printf("Nilai ke-3 baru: %d\n", nilai[2]); // Output: 100
```

---

## 🔁 Array + Perulangan `for` = Pasangan Serasi

Kekuatan sesungguhnya array terlihat saat dikombinasikan dengan perulangan `for`. Bayangkan kamu harus memproses 100 data — loop yang hanya 3 baris bisa menggantikan 100 baris kode manual!

### Contoh: Input & Tampilkan Nilai Seluruh Kelas

```c
#include <stdio.h>

int main() {
    int nilai[5];
    int i;

    // 1. Input semua nilai menggunakan loop
    printf("=== Input Nilai 5 Mahasiswa ===\n");
    for (i = 0; i < 5; i++) {
        printf("Masukkan nilai mahasiswa ke-%d: ", i + 1);
        scanf("%d", &nilai[i]);
    }

    // 2. Tampilkan semua nilai menggunakan loop
    printf("\n=== Rekap Nilai ===\n");
    for (i = 0; i < 5; i++) {
        printf("Mahasiswa ke-%d : %d\n", i + 1, nilai[i]);
    }

    return 0;
}
```

---

## 📐 Manajemen Ukuran Dinamis (Pola Umum)

Dalam program nyata, jumlah data bisa bervariasi (tergantung input pengguna). Pola terbaik di C adalah: **deklarasikan array dengan kapasitas maksimum**, lalu gunakan variabel penghitung terpisah untuk melacak jumlah elemen yang benar-benar terisi.

```c
#include <stdio.h>

int main() {
    int nilai[100];     // Kapasitas maksimum: 100 mahasiswa
    int jumlahMhs = 0;  // Penghitung: berapa yang sudah terisi (awal: 0)
    int i;

    printf("Masukkan jumlah mahasiswa (maks 100): ");
    scanf("%d", &jumlahMhs);

    // Input sejumlah jumlahMhs elemen saja
    for (i = 0; i < jumlahMhs; i++) {
        printf("Nilai mahasiswa ke-%d: ", i + 1);
        scanf("%d", &nilai[i]);
    }

    // Hitung total dan rata-rata
    int total = 0;
    for (i = 0; i < jumlahMhs; i++) {
        total += nilai[i];
    }
    float rataRata = (float)total / jumlahMhs;

    printf("\nTotal Nilai   : %d\n", total);
    printf("Rata-rata     : %.2f\n", rataRata);

    return 0;
}
```

---

## 🚨 Bahaya Out-of-Bounds Access!

> [!CAUTION]
> **Jangan pernah mengakses indeks di luar batas array!**
> ```c
> int arr[5] = {1, 2, 3, 4, 5};
> printf("%d\n", arr[5]); // 🔴 UNDEFINED BEHAVIOR! Indeks valid: 0-4
> printf("%d\n", arr[10]); // 🔴 Bisa crash atau menghasilkan nilai sampah acak
> ```
> Bahasa C **tidak memiliki pengecekan otomatis** batas array seperti Python atau Java. Tanggung jawab programmer sepenuhnya untuk memastikan indeks yang diakses selalu valid!

---

## 💻 Studi Kasus Lengkap: Daftar Harga Barang

<details>
<summary>🛒 <b>Klik untuk Lihat Program Toko Sederhana</b></summary>

```c
#include <stdio.h>
#include <string.h>

int main() {
    char namaBarang[10][50]; // Maks 10 barang, nama maks 50 karakter
    int harga[10];
    int jumlahBarang = 0;
    int i;

    printf("=== APLIKASI DAFTAR HARGA TOKO ===\n");
    printf("Masukkan jumlah barang (maks 10): ");
    scanf("%d", &jumlahBarang);

    // Input data barang
    for (i = 0; i < jumlahBarang; i++) {
        printf("\nBarang ke-%d:\n", i + 1);
        printf("  Nama  : ");
        scanf("%s", namaBarang[i]);
        printf("  Harga : Rp ");
        scanf("%d", &harga[i]);
    }

    // Tampilkan daftar harga
    printf("\n==========================================\n");
    printf("  %-20s  %s\n", "NAMA BARANG", "HARGA");
    printf("==========================================\n");
    for (i = 0; i < jumlahBarang; i++) {
        printf("  %-20s  Rp %d\n", namaBarang[i], harga[i]);
    }
    printf("==========================================\n");

    return 0;
}
```
</details>

---

## 🥊 Latihan Mandiri

Buatlah program yang:
1. Meminta input **5 nilai ujian** dari pengguna.
2. Mencari dan menampilkan **nilai tertinggi** dan **nilai terendah**.
3. Menghitung dan menampilkan **rata-rata** seluruh nilai.

> Petunjuk: Gunakan satu array `int nilai[5]` dan tiga variabel: `int maks`, `int min`, `int total`.

---

<div align="center">

[⬅️ Sebelumnya: Overview Bab 5](Bab5-Overview.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 5.2: Array Multidimensi ➡️](02-Array-Multidimensi.md)

</div>
