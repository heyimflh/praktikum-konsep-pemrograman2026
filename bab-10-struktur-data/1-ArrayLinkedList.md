[<< Silabus](../silabus.md)

# 10.1 - Array dan Linked List

Hampir semua program perlu menyimpan **banyak data sejenis**: daftar harga barang, daftar mahasiswa, daftar senjata dalam game, dan seterusnya. Kumpulan data seperti ini disebut **koleksi**.

Di C, ada dua cara dasar untuk membuat koleksi:

1. **Array**, yang sudah sering kita pakai.
2. **Linked list**, yang akan kita pelajari di materi ini.

Keduanya sama-sama bisa menyimpan koleksi, tetapi cara kerjanya berbeda, sehingga masing-masing unggul di situasi yang berbeda. Tujuan materi ini bukan memilih mana yang "paling bagus", melainkan memahami **kapan memakai yang mana**.

## Tujuan Pembelajaran

Setelah mempelajari materi ini, kamu diharapkan mampu:

- Menjelaskan perbedaan cara array dan linked list menyimpan data di memori.
- Membuat, menambah, menghapus, dan menelusuri doubly linked list di C.
- Menghindari kesalahan umum seperti *memory leak* dan *dangling pointer*.
- Memilih struktur data yang tepat sesuai kebutuhan.

## Prasyarat

Linked list tidak bisa dipisahkan dari **pointer**, **struct**, dan **dynamic memory allocation** (`malloc` dan `free`). Kalau salah satunya masih terasa kabur, baca ulang materi tersebut dulu supaya kamu tidak kesulitan di sini.

Semua kode di materi ini membutuhkan header berikut:

```c
#include <stdio.h>
#include <stdlib.h>   /* malloc, free */
#include <string.h>   /* strcpy */
```

---

## Array

### Konsep: Deretan Loker

Bayangkan **deretan loker bernomor** yang menempel berjejer di dinding. Loker nomor 3 pasti berada di antara loker nomor 2 dan nomor 4. Kalau mau membuka loker nomor 3, kamu langsung berjalan ke sana tanpa perlu membuka loker 0, 1, dan 2 dulu.

Begitu pula array. Elemen-elemennya disimpan **berjejeran** di memori, sehingga lokasi sebuah elemen langsung menempel dengan elemen sebelum dan sesudahnya.

```
Indeks:     [0]    [1]    [2]    [3]    [4]
          +------+------+------+------+------+
 Memori:  | 2000 | 1500 | 2500 | 3100 | 1800 |
          +------+------+------+------+------+
          (alamat berurutan, tanpa celah)
```

Karena letaknya berurutan, komputer bisa **menghitung** lokasi elemen ke-`i` secara langsung (alamat awal + `i` × ukuran elemen). Inilah alasan akses lewat indeks sangat cepat.

### Definisi

```c
int price_list[100];  /* kapasitas maksimal 100 elemen */
int item_count = 0;   /* jumlah elemen yang benar-benar terpakai */
```

Ukuran array bersifat **tetap** (di sini 100). Karena itu, untuk koleksi yang jumlah isinya berubah-ubah, kita perlu variabel tambahan seperti `item_count` untuk mencatat berapa elemen yang sedang terisi.

### Mengakses Elemen

Langsung gunakan indeks:

```c
printf("M4A1 price: %d\n", price_list[3]); /* akses indeks ke-3 */
```

### Memodifikasi Elemen

Sama saja, gunakan indeks:

```c
price_list[3] = 3100; /* ubah nilai pada indeks ke-3 */
```

### Menelusuri (Iterasi) Seluruh Elemen

Gunakan `for` dengan indeks sebagai *control variable*:

```c
int i; /* control variable: indeks yang sedang dikunjungi */

for (i = 0; i < item_count; i++) {
    printf("Harga barang ke-%d : %d\n", (i + 1), price_list[i]);
}
```

### Masalah Array: Menyisipkan dan Menghapus

Misalkan kita ingin menyisipkan harga `999` di indeks 1 pada array berisi 5 elemen. Karena tidak boleh ada celah, semua elemen setelahnya harus **digeser satu per satu**:

```
Sebelum:  [2000][1500][2500][3100][1800][ ? ]

Geser elemen indeks 4, 3, 2, 1 ke kanan (dari belakang!)

Sesudah:  [2000][ 999][1500][2500][3100][1800]
```

