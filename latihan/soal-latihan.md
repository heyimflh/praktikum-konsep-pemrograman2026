[⬅️ Kembali ke README Bab 2](../README.md)

# 📝 Kumpulan Latihan Praktikum — Bab 2

20 soal latihan yang menggabungkan seluruh sub-materi Bab 2 (variabel, operator, percabangan, perulangan), disusun dari yang paling dasar sampai yang menantang. Kerjakan berurutan — soal-soal di bagian akhir mengasumsikan kalian sudah lancar dengan konsep di soal-soal awal.

## 🟢 Tingkat Dasar (Soal 1-7)

**1.** Buat program yang menerima input nama dan umur, lalu menampilkan kalimat: `"Halo <nama>, umurmu adalah <umur> tahun."`

**2.** Buat program konversi suhu dari Celsius ke Reamur dan Fahrenheit sekaligus, dengan rumus:
`R = C * 4/5` dan `F = C * 9/5 + 32`

**3.** Buat program menghitung luas dan keliling persegi panjang dari panjang dan lebar yang diinput pengguna.

**4.** Buat program yang menerima sebuah angka, lalu tentukan apakah angka tersebut positif, negatif, atau nol.

**5.** Buat program penentu kelulusan sederhana: jika nilai ≥ 60 maka "Lulus", jika tidak maka "Tidak Lulus".

**6.** Buat program mencetak angka 1 sampai 20 menggunakan `for` loop, kemudian buat versi lainnya menggunakan `while` loop untuk hasil yang sama.

**7.** Buat program menghitung rata-rata dari 5 nilai ujian yang diinput satu per satu oleh pengguna.

## 🟡 Tingkat Menengah (Soal 8-14)

**8.** Buat program konversi nilai angka (0-100) menjadi nilai huruf: `A` (≥85), `B` (70-84), `C` (55-69), `D` (40-54), `E` (<40). Gunakan `if-else if`.

**9.** Buat program kalkulator sederhana menggunakan `switch-case` yang mendukung operasi `+`, `-`, `*`, `/`, dengan penanganan pembagian oleh nol (tampilkan pesan error, jangan biarkan program crash).

**10.** Buat program mencetak semua bilangan ganjil antara 1 sampai N (N diinput pengguna) beserta jumlah totalnya.

**11.** Buat program menghitung pangkat (`x^n`) tanpa menggunakan fungsi `pow()` bawaan — gunakan perulangan `for` untuk mengalikan `x` sebanyak `n` kali.

**12.** Buat program mencetak tabel perkalian 1 sampai 10 untuk sebuah angka yang diinput pengguna, format:
```
5 x 1 = 5
5 x 2 = 10
...
5 x 10 = 50
```

**13.** Buat program yang membalik urutan digit sebuah bilangan (misal input `1234`, output `4321`) menggunakan operator modulo (`%`) dan pembagian integer (`/`) di dalam loop `while`.

**14.** Buat program menentukan apakah sebuah bilangan termasuk bilangan prima atau bukan.

## 🔴 Tingkat Lanjut (Soal 15-20)

**15.** Buat program **FizzBuzz** lengkap: cetak angka 1-100, kelipatan 3 → "Fizz", kelipatan 5 → "Buzz", kelipatan 3 dan 5 → "FizzBuzz", selain itu cetak angkanya.

**16.** Buat program mencari FPB (Faktor Persekutuan Terbesar) dan KPK (Kelipatan Persekutuan Terkecil) dari dua bilangan yang diinput pengguna.

**17.** Buat program mencetak pola piramida bintang berikut untuk N baris (N diinput pengguna, contoh N=5):
```
    *
   ***
  *****
 *******
*********
```
*(Hint: butuh 2 nested loop — satu untuk spasi, satu untuk bintang.)*

**18.** Buat program menu interaktif menggunakan `do-while` + `switch-case`: konversi satuan panjang (cm ke m, m ke km, dst), dengan opsi keluar program. Menu harus terus muncul sampai user memilih keluar.

**19.** Buat program yang menghitung apakah suatu bilangan termasuk **bilangan sempurna** (*perfect number* — jumlah semua faktor pembaginya, selain dirinya sendiri, sama dengan bilangan itu sendiri. Contoh: 6 = 1+2+3).

**20.** **Studi kasus gabungan** — Buat program simulasi kasir minimarket sederhana:
- Tampilkan menu 3 barang dengan harga masing-masing (gunakan `switch-case`)
- Minta pengguna memasukkan kode barang dan jumlah beli, ulangi sampai user memilih selesai belanja (`do-while`)
- Hitung total belanja, terapkan diskon 10% jika total ≥ Rp100.000 (`if-else`)
- Tampilkan struk belanja di akhir

---

💡 **Cara memakai soal-soal ini sebagai asisten praktikum:** soal 1-7 cocok untuk sesi awal (pretest/tugas pendahuluan), soal 8-14 untuk latihan di kelas praktikum, dan soal 15-20 cocok sebagai tugas rumah atau bahan diskusi kelompok karena butuh menggabungkan beberapa konsep sekaligus.

⬅️ [Kembali ke README Bab 2](../README.md)
