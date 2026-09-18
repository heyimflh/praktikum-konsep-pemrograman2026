<div align="center">

# 🔄 3.3 Perulangan Menggunakan Do-While
### Bab 3: Program Control · Perulangan Bergaransi Minimal Satu Kali

[⬅️ Modul 3.2: Perulangan For](02-Perulangan-Menggunakan-For-Statement.md) &nbsp;•&nbsp; [Overview Bab 3](Bab3-Overview.md) &nbsp;•&nbsp; [Modul 3.4: Break & Continue ➡️](04-Break-Dan-Continue.md)

---

</div>

## 🎯 Mengapa Kita Butuh `do-while`?

Perulangan `while` dan `for` tergolong sebagai **Pre-Test Loop (Entry-Controlled)**. Artinya: pintu gerbang dijaga ketat di depan. Jika dari awal kondisi sudah tidak terpenuhi (False), **badan perulangan tidak akan pernah dieksekusi sekalipun**!

Namun, bagaimana jika kamu ingin:
- Menampilkan menu aplikasi kasir terlebih dahulu, baru menanyakan apakah kasir ingin transaksi lagi?
- Meminta input password dari user, baru memeriksa apakah password-nya benar?

Di sinilah **`do-while`** bersinar. `do-while` adalah **Post-Test Loop (Exit-Controlled)** yang memberi garansi mutlak: **instruksi di dalam blok PASTI dijalankan minimal satu kali**, baru kemudian kondisinya diuji di bagian bawah.

---

## ⚔️ Perbandingan Head-to-Head: `while` vs `do-while`

Perhatikan perbedaan mendasar alur logika berikut:

```mermaid
flowchart TD
    subgraph While Loop (Pre-Test)
        W1([Mulai]) --> W2{Kondisi Benar?}
        W2 -- YA --> W3[Jalankan Badan Loop]
        W3 --> W2
        W2 -- TIDAK --> W4([Keluar: Bisa 0 kali dieksekusi!])
    end

    subgraph Do-While Loop (Post-Test)
        D1([Mulai]) --> D2[Jalankan Badan Loop DULU!]
        D2 --> D3{Kondisi Benar?}
        D3 -- YA --> D2
        D3 -- TIDAK --> D4([Keluar: Dijamin minimal 1 kali!])
    end
```

### Bukti Nyata dalam Kode:

```c
// Kasus While:
int x = 100;
while (x < 5) {
    printf("Ini tidak akan pernah muncul!\n"); // ❌ Tidak dicetak sama sekali
}

// Kasus Do-While:
int y = 100;
do {
    printf("Pasti tercetak minimal satu kali!\n"); // ✅ Dicetak 1 kali!
} while (y < 5);
```

---

## 🏛️ Sintaks & Titik Koma Wajib

```c
do {
    // Pernyataan yang akan dieksekusi berulang
} while (/* kondisi pengujian */); // ⚠️ AWAS: Wajib diakhiri tanda titik koma!
```

> [!IMPORTANT]
> Jangan lupa meletakkan **titik koma (`;`)** di akhir pernyataan `while (kondisi);` pada konstruksi `do-while`. Ini adalah salah satu dari sedikit tempat dalam sintaks C di mana pernyataan kontrol diakhiri titik koma.

---

## 💻 Contoh Penggunaan Populer: Validasi Input Pengguna

Salah satu kasus terbaik penggunaan `do-while` adalah memaksa pengguna memasukkan data yang valid:

```c
#include <stdio.h>

int main() {
    int umur;

    // Paksa user menginput sampai nilainya masuk akal (1 - 120 tahun)
    do {
        printf("Masukkan umur Anda (1 - 120): ");
        scanf("%d", &umur);

        if (umur < 1 || umur > 120) {
            printf("⚠️ Umur tidak valid! Silakan masukkan ulang.\n\n");
        }
    } while (umur < 1 || umur > 120);

    printf("✅ Pendaftaran berhasil! Umur Anda: %d tahun.\n", umur);
    return 0;
}
```

---

## 🎮 Contoh Kasus: Menu Interaktif Berulang

```c
#include <stdio.h>

int main() {
    char pilihan;

    do {
        printf("\n=============================\n");
        printf("       MENU RESTORAN         \n");
        printf("=============================\n");
        printf("A. Nasi Goreng Spesial\n");
        printf("B. Mie Ayam Bakso\n");
        printf("X. Keluar dari Aplikasi\n");
        printf("Pilihan Anda: ");
        scanf(" %c", &pilihan); // Spasi sebelum %c untuk membersihkan karakter newline

        switch (pilihan) {
            case 'A':
            case 'a':
                printf("Pesanan diterima: Nasi Goreng Spesial siap dimasak!\n");
                break;
            case 'B':
            case 'b':
                printf("Pesanan diterima: Mie Ayam Bakso siap disajikan!\n");
                break;
            case 'X':
            case 'x':
                printf("Terima kasih telah berkunjung!\n");
                break;
            default:
                printf("Menu tidak ditemukan, silakan coba lagi.\n");
                break;
        }

    } while (pilihan != 'X' && pilihan != 'x');

    return 0;
}
```

---

## 🥊 Tantangan & Mini Kuis

### Tebak Jumlah Output:
Berapa kali kata `"Belajar C!"` akan dicetak oleh kode berikut?
```c
int counter = 5;
do {
    printf("Belajar C!\n");
    counter++;
} while (counter < 8);
```

<details>
<summary>🔍 <b>Klik untuk Buka Kunci Jawaban</b></summary>

**Jawaban:** **3 kali**.  
**Penjelasan:**
1. Putaran 1: Cetak `Belajar C!`, nilai `counter` naik jadi 6. Uji: `6 < 8` (True $\to$ Lanjut).
2. Putaran 2: Cetak `Belajar C!`, nilai `counter` naik jadi 7. Uji: `7 < 8` (True $\to$ Lanjut).
3. Putaran 3: Cetak `Belajar C!`, nilai `counter` naik jadi 8. Uji: `8 < 8` (False $\to$ Selesai).
</details>

---

<div align="center">

[⬅️ Sebelumnya: 3.2 Perulangan For](02-Perulangan-Menggunakan-For-Statement.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 3.4: Break & Continue ➡️](04-Break-Dan-Continue.md)

</div>