Semakin banyak elemen, semakin banyak yang harus digeser. Itulah sebabnya array kurang nyaman untuk koleksi yang sering berubah ukuran. Penghapusan punya masalah serupa, yaitu elemen sesudahnya harus digeser maju untuk menutup lubang.

---

## Linked List

### Konsep: Permainan Cari Harta Karun

Bayangkan permainan *treasure hunt*. Kamu hanya diberi **petunjuk pertama**. Di lokasi pertama, ada secarik kertas yang berisi data sekaligus **petunjuk menuju lokasi berikutnya**. Lokasi-lokasi itu bisa tersebar di mana saja, tidak harus berdekatan.

Linked list bekerja seperti itu. Elemen-elemennya disimpan **terpisah-pisah** di memori, dan setiap elemen membawa **pointer** yang menunjuk ke elemen berikutnya (dan sebelumnya, pada *doubly* linked list).

```
Array (berjejeran):
  [A][B][C][D]

Linked list (tersebar, dihubungkan pointer):
  [A] ----> ... [B] ----> ... ... [C] ----> [D]
```

Setiap elemen dalam linked list disebut **node**.

### Singly vs Doubly Linked List

| | Singly Linked List | Doubly Linked List |
|---|---|---|
| Pointer tiap node | `next` saja | `next` dan `prev` |
| Arah penelusuran | Maju saja | Maju dan mundur |
| Memori per node | Lebih hemat | Sedikit lebih besar |
| Penghapusan node | Perlu tahu node sebelumnya | Cukup tahu node itu sendiri |

Di materi ini kita memakai **doubly linked list** karena paling serbaguna. Kita akan memakainya lagi saat membahas **stack** dan **queue** di topik berikutnya.

Bentuknya seperti ini:

```
        first                                          last
          |                                              |
          v                                              v
 NULL <- [ A ] <-> [ B ] <-> [ C ] <-> [ D ] -> NULL
         prev/next  prev/next  prev/next  prev/next
```

Perhatikan dua aturan penting:

- `prev` dari node **pertama** bernilai `NULL`.
- `next` dari node **terakhir** bernilai `NULL`.

### Definisi

Kita butuh dua struktur. Pertama, **node** yang membungkus data beserta pointer penghubungnya (disebut *self-referential structure*, yaitu struct yang memuat pointer ke struct bertipe sama). Kedua, **list** yang mencatat awal dan akhir rantai.

Sebagai contoh, kita buat koleksi `struct Weapon`:

```c
struct Weapon {
    char name[50];
    int price;
    int damage;
    int rounds;
};

/* Satu node = satu elemen dalam linked list */
struct WeaponNode {
    /* data elemen yang tersimpan dalam node */
    struct Weapon data;

    /* pointer ke node sebelumnya; NULL jika ini node pertama */
    struct WeaponNode *prev;

    /* pointer ke node berikutnya; NULL jika ini node terakhir */
    struct WeaponNode *next;
};

/* Representasi seluruh linked list */
struct WeaponList {
    /* pointer ke node pertama; NULL jika list masih kosong */
    struct WeaponNode *first;

    /* pointer ke node terakhir; NULL jika list masih kosong */
    struct WeaponNode *last;
};
```

Kenapa ada `first` **dan** `last`? Dengan `first`, kita tahu dari mana harus mulai menelusuri. Dengan `last`, menambah elemen di akhir bisa dilakukan langsung tanpa menelusuri seluruh list.

Setiap variabel list harus **diinisialisasi sebagai kosong** sebelum dipakai:

```c
void initWeaponList(struct WeaponList *list) {
    list->first = NULL;
    list->last = NULL;
}
```

```c
struct WeaponList weapons;
initWeaponList(&weapons);   /* keadaan awal: list kosong */
```

> **Awas:** Lupa menginisialisasi `first` dan `last` berarti keduanya berisi nilai sampah. Program akan mencoba mengikuti pointer ke alamat acak dan biasanya langsung crash.

### Menambahkan Elemen

Fungsi `insertWeapon` menambahkan node baru **setelah node `after`**:

- `after != NULL`: node baru disisipkan tepat setelah `after`.
- `after == NULL`: node baru dipasang di **awal** list.

Menambah di akhir berarti memakai `list.last` sebagai `after`.

