[<< Array dan Linked List](1-ArrayLinkedList.md)

# 10.2 - Stack dan Queue

Di materi sebelumnya, kita sudah belajar membuat koleksi dengan array dan linked list. Keduanya berfungsi sebagai "wadah" yang fleksibel: kita boleh menambah atau menghapus elemen di posisi mana saja.

Pada kasus nyata, sering kali kita justru ingin **membatasi** cara data diakses. Pembatasan ini membuat program lebih mudah dipahami dan lebih sedikit bug. Dua struktur data klasik yang lahir dari ide ini adalah **stack** dan **queue**.

Keduanya sebenarnya hanyalah linked list dengan **aturan main** tertentu. Kita tidak perlu menulis struktur baru. Kita cukup memanfaatkan `insertWeapon` dan `deleteWeapon` dari materi sebelumnya dengan cara yang disiplin.

## Tujuan Pembelajaran

Setelah mempelajari materi ini, kamu diharapkan mampu:

- Menjelaskan prinsip **LIFO** (stack) dan **FIFO** (queue).
- Mengimplementasikan operasi `push`, `pop`, `top` pada stack.
- Mengimplementasikan operasi `enqueue`, `dequeue`, `head` pada queue.
- Menentukan kapan sebuah masalah cocok diselesaikan dengan stack atau queue.

## Prasyarat

Materi ini memakai ulang kode dari [Array dan Linked List](1-ArrayLinkedList.md), yaitu `struct WeaponList`, `struct WeaponNode`, `initWeaponList`, `insertWeapon`, `deleteWeapon`, dan `freeWeaponList`. Pastikan kamu sudah memahami cara kerja fungsi-fungsi tersebut.

---

## Stack

### Konsep: Tumpukan Piring

Bayangkan **tumpukan piring** di dapur. Piring baru selalu diletakkan di **paling atas**, dan saat butuh piring, kita juga mengambil dari **paling atas**. Kita tidak mungkin menarik piring di dasar tumpukan tanpa menyingkirkan semua piring di atasnya.

Prinsip ini disebut **LIFO** (*Last In, First Out*): **elemen yang masuk terakhir adalah yang keluar pertama**.

```
        push                    pop
         |                       ^
         v                       |
      +--------+             +--------+
 TOP  |  AK-47 |  <-- top    |  AK-47 |
      +--------+             +--------+
      |  M4A1  |             |  M4A1  |
      +--------+             +--------+
      | Deagle |             | Deagle |
      +--------+             +--------+
      (dasar)                (dasar)
```

Stack punya tiga operasi utama:

| Operasi | Fungsi |
|---|---|
| **push** | Menaruh elemen baru di puncak tumpukan |
| **pop** | Membuang elemen di puncak tumpukan |
| **top** | Melihat elemen di puncak tumpukan **tanpa** membuangnya |

### Memetakan Stack ke Linked List

Kita perlu memutuskan sisi mana dari linked list yang menjadi "puncak" tumpukan. Di materi ini kita tetapkan:

> **Puncak stack = elemen terakhir linked list** (`stack->last`)

Karena semua operasi stack terjadi di satu ujung yang sama, kita cukup menambah, menghapus, dan mengintip di ujung itu.

```
 first                         last
   |                             |
   v                             v
 [Deagle] <-> [M4A1] <-> [AK-47]
 (dasar)                  (TOP)
```

### Operasi Push

`push` menambahkan elemen baru di puncak, yaitu **setelah node terakhir**:

```c
void weapons_push(struct WeaponList *stack, const struct Weapon *data) {
    insertWeapon(stack, stack->last, data);
}
```

Ingat, `insertWeapon(list, after, data)` menyisipkan node setelah `after`. Dengan `after = stack->last`, node baru otomatis menjadi puncak yang baru. Kalau stack masih kosong, `last` bernilai `NULL`, dan `insertWeapon` otomatis memasang node itu sebagai elemen pertama.

### Operasi Pop

`pop` membuang elemen di puncak, yaitu **node terakhir**:

```c
void weapons_pop(struct WeaponList *stack) {
    deleteWeapon(stack, stack->last);
}
```

Jika stack kosong, `stack->last` bernilai `NULL`. Fungsi `deleteWeapon` versi kita sudah mengabaikan `NULL`, sehingga `pop` pada stack kosong aman dan tidak menyebabkan crash.

> **Perhatikan:** `pop` di sini hanya **membuang** elemen, tidak mengembalikan nilainya. Kalau kamu butuh nilainya, panggil `top` **sebelum** `pop`.

### Operasi Top

`top` hanya **mengintip** elemen puncak tanpa mengubah stack:

```c
struct WeaponNode *weapons_top(struct WeaponList *stack) {
    return stack->last;
}
```

