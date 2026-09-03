[⬅️ Kembali ke README Bab 2](README.md)

# 2.2 Algoritma, Pseudocode, dan Source Code

Sebelum jari menyentuh keyboard untuk menulis kode, seorang programmer yang baik **berpikir dulu**. Tiga istilah berikut sering tertukar padahal punya peran berbeda dalam proses itu.

## Algoritma

**Algoritma** adalah langkah-langkah penyelesaian masalah, ditulis dengan kalimat bebas/tidak formal, urut dan logis. Ini adalah tahap paling awal — belum terikat sintaks bahasa pemrograman apapun.

**Contoh Algoritma** — menentukan bilangan genap/ganjil:
```
1. Ambil sebuah bilangan bulat dari pengguna
2. Bagi bilangan tersebut dengan 2
3. Jika sisa baginya 0, maka bilangan tersebut genap
4. Jika tidak, maka bilangan tersebut ganjil
5. Tampilkan hasilnya ke layar
```

## Pseudocode

**Pseudocode** adalah representasi algoritma dengan gaya penulisan yang menyerupai kode program, tapi tetap bebas dari aturan sintaks baku bahasa tertentu. Tujuannya mempermudah transisi dari algoritma menuju kode asli.

**Contoh Pseudocode** dari algoritma di atas:
```
BILANGAN = INPUT()
SISA = BILANGAN MOD 2
IF SISA == 0 THEN
    OUTPUT("Bilangan genap")
ELSE
    OUTPUT("Bilangan ganjil")
END IF
```

## Source Code

**Source code** adalah implementasi nyata dalam bahasa pemrograman formal (di sini: C) yang bisa dikompilasi dan dijalankan komputer.

```c
#include <stdio.h>

int main() {
    int bilangan, sisa;

    printf("Masukkan sebuah bilangan bulat: ");
    scanf("%d", &bilangan);

    sisa = bilangan % 2;   // operator modulo

    if (sisa == 0) {
        printf("%d adalah bilangan genap\n", bilangan);
    } else {
        printf("%d adalah bilangan ganjil\n", bilangan);
    }

    return 0;
}
```

## Kenapa Tahapan Ini Penting?

Melompat langsung ke source code untuk masalah yang agak kompleks biasanya berujung pada kode berantakan dan susah didebug. Membiasakan diri menulis algoritma/pseudocode dulu:

- Memaksa kita memahami masalah **sebelum** terjebak detail sintaks
- Memudahkan mendeteksi celah logika lebih awal (lebih murah diperbaiki di kertas daripada di kode)
- Menjadi dokumentasi alami yang mudah dibaca siapa saja, termasuk yang tidak bisa membaca kode C

## 🧪 Latihan Cepat

Untuk tiap kasus berikut, tulis dulu **algoritma**, lalu **pseudocode**, baru terakhir **source code** dalam C:

1. Program menentukan apakah tahun yang diinput adalah tahun kabisat (aturan: habis dibagi 4, kecuali kelipatan 100 yang tidak habis dibagi 400).
2. Program menghitung total belanja setelah diskon: jika total belanja ≥ Rp100.000 dapat diskon 10%, jika tidak, tidak ada diskon.
3. Program menentukan nilai terbesar di antara tiga bilangan yang diinput pengguna.

> 💡 Latihan ini sengaja belum membahas `if-else` secara detail — itu akan dibahas tuntas di [sub-materi 2.4 Percabangan](04-Percabangan.md). Fokus dulu pada kebiasaan berpikir algoritmis.

---
⬅️ [Sebelumnya: Pengantar C & Variabel](01-Pengantar-C-dan-Variabel.md) | ➡️ [Lanjut: Operator Assignment & Aritmatika](03-Operator-Aritmatika-dan-Assignment.md)