```c
void insertWeapon(struct WeaponList *list, struct WeaponNode *after, const struct Weapon *data) {
    struct WeaponNode *newNode;

    /* 1. Ciptakan node baru di memori */
    newNode = (struct WeaponNode *)malloc(sizeof(struct WeaponNode));
    if (newNode == NULL) {
        return; /* memori habis, batalkan penambahan */
    }
    newNode->data = *data;

    /* 2. Tentukan pointer prev dan next milik node baru */
    if (after != NULL) {
        newNode->prev = after;
        newNode->next = after->next;
        after->next = newNode;          /* 3a. sambungkan node lama ke node baru */
    } else {
        newNode->prev = NULL;           /* node baru jadi node pertama */
        newNode->next = list->first;
        list->first = newNode;          /* 3b. geser pointer first */
    }

    /* 4. Sambungkan node SESUDAH node baru (kalau ada) */
    if (newNode->next != NULL) {
        newNode->next->prev = newNode;  /* node setelahnya menunjuk balik ke node baru */
    } else {
        list->last = newNode;           /* tidak ada node sesudahnya: node baru adalah last */
    }
}
```

Ilustrasi menyisipkan node `X` setelah `A` (`A` <-> `B` menjadi `A` <-> `X` <-> `B`):

```
Sebelum:   [A] <-> [B]

Langkah 1: X.prev = A,  X.next = B      (X tahu tetangganya)
Langkah 2: A.next = X                   (A menunjuk ke X)
Langkah 3: B.prev = X                   (B menunjuk balik ke X)

Sesudah:   [A] <-> [X] <-> [B]
```

> **Tips:** Urutan itu penting. Atur pointer milik node baru **dulu**, baru ubah pointer node lama. Kalau terbalik, kamu bisa kehilangan jejak node yang seharusnya disambungkan.

Kode di atas menangani semua kasus sekaligus:

| Kasus | Yang terjadi |
|---|---|
| List kosong, tambah elemen pertama | `first` dan `last` sama-sama menunjuk node baru |
| Tambah di awal (`after == NULL`) | `first` bergeser ke node baru |
| Tambah di akhir (`after == last`) | `last` bergeser ke node baru |
| Tambah di tengah | Hanya pointer tetangga yang diubah |

### Menghapus Elemen

Fungsi `deleteWeapon` menghapus node `node` dari list. Idenya: **lewati** node tersebut dengan menyambungkan langsung tetangga kiri dan kanannya, lalu bebaskan memorinya.

```c
void deleteWeapon(struct WeaponList *list, struct WeaponNode *node) {
    if (list == NULL || node == NULL) {
        return;
    }

    /* 1. Sambungkan node SEBELUM `node` ke node SESUDAH `node` */
    if (node->prev != NULL) {
        node->prev->next = node->next;
    } else {
        list->first = node->next;       /* `node` adalah node pertama */
    }

    /* 2. Sambungkan node SESUDAH `node` ke node SEBELUM `node` */
    if (node->next != NULL) {
        node->next->prev = node->prev;
    } else {
        list->last = node->prev;        /* `node` adalah node terakhir */
    }

    /* 3. Setelah rantai aman, baru bebaskan memori node */
    free(node);
}
```

Ilustrasi menghapus `B` (`A` <-> `B` <-> `C` menjadi `A` <-> `C`):

```
Sebelum:   [A] <-> [B] <-> [C]

Langkah 1: A.next = C           (A melompati B)
Langkah 2: C.prev = A           (C melompati B)
Langkah 3: free(B)

Sesudah:   [A] <-> [C]
```

Kasus-kasus yang otomatis tertangani:

| Kasus | Yang terjadi |
|---|---|
| Hapus node pertama | `first` maju ke node berikutnya |
| Hapus node terakhir | `last` mundur ke node sebelumnya |
| Hapus node di tengah | Tetangga kiri dan kanan saling tersambung |
| Hapus satu-satunya node | `first` dan `last` sama-sama menjadi `NULL` |

> **Penting:** `free(node)` dilakukan **terakhir**. Setelah `free`, memori itu tidak boleh diakses lagi. Pointer yang masih menunjuk ke memori yang sudah di-`free` disebut ***dangling pointer***.

### Menelusuri (Iterasi) Seluruh Node