Fungsi ini mengembalikan `NULL` jika stack kosong, jadi **selalu cek hasilnya sebelum dipakai**:

```c
struct WeaponNode *top = weapons_top(&stack);
if (top != NULL) {
    printf("Puncak stack: %s\n", top->data.name);
} else {
    printf("Stack kosong\n");
}
```

### Contoh Penerapan Stack

- **Undo/Redo** di editor teks: aksi terakhir yang dilakukan adalah aksi pertama yang dibatalkan.
- **Tombol Back** di browser: halaman terakhir yang dibuka adalah yang pertama dikunjungi ulang saat menekan Back.
- **Call stack** program: fungsi yang terakhir dipanggil adalah yang pertama selesai.
- **Pengecekan tanda kurung** `( [ { } ] )` pada kompiler atau kalkulator.

---

## Queue

### Konsep: Antrean Kasir

Bayangkan **antrean di kasir minimarket**. Orang yang datang duluan dilayani duluan, dan pendatang baru berdiri di **belakang** antrean. Tidak boleh menyerobot.

Prinsip ini disebut **FIFO** (*First In, First Out*): **elemen yang masuk pertama adalah yang keluar pertama**.

```
 keluar (dequeue)                          masuk (enqueue)
        <----                                  <----
      +--------+--------+--------+
      | Deagle |  M4A1  | AK-47  |
      +--------+--------+--------+
        ^                    ^
       head                 tail
    (depan antrean)    (belakang antrean)
```

Queue punya tiga operasi utama:

| Operasi | Fungsi |
|---|---|
| **enqueue** | Menambah elemen di **belakang** antrean |
| **dequeue** | Membuang elemen di **depan** antrean |
| **head** | Melihat elemen di depan antrean **tanpa** membuangnya |

### Memetakan Queue ke Linked List

Berbeda dengan stack, queue memakai **dua ujung** linked list:

> **Depan antrean (head) = elemen pertama** linked list (`queue->first`)
> **Belakang antrean (tail) = elemen terakhir** linked list (`queue->last`)

```
 first                          last
   |                              |
   v                              v
 [Deagle] <-> [M4A1] <-> [AK-47]
 (HEAD, keluar dari sini)  (TAIL, masuk dari sini)
```

Di sinilah pointer `last` berguna: menambah di belakang tidak perlu menelusuri seluruh list, langsung O(1).

### Operasi Enqueue

`enqueue` menambahkan elemen di belakang antrean, yaitu **setelah node terakhir**:

```c
void weapons_enqueue(struct WeaponList *queue, const struct Weapon *data) {
    insertWeapon(queue, queue->last, data);
}
```

Perhatikan bahwa kodenya **sama persis** dengan `weapons_push`. Perbedaan stack dan queue ada pada sisi mana yang dibuang, bukan pada sisi mana yang ditambah.

### Operasi Dequeue

`dequeue` membuang elemen di depan antrean, yaitu **node pertama**:

```c
void weapons_dequeue(struct WeaponList *queue) {
    deleteWeapon(queue, queue->first);
}
```

Seperti `pop`, jika antrean kosong maka `queue->first` bernilai `NULL` dan fungsi ini tidak melakukan apa-apa.

### Operasi Head

`head` mengintip elemen paling depan tanpa mengubah antrean:

```c
struct WeaponNode *weapons_head(struct WeaponList *queue) {
    return queue->first;
}
```

Sama seperti `top`, hasilnya bisa `NULL` jika antrean kosong, jadi cek dulu sebelum dipakai.

### Contoh Penerapan Queue

- **Antrean cetak** (*print queue*): dokumen yang dikirim lebih dulu dicetak lebih dulu.
- **Antrean pelanggan** pada sistem layanan, misalnya customer service atau tiket.
- **Buffer** data pada streaming dan jaringan.
- **Penjadwalan proses** pada sistem operasi.
- Penelusuran graf *Breadth-First Search* (BFS), yang akan kamu temui di materi lanjutan.

---

## Stack vs Queue

Kedua struktur data memakai linked list yang sama dan hanya berbeda aturan main:

| | Stack | Queue |
|---|---|---|
| Prinsip | **LIFO** (terakhir masuk, pertama keluar) | **FIFO** (pertama masuk, pertama keluar) |
| Analogi | Tumpukan piring | Antrean kasir |
| Tambah elemen | Di `last` | Di `last` |
| Buang elemen | Di **`last`** (sisi yang sama) | Di **`first`** (sisi berlawanan) |
| Intip elemen | `last` (`top`) | `first` (`head`) |
| Kompleksitas semua operasi | O(1) | O(1) |

Kalau ditulis ringkas:

```
Stack :  push -> last   pop -> last    top  -> last
Queue :  enqueue -> last  dequeue -> first  head -> first
```

