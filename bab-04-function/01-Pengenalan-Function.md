<div align="center">

# ⚙️ 4.1 Pengenalan Function (Fungsi)
### Bab 4: C Function · Modul Anatomi & Blok Bangunan Modular

[⬅️ Overview Bab 4](Bab4-Overview.md) &nbsp;•&nbsp; [📋 Daftar Materi](../Daftar_Materi.md) &nbsp;•&nbsp; [Modul 4.2: Library Bawaan C ➡️](02-Fungsi-Library-C.md)

---

</div>

## 💡 Apa itu Fungsi? (Analogi Mesin Pembuat Jus)

Bayangkan sebuah **mesin pembuat jus (*blender*)**:
- Kamu memasukkan **buah dan es batu** sebagai bahan mentah (**Parameter / Argumen Masukan**).
- Mesin memproses dan menghaluskan bahan di dalamnya (**Body Function**).
- Mesin menghasilkan **segelas jus segar** untuk dinikmati (**Return Value / Nilai Kembalian**).

Jika kamu ingin membuat 10 gelas jus, kamu tidak perlu merakit mesin blender baru dari nol setiap saat. Cukup gunakan mesin yang sama, masukkan buahnya, dan tekan tombol! Itulah esensi dari **Fungsi (Function)** dalam pemrograman.

---

## 🏛️ Anatomi Struktur Sebuah Fungsi

Di dalam bahasa C, sebuah fungsi memiliki bentuk umum seperti ini:

```c
return_type nama_fungsi(tipe_param1 nama_param1, tipe_param2 nama_param2) {
    // Pernyataan / Instruksi logika di sini...
    return nilai_kembalian; // Opsional jika tipe adalah void
}
```

```mermaid
flowchart LR
    Param["📥 Parameter (Input)"] --> Body["⚙️ Badan Fungsi (Proses)"]
    Body --> Ret["📤 Return Value (Output)"]
    
    style Param fill:#e1f5fe,stroke:#0288d1,stroke-width:2px;
    style Body fill:#fff3e0,stroke:#f57c00,stroke-width:2px;
    style Ret fill:#e8f5e9,stroke:#388e3c,stroke-width:2px;
```

### Penjelasan 4 Komponen:

1. **Return Type (Tipe Kembalian):** Jenis data dari hasil akhir yang dikirimkan kembali ke pemanggil fungsi (`int`, `float`, `double`, `char`, dll). Jika fungsi hanya bertugas mencetak sesuatu ke layar tanpa menghasilkan nilai kalkulasi, gunakan tipe khusus **`void`**.
2. **Function Name (Nama Fungsi):** Nama unik yang mendeskripsikan tugas fungsi tersebut (contoh: `hitungLuas`, `cetakHeader`, `cariMaksimal`).
3. **Parameters (Parameter Masukan):** Variabel penampung nilai kiriman dari luar saat fungsi dipanggil. Parameter bersifat opsional; sebuah fungsi boleh memiliki nol parameter `()`.
4. **Body & Return:** Blok kode di dalam kurung kurawal `{ }`. Kata kunci `return` digunakan untuk mengirim hasil akhir keluar dari fungsi dan mengakhiri eksekusinya.

---

## 🔁 3 Langkah Kerja Menggunakan Fungsi

Dalam bahasa C, terdapat tiga tahapan resmi saat menggunakan fungsi:

### 1. Prototipe / Deklarasi Fungsi (Function Prototype)
Memberi tahu compiler di bagian atas file tentang nama fungsi, tipe kembalian, dan parameternya sebelum fungsi tersebut dipanggil di dalam `main()`.
```c
double hitungVolumeKubus(double sisi); // Diakhiri titik koma!
```

### 2. Pemanggilan Fungsi (Function Call)
Menjalankan fungsi di dalam `main()` atau fungsi lain dengan mengirimkan nilai argumen nyata.
```c
double volume = hitungVolumeKubus(5.0); // Memanggil fungsi dengan argumen 5.0
```

### 3. Definisi Fungsi (Function Definition)
Implementasi lengkap logika fungsi yang biasanya diletakkan di bawah `main()`.
```c
double hitungVolumeKubus(double sisi) {
    return sisi * sisi * sisi;
}
```

---

## 💻 Studi Kasus: Menghitung Volume Balok (Reusability)