Berbeda dengan array, pada linked list kita tidak punya indeks. Kita harus mulai dari `first`, lalu terus mengikuti pointer `next` sampai bertemu `NULL`:

```c
struct WeaponNode *iterator;

for (iterator = list.first; iterator != NULL; iterator = iterator->next) {
    /* lakukan sesuatu dengan `iterator` */
    printf("%s\n", iterator->data.name);
}
```

Untuk mengakses data di dalam node, pakai `iterator->data.price`, `iterator->data.name`, dan seterusnya.

#### Menghapus Node Sambil Menelusuri

Ini jebakan yang paling sering terjadi. Jangan menulis seperti ini:

```c
/* SALAH! */
for (iterator = list.first; iterator != NULL; iterator = iterator->next) {
    if (iterator->data.price > 3000) {
        deleteWeapon(&list, iterator);  /* iterator sudah di-free... */
    }
}                                       /* ...tapi iterator->next tetap dibaca: crash! */
```

Setelah `deleteWeapon`, node sudah di-`free`. Saat loop mengeksekusi `iterator = iterator->next`, ia membaca memori yang sudah tidak valid.

Solusinya: **simpan dulu `next`** sebelum menghapus, memakai `while`:

```c
/* BENAR: hapus semua senjata dengan harga di atas 3000 */
iterator = list.first;
while (iterator != NULL) {
    struct WeaponNode *next;

    next = iterator->next;               /* 1. catat node berikutnya dulu */
    if (iterator->data.price > 3000) {
        deleteWeapon(&list, iterator);   /* 2. baru hapus */
    }
    iterator = next;                     /* 3. lanjut dari catatan tadi */
}
```

### Membebaskan Seluruh List

Setiap node dibuat dengan `malloc`, jadi setiap node harus di-`free` sebelum program selesai. Kalau tidak, memori bocor (***memory leak***). Pola yang dipakai sama dengan penghapusan sambil menelusuri:

```c
void freeWeaponList(struct WeaponList *list) {
    struct WeaponNode *iterator = list->first;

    while (iterator != NULL) {
        struct WeaponNode *next = iterator->next; /* catat dulu */
        free(iterator);
        iterator = next;
    }

    list->first = NULL;
    list->last = NULL;
}
```

---

## Contoh Program Lengkap

Program berikut bisa langsung dikompilasi dan dijalankan:

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct Weapon {
    char name[50];
    int price;
    int damage;
    int rounds;
};

struct WeaponNode {
    struct Weapon data;
    struct WeaponNode *prev;
    struct WeaponNode *next;
};

struct WeaponList {
    struct WeaponNode *first;
    struct WeaponNode *last;
};

void initWeaponList(struct WeaponList *list) {
    list->first = NULL;
    list->last = NULL;
}

void insertWeapon(struct WeaponList *list, struct WeaponNode *after, const struct Weapon *data) {
    struct WeaponNode *newNode;

    newNode = (struct WeaponNode *)malloc(sizeof(struct WeaponNode));
    if (newNode == NULL) {
        return;
    }
    newNode->data = *data;

    if (after != NULL) {
        newNode->prev = after;
        newNode->next = after->next;
        after->next = newNode;
    } else {
        newNode->prev = NULL;
        newNode->next = list->first;
        list->first = newNode;
    }

    if (newNode->next != NULL) {
        newNode->next->prev = newNode;
    } else {
        list->last = newNode;
    }
}

void deleteWeapon(struct WeaponList *list, struct WeaponNode *node) {
    if (list == NULL || node == NULL) {
        return;
    }

    if (node->prev != NULL) {
        node->prev->next = node->next;
    } else {
        list->first = node->next;
    }

    if (node->next != NULL) {
        node->next->prev = node->prev;
    } else {
        list->last = node->prev;
    }

    free(node);
}

void freeWeaponList(struct WeaponList *list) {
    struct WeaponNode *iterator = list->first;

    while (iterator != NULL) {
        struct WeaponNode *next = iterator->next;
        free(iterator);
        iterator = next;
    }
    list->first = NULL;
    list->last = NULL;
}