> **Catatan:** Pemetaan di atas hanyalah salah satu pilihan. Stack juga bisa dibuat dengan puncak di `first` (tambah lewat `insertWeapon(stack, NULL, data)`, buang lewat `deleteWeapon(stack, stack->first)`). Yang penting, **konsisten**: tambah, buang, dan intip harus dilakukan di sisi yang sama.

---

## Contoh Program Lengkap

Program berikut menggabungkan kode linked list dari materi sebelumnya dengan operasi stack dan queue. Kamu bisa langsung mengompilasi dan menjalankannya.

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

/* ===== Linked list (dari materi 10.1) ===== */

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

/* ===== Stack ===== */

void weapons_push(struct WeaponList *stack, const struct Weapon *data) {
    insertWeapon(stack, stack->last, data);
}

void weapons_pop(struct WeaponList *stack) {
    deleteWeapon(stack, stack->last);
}

struct WeaponNode *weapons_top(struct WeaponList *stack) {
    return stack->last;
}

/* ===== Queue ===== */

void weapons_enqueue(struct WeaponList *queue, const struct Weapon *data) {
    insertWeapon(queue, queue->last, data);
}

void weapons_dequeue(struct WeaponList *queue) {
    deleteWeapon(queue, queue->first);
}

struct WeaponNode *weapons_head(struct WeaponList *queue) {
    return queue->first;
}

/* ===== Fungsi bantu untuk demo ===== */

struct Weapon makeWeapon(const char *name, int price, int damage, int rounds) {
    struct Weapon w;

    strcpy(w.name, name);
    w.price = price;
    w.damage = damage;
    w.rounds = rounds;
    return w;
}

/* Cetak stack dari puncak ke dasar */
void printStack(const struct WeaponList *stack) {
    const struct WeaponNode *it;

    if (stack->last == NULL) {
        printf("(stack kosong)\n\n");
        return;
    }
    for (it = stack->last; it != NULL; it = it->prev) {
        printf("%s %s ($%d)\n", (it == stack->last) ? "TOP   >" : "       ",
               it->data.name, it->data.price);
    }
    printf("\n");
}

/* Cetak queue dari depan ke belakang */
void printQueue(const struct WeaponList *queue) {
    const struct WeaponNode *it;

    if (queue->first == NULL) {
        printf("(antrean kosong)\n\n");
        return;
    }
    for (it = queue->first; it != NULL; it = it->next) {
        printf("%s ($%d)", it->data.name, it->data.price);
        if (it->next != NULL) {
            printf(" -> ");
        }
    }
    printf("\n\n");
}

int main() {
    struct WeaponList stack, queue;
    struct WeaponNode *node;
    struct Weapon w;

    initWeaponList(&stack);
    initWeaponList(&queue);

    /* ---------- Demo STACK ---------- */
    printf("=== STACK ===\n");

    w = makeWeapon("Desert Eagle", 750, 35, 7);
    weapons_push(&stack, &w);
    w = makeWeapon("M4A1", 3100, 20, 30);
    weapons_push(&stack, &w);
    w = makeWeapon("AK-47", 2500, 25, 30);
    weapons_push(&stack, &w);

    printf("Setelah push Desert Eagle, M4A1, AK-47:\n");
    printStack(&stack);

    node = weapons_top(&stack);
    if (node != NULL) {
        printf("Top saat ini: %s\n\n", node->data.name);
    }

    printf("Pop...\n");
    weapons_pop(&stack);
    printStack(&stack);

    printf("Pop 2x lagi...\n");
    weapons_pop(&stack);
    weapons_pop(&stack);
    printStack(&stack);

    printf("Pop pada stack kosong (aman, tidak terjadi apa-apa)...\n");
    weapons_pop(&stack);
    printStack(&stack);

    /* ---------- Demo QUEUE ---------- */
    printf("=== QUEUE ===\n");

    w = makeWeapon("Desert Eagle", 750, 35, 7);
    weapons_enqueue(&queue, &w);
    w = makeWeapon("M4A1", 3100, 20, 30);
    weapons_enqueue(&queue, &w);
    w = makeWeapon("AK-47", 2500, 25, 30);
    weapons_enqueue(&queue, &w);

    printf("Setelah enqueue Desert Eagle, M4A1, AK-47:\n");
    printQueue(&queue);

    node = weapons_head(&queue);
    if (node != NULL) {
        printf("Head saat ini: %s\n\n", node->data.name);
    }

    printf("Dequeue...\n");
    weapons_dequeue(&queue);
    printQueue(&queue);

    printf("Enqueue H&K MP5...\n");
    w = makeWeapon("H&K MP5", 1500, 15, 30);
    weapons_enqueue(&queue, &w);
    printQueue(&queue);

    printf("Dequeue 3x...\n");
    weapons_dequeue(&queue);
    weapons_dequeue(&queue);
    weapons_dequeue(&queue);
    printQueue(&queue);

    freeWeaponList(&stack);
    freeWeaponList(&queue);
    return 0;
}