Berikut adalah contoh program elegan yang menunjukkan bagaimana sebuah fungsi input dapat **digunakan kembali (reusable)** untuk meminta panjang, lebar, dan tinggi tanpa menduplikasi kode `printf` dan `scanf`:

```c
#include <stdio.h>

// 1. Deklarasi / Prototipe Fungsi
int mintaInput(char label[]);
int hitungVolumeBalok(int p, int l, int t);

int main() {
    printf("=== APLIKASI PENGHITUNG VOLUME BALOK ===\n\n");

    // 2. Memanggil fungsi input berkali-kali secara efisien!
    int panjang = mintaInput("panjang");
    int lebar   = mintaInput("lebar");
    int tinggi  = mintaInput("tinggi");

    // Menghitung volume dengan fungsi khusus
    int volume = hitungVolumeBalok(panjang, lebar, tinggi);

    printf("\n>>> Hasil Perhitungan:\n");
    printf("Volume Balok (%d x %d x %d) = %d satuan kubik.\n", panjang, lebar, tinggi, volume);

    return 0;
}

// 3. Definisi Fungsi Pembaca Input
int mintaInput(char label[]) {
    int nilai;
    printf("Masukkan nilai %s: ", label);
    scanf("%d", &nilai);
    return nilai;
}

// 4. Definisi Fungsi Perhitungan
int hitungVolumeBalok(int p, int l, int t) {
    return p * l * t;
}
```

**Output Terminal:**
```text
=== APLIKASI PENGHITUNG VOLUME BALOK ===

Masukkan nilai panjang: 10
Masukkan nilai lebar: 4
Masukkan nilai tinggi: 5

>>> Hasil Perhitungan:
Volume Balok (10 x 4 x 5) = 200 satuan kubik.
```

---

## 🎭 Variasi Bentuk-Bentuk Fungsi

### 1. Fungsi Tanpa Kembalian (`void`)
Fungsi yang hanya melakukan tindakan (seperti mencetak teks atau mengubah file) tanpa mengembalikan angka ke pemanggil:
```c
void sapaMahasiswa(char nama[]) {
    printf("Halo %s, selamat belajar di Laboratorium Komputer!\n", nama);
    // Tidak membutuhkan baris 'return nilai;'
}
```

### 2. Fungsi Tanpa Parameter
Fungsi yang tidak membutuhkan data masukan apapun dari luar:
```c
void cetakGarisPemisah() {
    printf("=========================================\n");
}
```

---

## ⚠️ Kesalahan Umum Pemula (Common Pitfalls)

> [!WARNING]
> ### 1. Lupa Menulis Prototipe Fungsi di Atas `main()`
> Jika kamu mendefinisikan fungsimu di **bawah** `main()` tanpa menuliskan prototipenya di atas, compiler C versi modern akan mengeluarkan peringatan (*warning / error*): `implicit declaration of function`. Biasakan selalu menulis deklarasi prototipe di bagian atas file!

> [!CAUTION]
> ### 2. Tipe Data Return Tidak Sesuai
> Jika fungsi dideklarasikan dengan `int hitung(...)`, pastikan nilai yang kamu `return` adalah bilangan bulat. Mengembalikan float tanpa konversi akan memotong desimal di belakang koma tanpa peringatan.

---

## 🥊 Tantangan & Mini Kuis

### Soal 1: Analisis Fungsi
Buatlah prototipe dan definisi fungsi bernama `isGenap` yang menerima satu parameter bilangan bulat `int angka`, dan mengembalikan nilai:
- `1` (True) jika angka tersebut genap.
- `0` (False) jika angka tersebut ganjil.

<details>
<summary>🔍 <b>Klik untuk Buka Kunci Solusi</b></summary>

```c
// Prototipe:
int isGenap(int angka);

// Definisi:
int isGenap(int angka) {
    if (angka % 2 == 0) {
        return 1;
    } else {
        return 0;
    }
}

// Atau versi 1 baris ultra ringkas:
int isGenap(int angka) {
    return (angka % 2 == 0);
}
```
</details>

---

<div align="center">

[⬅️ Sebelumnya: Overview Bab 4](Bab4-Overview.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 4.2: Fungsi Library Bawaan C ➡️](02-Fungsi-Library-C.md)

</div>
