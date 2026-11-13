# Bab 11 — Preprocessor pada Bahasa C
## Bagian 1: Pengantar Preprocessor, Symbolic Constant, dan Macro

> **Peta perjalanan Bab 11**
>
> | Bagian | Isi | Posisi |
> |---|---|---|
> | **Bagian 1** (file ini) | 11.1 Pengantar Preprocessor · 11.2 Symbolic Constant dan Macro | 📍 *kamu di sini* |
> | **Bagian 2** | 11.3 Conditional Compilation · Ringkasan Bab · Latihan | [Lanjut ke Bagian 2 »](Bab-11-Bagian-2-Conditional-Compilation.md) |
>
> **Cerita singkat bagian ini:** kita mulai dari pertanyaan paling dasar, *"preprocessor itu apa dan bekerja di mana?"*, lalu mendalami `#define` sebagai alat utamanya: dari konstanta sederhana, macro berargumen, sampai operator `#`, `##`, dan `\`. Semua bekal ini akan kita pakai sebagai "saklar" di Bagian 2.

**Daftar isi Bagian 1**

- [11.1 Pengantar Preprocessor](#111--pengantar-preprocessor)
- [11.2 Symbolic Constant dan Macro](#112--symbolic-constant-dan-macro)
- [Ringkasan Bagian 1](#ringkasan-bagian-1)
- [Latihan Bagian 1](#latihan-bagian-1)

---

# 11.1 — Pengantar Preprocessor

## Apa Itu Preprocessor?

Kalau kamu pernah menulis `#include <stdio.h>` di baris paling atas program C, berarti kamu **sudah pernah memakai preprocessor**, mungkin tanpa sadar.

**Preprocessor** adalah tahap pemrosesan yang dijalankan **sebelum** proses compile yang sesungguhnya. Tugasnya memproses teks source code terlebih dahulu: menempelkan isi file lain, mengganti simbol dengan nilai tertentu, atau membuang bagian kode yang tidak diperlukan. Hasil akhirnya tetap berupa teks kode C, hanya saja sudah "matang" dan siap diterima compiler. Karena kerjanya yang mendahului (*pre-*) inilah ia dinamakan *pre*processor.

Perintah-perintah untuk preprocessor disebut **directive**. Ciri paling gampang dikenali: **selalu diawali tanda `#`** (hash).

### Posisi preprocessor dalam alur build

```text
 source.c ──► [ Preprocessor ] ──► [ Compiler ] ──► [ Assembler ] ──► [ Linker ] ──► program
              (olah teks:          (terjemah ke       (jadi object      (gabung &
               #include, #define,   bahasa assembly)   file .o)          hasilkan
               #if, ...)                                                 executable)
```

> 💡 **Tips praktik:** kamu bisa "mengintip" hasil kerja preprocessor dengan GCC menggunakan option `-E`:
>
> ```bash
> gcc -E source.c          # tampilkan hasil preprocessing di layar
> gcc -E source.c -o out.i # simpan hasilnya ke file out.i
> ```
>
> Cara ini sangat membantu untuk debugging macro, karena kamu bisa melihat persis apa yang diterima compiler.

## Directive yang Paling Umum

Ada beberapa directive yang paling sering dijumpai. Di sini kita kenalan dulu secara garis besar; pembahasan mendalamnya ada di subbab berikutnya.

### 1. `#include` — memuat isi file lain

`#include` menyalin isi suatu file ke posisi directive tersebut berada, umumnya berupa header library.

```c
#include <stdio.h>   /* muat isi stdio.h  */
#include <stdlib.h>  /* muat isi stdlib.h */
```

### 2. `#define` — mendefinisikan pengganti simbol

`#define` membuat sebuah simbol yang nantinya **diganti** (disubstitusi) di seluruh source code oleh preprocessor.

```c
#define PI 3.14                 /* setiap PI diganti dengan 3.14 */
#define OPEN_READ_BINARY "rb"   /* setiap OPEN_READ_BINARY diganti dengan "rb" */
#define OUTPUT(x) printf(x)     /* OUTPUT(isi) diganti dengan printf(isi) */
```