/*
Output:

=== STACK ===
Setelah push Desert Eagle, M4A1, AK-47:
TOP   > AK-47 ($2500)
        M4A1 ($3100)
        Desert Eagle ($750)

Top saat ini: AK-47

Pop...
TOP   > M4A1 ($3100)
        Desert Eagle ($750)

Pop 2x lagi...
(stack kosong)

Pop pada stack kosong (aman, tidak terjadi apa-apa)...
(stack kosong)

=== QUEUE ===
Setelah enqueue Desert Eagle, M4A1, AK-47:
Desert Eagle ($750) -> M4A1 ($3100) -> AK-47 ($2500)

Head saat ini: Desert Eagle

Dequeue...
M4A1 ($3100) -> AK-47 ($2500)

Enqueue H&K MP5...
M4A1 ($3100) -> AK-47 ($2500) -> H&K MP5 ($1500)

Dequeue 3x...
(antrean kosong)

*/
```

Perhatikan bahwa urutan data yang dimasukkan **sama** di kedua demo (Desert Eagle, M4A1, AK-47), tetapi urutan keluarnya berbeda. Stack mengeluarkan AK-47 lebih dulu (LIFO), sedangkan queue mengeluarkan Desert Eagle lebih dulu (FIFO).

---

## Kesalahan Umum

| Kesalahan | Akibat | Cara menghindari |
|---|---|---|
| Memakai `weapons_top` / `weapons_head` tanpa cek `NULL` | Crash saat stack/antrean kosong | Selalu cek `if (node != NULL)` |
| Menganggap `pop` / `dequeue` mengembalikan nilai | Data hilang sebelum sempat dibaca | Panggil `top` / `head` dulu, baru `pop` / `dequeue` |
| Mencampur sisi: `push` di `last` tapi `pop` di `first` | Perilaku menjadi queue, bukan stack | Tetapkan sisi puncak dan jangan berganti-ganti |
| Lupa `freeWeaponList` di akhir program | *Memory leak* | Bebaskan stack dan queue sebelum `return` |
| Mengakses node langsung di tengah stack/queue | Melanggar aturan LIFO/FIFO | Hanya gunakan operasi resmi (`push`/`pop`/`top`, `enqueue`/`dequeue`/`head`) |

---

## Ringkasan

- **Stack** mengikuti prinsip **LIFO** dengan operasi `push`, `pop`, `top`, seperti tumpukan piring.
- **Queue** mengikuti prinsip **FIFO** dengan operasi `enqueue`, `dequeue`, `head`, seperti antrean kasir.
- Keduanya dibangun di atas **doubly linked list** dengan `insertWeapon` dan `deleteWeapon`. Yang membedakan hanya sisi mana yang dipakai.
- Pada implementasi kita, stack bekerja di ujung **`last`**, sedangkan queue menambah di **`last`** dan membuang di **`first`**.
- Semua operasi berjalan dalam waktu konstan, O(1), karena kita selalu memegang pointer `first` dan `last`.
- Selalu cek `NULL` pada `top` dan `head` sebelum dipakai.

---

## Latihan

1. **Mudah:** Tulis fungsi `int weapons_isEmpty(const struct WeaponList *list)` yang mengembalikan `1` jika list kosong dan `0` jika tidak.
2. **Mudah:** Jelaskan dengan kata-katamu sendiri kenapa `weapons_push` dan `weapons_enqueue` memiliki kode yang sama persis.
3. **Sedang:** Ubah implementasi stack agar puncaknya berada di **`first`**. Fungsi mana saja yang perlu diubah? Pastikan hasil demo di atas tetap sama.
4. **Sedang:** Tulis fungsi `weapons_popValue(struct WeaponList *stack, struct Weapon *out)` yang menyalin data puncak ke `out` lalu membuangnya. Fungsi mengembalikan `1` jika berhasil dan `0` jika stack kosong.
5. **Tantangan:** Gunakan stack untuk memeriksa apakah sebuah string tanda kurung seimbang, misalnya `"([]{})"` valid sedangkan `"([)]"` tidak valid. Petunjuk: buat stack berisi `char`, `push` untuk setiap kurung buka, dan `pop` untuk setiap kurung tutup yang cocok.
6. **Tantangan:** Simulasikan antrean loket dengan queue. Setiap pelanggan punya nomor antrean, dan program membaca perintah `datang` (enqueue) dan `layani` (dequeue) sampai pengguna mengetik `selesai`.

[Tree >>](3-Tree.md)