void printWeapons(const struct WeaponList *list) {
    const struct WeaponNode *iterator;

    if (list->first != NULL) {
        for (iterator = list->first; iterator != NULL; iterator = iterator->next) {
            printf("Weapon: %s ($: %d, %%: %d, n: %d)\n",
                   iterator->data.name,
                   iterator->data.price,
                   iterator->data.damage,
                   iterator->data.rounds);
        }
    } else {
        printf("(list is empty)\n");
    }
    printf("\n");
}

int main() {
    struct WeaponNode *iterator;
    struct WeaponList list;
    struct Weapon wpn;

    initWeaponList(&list);

    strcpy(wpn.name, "Desert Eagle");
    wpn.price = 750;
    wpn.damage = 35;
    wpn.rounds = 7;
    insertWeapon(&list, list.last, &wpn);

    strcpy(wpn.name, "M4A1");
    wpn.price = 3100;
    wpn.damage = 20;
    wpn.rounds = 30;
    insertWeapon(&list, list.last, &wpn);

    strcpy(wpn.name, "H&K MP5");
    wpn.price = 1500;
    wpn.damage = 15;
    wpn.rounds = 30;
    insertWeapon(&list, list.last, &wpn);

    strcpy(wpn.name, "AK-47");
    wpn.price = 2500;
    wpn.damage = 25;
    wpn.rounds = 30;
    insertWeapon(&list, list.last, &wpn);

    printWeapons(&list);

    printf("Menghapus elemen pertama...\n");
    deleteWeapon(&list, list.first);
    printWeapons(&list);

    printf("Menghapus elemen terakhir...\n");
    deleteWeapon(&list, list.last);
    printWeapons(&list);

    printf("Menambahkan AWP...\n");
    strcpy(wpn.name, "AWP Magnum");
    wpn.price = 4750;
    wpn.damage = 100;
    wpn.rounds = 10;
    insertWeapon(&list, list.last, &wpn);
    printWeapons(&list);

    printf("Menghapus senjata yang harganya lebih dari $3000...\n");
    iterator = list.first;
    while (iterator != NULL) {
        struct WeaponNode *next = iterator->next;

        if (iterator->data.price > 3000) {
            deleteWeapon(&list, iterator);
        }
        iterator = next;
    }
    printWeapons(&list);

    printf("Menghapus elemen pertama...\n");
    deleteWeapon(&list, list.first);
    printWeapons(&list);

    freeWeaponList(&list); /* kebiasaan baik: selalu bebaskan sebelum program selesai */
    return 0;
}