Baris pertama dan kedua disebut *symbolic constant*, sedangkan baris ketiga disebut *macro* (karena menerima argumen). Keduanya kita kupas tuntas di **11.2** di bawah.

### 3. `#if`, `#elif`, `#else`, `#endif` — memilih kode berdasarkan kondisi

Konsepnya mirip `if-else` biasa, tapi **dijalankan oleh preprocessor, bukan oleh program kamu saat berjalan**. Artinya, keputusan diambil saat *compile*, dan kode yang tidak terpilih tidak ikut diterjemahkan sama sekali. Teknik ini dikenal sebagai **conditional compilation** dan dibahas lengkap di **Bagian 2**. Sebagai gambaran awal:

```c
/* Contoh ini bekerja pada compiler berbasis GCC/Clang.
   Macro bawaan seperti _WIN32, __linux__, dan __APPLE__
   bisa berbeda antar compiler dan platform. */
#if defined(_WIN32)
    printf("Anda menggunakan sistem operasi Windows\n");
#elif defined(__linux__)
    printf("Anda menggunakan sistem operasi Linux\n");
#elif defined(__APPLE__)
    printf("Anda menggunakan sistem operasi macOS\n");
#else
    printf("Anda menggunakan sistem operasi yang lain\n");
#endif
```

Contoh lain, yang sering dipakai untuk mode debugging:

```c
int Y = 0;
for (int i = 0; i < 5; i++) {
    int y = 2 * i + 1;
#if !defined(NDEBUG)   /* jika mode debug menyala saat compile */
    printf("f(%d): %d\n", i, y);
#endif
    Y = Y + y;
}
printf("Hasil: %d\n", Y);
```

Di sini `printf` di dalam `#if` hanya ikut di-compile jika `NDEBUG` **tidak** didefinisikan. Saat program dirilis (biasanya dengan `-DNDEBUG`), baris debug itu lenyap dari program final tanpa perlu kamu hapus manual.

### Aturan umum penulisan directive

| Aturan | Penjelasan |
|---|---|
| Diawali `#` | Setiap directive dimulai dengan tanda `#`; spasi sebelum/sesudah `#` boleh ada. |
| Tanpa titik koma | Directive **bukan** statement C, jadi tidak diakhiri `;`. |
| Satu baris | Satu directive berakhir di akhir baris. Untuk menyambung ke baris berikutnya, gunakan `\` (dibahas di 11.2). |
| Diproses dari atas ke bawah | Sebuah macro hanya berlaku **setelah** baris `#define`-nya. |

> **Jembatan ke subbab berikutnya:** kita sudah melihat sekilas `#define`. Sekarang saatnya membedah cara kerjanya, mulai dari bentuk paling sederhana: *symbolic constant*.

---

# 11.2 — Symbolic Constant dan Macro

`#define` adalah salah satu directive yang paling sering dipakai. Prinsip kerjanya sederhana: **mengganti sebuah *identifier* dengan *replacement* (teks pengganti)** sebelum compile berjalan. Dari prinsip ini lahir dua pemakaian utama:

| | Symbolic Constant | Macro |
|---|---|---|
| Menerima argumen? | ❌ Tidak | ✅ Ya |
| Bisa dianggap sebagai | Pengganti nama/nilai | "Fungsi mini" berbasis substitusi teks |

Kamu juga bisa memandang symbolic constant sebagai *macro tanpa argumen*.

> ⚠️ **Poin kunci:** preprocessor hanya melakukan **substitusi teks**. Ia tidak mengecek tipe data, tidak menghitung apa-apa, dan tidak "mengerti" C. Ia cuma mengganti teks. Kebanyakan jebakan macro berasal dari sini, jadi simpan baik-baik prinsip ini.

## Symbolic Constant

### Format

```c
#define identifier replacement
```

Terdiri dari tiga komponen:

1. `#define` — directive-nya,
2. `identifier` — nama symbolic constant (konvensinya huruf kapital, misal `MAX_SIZE`),
3. `replacement` — teks yang akan menggantikan identifier.

Replacement **tidak harus berupa angka**. Ia bisa berupa angka, string, nama tipe data, bahkan potongan kode.

### Contoh: mengganti tipe data dengan satu perubahan

Symbolic constant membuat source code lebih mudah diubah, karena kamu cukup mengedit satu tempat saja.

```c
#include <stdio.h>
#define DATA_TYPE int

void foo1(DATA_TYPE x);
DATA_TYPE foo2();

int main(void) {
    DATA_TYPE var;

    /* code */

    DATA_TYPE varArray[5];

    /* code */
}
```

Yang sebenarnya dilihat compiler setelah preprocessing:

```c
void foo1(int x);
int foo2();

int main(void) {
    int var;

    /* code */

    int varArray[5];

    /* code */
}
```

Seluruh `DATA_TYPE` berubah menjadi `int`, yaitu pada `foo1(int x)`, `int foo2()`, `int var`, dan `int varArray[5]`.

Bayangkan sekarang kamu ingin semua variabel tersebut bertipe `double`. Tidak perlu mengubah satu per satu, cukup ubah satu baris:

```c
#define DATA_TYPE double
```

### Kesalahan umum pada symbolic constant

```c
#define PI 3.14;        /* ❌ titik koma ikut menjadi bagian replacement */
#define PI = 3.14       /* ❌ tanda = ikut tergantikan juga */
#define PI 3.14         /* ✅ benar */
```

Akibatnya: `float luas = PI * r * r;` dengan `PI 3.14;` menjadi `float luas = 3.14; * r * r;`, yang jelas bukan yang kamu maksud.

Hal lain yang perlu diingat: jika replacement berupa **ekspresi**, bungkus dengan kurung.

```c
#define LEBAR 10 + 5
int total = LEBAR * 2;   /* menjadi 10 + 5 * 2 = 20, bukan 30 */

#define LEBAR (10 + 5)
int total = LEBAR * 2;   /* menjadi (10 + 5) * 2 = 30 ✅ */
```

> **Jembatan:** symbolic constant itu "nama pengganti" yang tetap. Bagaimana kalau penggantinya perlu berubah mengikuti input? Di sinilah **macro berargumen** berperan.

## Macro

Macro bekerja seperti symbolic constant, tetapi **menerima argumen**. Jumlah argumennya bebas, sesuai kebutuhan.

### Format

Macro dengan 1 argumen:

```c
#define identifier(argument) replacement_with_argument
```

Macro dengan 2 argumen:

```c
#define identifier(argument1, argument2) replacement_with_arguments
```

> ⚠️ **Jangan beri spasi** antara nama macro dan tanda kurung `(`. `#define SQUARE(x) ((x)*(x))` adalah macro berargumen, sedangkan `#define SQUARE (x) ((x)*(x))` dianggap symbolic constant bernama `SQUARE` dengan replacement `(x) ((x)*(x))`.

### Contoh: menghitung damage

Macro memungkinkan kita membuat "fungsi" yang mudah diubah dan tidak memerlukan pemanggilan fungsi saat program berjalan.

```c
#include <stdio.h>
#define DAMAGE(atk, def) (atk) * (100 - (def)) / 100

int main(void) {
    int defense = 20;
    printf("Damage yang dihasilkan: ");
    printf("%d\n", DAMAGE(6000, defense));
}
```

Yang dibaca compiler:

```c
int main(void) {
    int defense = 20;
    printf("Damage yang dihasilkan: ");
    printf("%d\n", (6000) * (100 - (defense)) / 100);
}
```

Perhatikan bahwa preprocessor **tidak menghitung** hasilnya. Ia hanya menempelkan teks; perhitungan `6000 * (100 - 20) / 100 = 4800` baru dilakukan saat program berjalan. Argumen boleh berupa nilai langsung (`6000`) maupun variabel (`defense`).

### Mengapa tanda kurung itu penting?

Karena substitusinya murni teks, tanpa kurung urutan operasi bisa kacau. Misalkan macro tadi ditulis tanpa kurung:

```c
#define DAMAGE(atk, def) atk * (100 - def) / 100
```

Lalu dipanggil dengan `DAMAGE(5500 + 500, 20)`. Hasil substitusinya:

```c
5500 + 500 * (100 - 20) / 100
```

Karena `*` dan `/` didahulukan, hasilnya `5500 + 400 = 5900`. Padahal yang kita inginkan `(5500 + 500) * 80 / 100 = 4800`. Itulah kenapa pada versi yang benar, **setiap argumen dibungkus kurung**, dan sebaiknya seluruh replacement juga dibungkus kurung bila berupa ekspresi.

### Macro vs fungsi biasa

| Aspek | Macro | Fungsi |
|---|---|---|
| Mekanisme | Substitusi teks sebelum compile | Dipanggil saat program berjalan |
| Pengecekan tipe | Tidak ada | Ada |
| Kecepatan | Tidak ada overhead pemanggilan | Ada sedikit overhead |
| Risiko | Argumen bisa dievaluasi berkali-kali | Argumen dievaluasi sekali |
| Debugging | Lebih sulit | Lebih mudah |

Contoh jebakan **efek samping** pada macro:

```c
#define KUADRAT(x) ((x) * (x))

int a = 3;
int b = KUADRAT(a++);   /* menjadi ((a++) * (a++)) ❌ a bertambah dua kali, hasil tidak terdefinisi */
```

Jadi hindari memberi argumen yang punya efek samping (seperti `a++`) pada macro.

> **Jembatan:** sejauh ini replacement kita berupa angka, nama tipe, atau ekspresi. Bagaimana kalau kita ingin macro bekerja dengan **teks/string**, atau mendefinisikan macro yang panjang? Untuk itu ada tiga operator khusus.

## Operator Khusus pada Macro: `#`, `##`, dan `\`

### Operator `#` (Stringify)

`#` adalah operator unary yang diletakkan **di depan argumen** pada bagian replacement. Efeknya: argumen diubah menjadi **string literal** (diapit tanda kutip ganda).

```c
#include <stdio.h>
#define PRINT_VALUE(s, v) printf(#s, v)

int main(void) {
    PRINT_VALUE(%d, 50);
}
```

Yang dibaca compiler:

```c
int main(void) {
    printf("%d", 50);
}
```

Argumen `%d` berubah menjadi `"%d"`, sehingga `printf` menerima format string yang valid.

### Operator `##` (Concatenate / Token Pasting)

`##` adalah operator binary yang diletakkan **di antara dua argumen**. Efeknya: kedua argumen **digabung menjadi satu token**.

```c
#include <stdio.h>
#define CONC(a, b) a ## b
#define TO_STRING(x) #x
#define CONC_AND_STRING(a, b) TO_STRING(a ## b)

int main(void) {
    int CONC(man, tap) = 10;
    printf( CONC_AND_STRING(Hello, World) );
    puts("");
    printf( TO_STRING( CONC(Hello, World) ) );
}
```

Yang dibaca compiler:

```c
int main(void) {
    int mantap = 10;
    printf( "HelloWorld" );
    puts("");
    printf( "CONC(Hello, World)" );
}

/* OUTPUT
HelloWorld
CONC(Hello, World)
*/
```

Mari telusuri satu per satu:

- `CONC(man, tap)` → `man ## tap` → `mantap`, sehingga menjadi nama variabel `int mantap = 10;`.
- `CONC_AND_STRING(Hello, World)` → `TO_STRING(Hello ## World)` → `TO_STRING(HelloWorld)` → `"HelloWorld"`.
- `TO_STRING( CONC(Hello, World) )` → hasilnya `"CONC(Hello, World)"`. `CONC` **tidak dieksekusi**, ia hanya diubah menjadi teks.

> 📝 **Catatan penting tentang "nesting" macro**
>
> Pada contoh terakhir, `CONC(Hello, World)` tidak diproses karena argumen yang menjadi operand `#` (atau `##`) **tidak di-expand** terlebih dahulu. Jadi `#` memperlakukannya persis seperti teks apa adanya.
>
> Di sisi lain, **bagian replacement sebuah macro boleh memanggil macro lain**, seperti pada `#define CONC_AND_STRING(a, b) TO_STRING(a ## b)`. Itulah alasan `CONC_AND_STRING` bisa bekerja.