/*
Output:

Weapon: Desert Eagle ($: 750, %: 35, n: 7)
Weapon: M4A1 ($: 3100, %: 20, n: 30)
Weapon: H&K MP5 ($: 1500, %: 15, n: 30)
Weapon: AK-47 ($: 2500, %: 25, n: 30)

Menghapus elemen pertama...
Weapon: M4A1 ($: 3100, %: 20, n: 30)
Weapon: H&K MP5 ($: 1500, %: 15, n: 30)
Weapon: AK-47 ($: 2500, %: 25, n: 30)

Menghapus elemen terakhir...
Weapon: M4A1 ($: 3100, %: 20, n: 30)
Weapon: H&K MP5 ($: 1500, %: 15, n: 30)

Menambahkan AWP...
Weapon: M4A1 ($: 3100, %: 20, n: 30)
Weapon: H&K MP5 ($: 1500, %: 15, n: 30)
Weapon: AWP Magnum ($: 4750, %: 100, n: 10)

Menghapus senjata yang harganya lebih dari $3000...
Weapon: H&K MP5 ($: 1500, %: 15, n: 30)

Menghapus elemen pertama...
(list is empty)

*/
```

---

## Perbandingan Array vs Linked List

| Aspek | Array | Linked List |
|---|---|---|
| Letak di memori | Berjejeran | Terpisah, dihubungkan pointer |
| Ukuran | Tetap (ditentukan saat deklarasi) | Fleksibel (tumbuh dan menyusut saat program berjalan) |
| Akses elemen ke-`i` | **Cepat**, langsung lewat indeks, O(1) | **Lambat**, harus menelusuri dari awal, O(n) |
| Tambah/hapus di awal | Lambat, semua elemen digeser, O(n) | **Cepat**, O(1) |
| Tambah/hapus di akhir | Cepat jika masih ada kapasitas, O(1) | **Cepat** (karena ada pointer `last`), O(1) |
| Tambah/hapus di tengah | Lambat, elemen digeser, O(n) | **Cepat** jika node-nya sudah diketahui, O(1) |
| Mencari elemen berdasarkan nilai | O(n) | O(n) |
| Memori tambahan | Tidak ada | Tiap node butuh pointer ekstra (`prev` dan `next`) |
| Kemudahan penggunaan | Sederhana | Perlu hati-hati dengan pointer dan `malloc`/`free` |

> **Catatan:** Notasi O(1) artinya waktunya tetap, berapa pun jumlah elemennya. Notasi O(n) artinya waktunya bertambah sebanding dengan jumlah elemen. Untuk linked list, "O(1) di tengah" berlaku **setelah** node-nya ditemukan. Mencari node itu sendiri tetap membutuhkan penelusuran O(n).

### Kapan Memakai yang Mana?

**Pilih array** ketika:

- Ukuran koleksi relatif tetap atau sudah diketahui sejak awal.
- Elemen sering diakses berdasarkan **nomor indeks** (misalnya "ambil barang ke-5").
- Kamu ingin kode yang sederhana dan hemat memori.

**Pilih linked list** ketika:

- Ukuran koleksi **sering berubah** (sering tambah dan hapus).
- Elemen lebih sering **ditelusuri berurutan** daripada diakses lewat indeks.
- Kamu butuh menyisipkan atau menghapus di awal, akhir, atau tengah dengan efisien.

---

## Kesalahan Umum

| Kesalahan | Akibat | Cara menghindari |
|---|---|---|
| Lupa menginisialisasi `first` dan `last` dengan `NULL` | Pointer sampah, program crash | Selalu panggil `initWeaponList` |
| Memakai `iterator->next` setelah `iterator` di-`free` | Mengakses memori tidak valid | Simpan `next` ke variabel sebelum menghapus |
| Lupa memperbarui `first` atau `last` | List "kehilangan" ujungnya | Cek kasus awal, akhir, dan list kosong |
| Lupa memperbarui salah satu dari `prev` atau `next` | Rantai putus atau tidak konsisten | Selalu sambungkan **kedua arah** |
| Tidak memanggil `free` pada semua node | *Memory leak* | Panggil `freeWeaponList` di akhir program |
| Tidak mengecek hasil `malloc` | Crash saat memori habis | Cek `newNode == NULL` |

---

## Ringkasan

- **Array** menyimpan elemen berjejeran. Akses lewat indeks cepat, tetapi menyisipkan dan menghapus mahal karena elemen harus digeser.
- **Linked list** menyimpan elemen terpisah-pisah yang dihubungkan pointer. Menyisipkan dan menghapus mudah, tetapi tidak ada akses langsung lewat indeks.
- Pada **doubly linked list**, tiap node punya `prev` dan `next`, sedangkan list mencatat `first` dan `last`.
- Saat menambah atau menghapus node, pastikan **semua pointer terkait** (`prev`, `next`, `first`, `last`) tetap konsisten.
- Saat menghapus sambil menelusuri, **simpan `next` terlebih dahulu**.
- Setiap `malloc` harus punya pasangan `free`.

---

## Latihan

1. **Mudah:** Tulis fungsi `int countWeapons(const struct WeaponList *list)` yang mengembalikan jumlah node dalam list.
2. **Mudah:** Tulis fungsi `struct WeaponNode *findWeapon(struct WeaponList *list, const char *name)` yang mengembalikan node dengan nama tertentu, atau `NULL` jika tidak ditemukan.
3. **Sedang:** Tulis fungsi `printWeaponsReverse` yang mencetak isi list dari **belakang ke depan**. Petunjuk: manfaatkan `last` dan `prev`.
4. **Sedang:** Tulis fungsi yang menghapus semua senjata dengan `rounds` kurang dari 10. Ingat jebakan menghapus sambil menelusuri.
5. **Tantangan:** Ubah program agar senjata selalu tersimpan **terurut berdasarkan harga** (termurah di awal). Petunjuk: telusuri list untuk mencari node `after` yang tepat, lalu panggil `insertWeapon`.
6. **Tantangan:** Gambar di kertas keadaan list (nilai `first`, `last`, `prev`, `next` tiap node) setelah setiap langkah pada `main()` di atas, lalu cocokkan dengan hasilnya.

[Stack dan Queue >>](2-StackQueue.md)