**Trik tambahan (agar macro di-expand dulu sebelum di-stringify):** gunakan dua lapis macro.

```c
#define STR(x)  #x
#define XSTR(x) STR(x)      /* lapis perantara agar x di-expand dulu */
#define VERSI   42

printf("%s\n", STR(VERSI));   /* "VERSI" */
printf("%s\n", XSTR(VERSI));  /* "42"    */
```

### Operator `\` (Penyambung Baris)

Directive preprocessor secara default berakhir di akhir baris. Jika definisinya panjang, kamu bisa menyambungnya ke baris berikutnya dengan `\`.

```c
#define MACRO(x)  printf("Anda sedang menggunakan macro\n"); \
                  printf("Argumen macro ini ialah " #x "\n"); \
                  printf("Keluar dari macro...\n");
```

Beberapa hal yang perlu diperhatikan:

- `\` harus menjadi **karakter terakhir di baris** (jangan ada spasi setelahnya).
- Baris terakhir macro **tidak diberi** `\`.
- Pada contoh di atas, `"Argumen macro ini ialah " #x "\n"` memanfaatkan operator `#` dan penggabungan otomatis string literal yang bersebelahan di C.

> **Tips:** macro yang berisi beberapa statement sebaiknya dibungkus `do { ... } while (0)` agar aman dipakai di dalam `if` tanpa kurung kurawal.
>
> ```c
> #define LOG(x) do { \
>     printf("Log: " #x "\n"); \
>     fflush(stdout); \
> } while (0)
> ```

---

# Ringkasan Bagian 1

```text
11.1  Preprocessor = tahap olah teks SEBELUM compile; directive diawali #
          │
          ▼
11.2  #define → substitusi teks
          ├─ Symbolic constant : identifier  → replacement
          ├─ Macro             : identifier(args) → replacement
          └─ Operator khusus   : #  (jadi string)
                                 ## (gabung token)
                                 \  (sambung baris)
```

**Hal-hal yang wajib diingat:**

1. Preprocessor hanya **mengganti teks**; ia tidak memahami tipe data dan tidak menghitung nilai.
2. Directive **tidak diakhiri titik koma**.
3. **Bungkus argumen dan ekspresi macro dengan kurung** agar urutan operasi tidak salah.
4. **Jangan beri spasi** antara nama macro dan `(` pada macro berargumen.
5. Hindari argumen berefek samping (misal `i++`) pada macro.
6. Operand `#` dan `##` **tidak di-expand** lebih dulu; gunakan macro perantara jika diperlukan.
7. `gcc -E` membantu melihat hasil preprocessing.

---

# Latihan Bagian 1

**Soal 1.** Apa hasil dari kode berikut?

```c
#define LUAS(p, l) p * l
printf("%d", LUAS(2 + 3, 4));
```

<details>
<summary>Lihat jawaban</summary>

Hasil substitusi: `2 + 3 * 4` = `2 + 12` = **14** (bukan 20). Perbaikannya: `#define LUAS(p, l) ((p) * (l))`, sehingga menjadi `((2 + 3) * (4))` = **20**.

</details>

**Soal 2.** Apa isi string yang dihasilkan?

```c
#define NAMA(x) #x
printf("%s", NAMA(Halo Dunia));
```

<details>
<summary>Lihat jawaban</summary>

Operator `#` mengubah argumen menjadi string, sehingga output-nya adalah **Halo Dunia**.

</details>

---

> ## ➡️ Selanjutnya: Bagian 2 — Conditional Compilation
>
> Kamu sekarang sudah paham bahwa `#define` bisa menjadikan sebuah simbol "ada" atau "tidak ada". Di Bagian 2, kita manfaatkan hal itu sebagai **saklar** untuk memilih potongan kode mana yang ikut di-compile, lengkap dengan `#if`, `#ifdef`, `#ifndef`, `#elif`, `#else`, `#endif`, dan operator `defined`.
>
> **[Lanjut ke Bagian 2: Conditional Compilation »](Bab-11-Bagian-2-Conditional-Compilation.md)**
