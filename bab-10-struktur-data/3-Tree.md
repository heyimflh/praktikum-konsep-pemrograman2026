[<< Stack dan Queue](2-StackQueue.md)

# 10.3 - Tree

Array, linked list, stack, dan queue punya satu kesamaan: datanya berbaris **satu garis lurus**. Ada elemen pertama, kedua, ketiga, dan seterusnya.

Padahal banyak data di dunia nyata **tidak berbentuk garis lurus**, melainkan bercabang:

- **Struktur folder** di komputer: satu folder berisi beberapa subfolder, yang masing-masing berisi subfolder lagi.
- **Silsilah keluarga** dan **struktur organisasi** perusahaan.
- **Struktur HTML** sebuah halaman web.

Untuk data yang bercabang seperti ini, kita memakai **tree** (pohon). Di materi ini kita juga akan mempelajari jenis tree yang sangat berguna untuk **pencarian cepat**, yaitu **Binary Search Tree (BST)**.

## Tujuan Pembelajaran

Setelah mempelajari materi ini, kamu diharapkan mampu:

- Menjelaskan istilah-istilah tree (root, parent, child, leaf, subtree, tinggi).
- Membedakan tree umum, binary tree, dan binary search tree.
- Mengimplementasikan **insersi**, **pencarian**, **traversal**, dan **penghapusan** pada BST di C.
- Menjelaskan kenapa BST cepat, dan kapan ia bisa menjadi lambat.
- Memakai BST untuk menyimpan data berbasis **key** (misalnya NIM).

## Prasyarat

Materi ini membutuhkan pemahaman tentang **pointer**, **struct**, **dynamic memory allocation**, dan terutama **rekursi**. Struktur tree sendiri bersifat rekursif, sehingga hampir semua fungsinya ditulis secara rekursif. Kalau rekursi masih terasa asing, latih dulu dengan contoh sederhana seperti faktorial.

---

## Pengertian Tree

**Tree** adalah struktur data **non-linear** yang tersusun dari **node** (simpul). Setiap node menyimpan data dan **link (pointer)** ke node-node di bawahnya. Jika digambar, bentuknya seperti pohon yang **dibalik**: akarnya di atas, daunnya di bawah.

```
              100            <- root (akar)
           /   |   \
         30    23   46
        /  \         |
      10    6        2       <- 10, 6, dan 2 adalah leaf (daun)
```

Dari gambar itu, tree punya sifat penting: **setiap node (kecuali root) punya tepat satu parent**, dan tidak ada siklus (tidak ada jalan yang kembali ke node asal).

### Istilah-Istilah Penting

Perhatikan tree berikut sebagai acuan:

```
              50              <- level 1 (root)
            /    \
          12      62          <- level 2
         /  \    /  \
        1   20  61   63       <- level 3 (semuanya leaf)
```

| Istilah | Arti | Contoh pada gambar |
|---|---|---|
| **Root** | Node paling atas, satu-satunya yang tidak punya parent | `50` |
| **Parent** | Node yang berada langsung di atas sebuah node | Parent dari `20` adalah `12` |
| **Child** | Node yang berada langsung di bawah sebuah node | Child dari `62` adalah `61` dan `63` |
| **Sibling** | Node-node yang punya parent yang sama | `1` dan `20` bersaudara |
| **Leaf (daun)** | Node yang tidak punya child | `1`, `20`, `61`, `63` |
| **Subtree** | Tree kecil yang berakar di salah satu node | Subtree kiri `50` berakar di `12` |
| **Level** | Kedalaman node; root berada di level 1 | `61` berada di level 3 |
| **Tinggi (height)** | Jumlah level terbanyak dalam tree | Tinggi tree di atas adalah `3` |

### Jenis-Jenis Tree

**1. Tree umum**: setiap node boleh punya berapa pun child.

**2. Binary Tree**: setiap node punya **maksimal dua child**, yaitu *child kiri* (`left`) dan *child kanan* (`right`).

```
        100
        /  \
       32  12
      / \    \
     1   4    8
```

**3. Binary Search Tree (BST)**: binary tree dengan aturan urutan yang ketat. Untuk **setiap node**:

> Semua nilai di **subtree kiri** lebih **kecil** dari node tersebut, dan semua nilai di **subtree kanan** lebih **besar** dari node tersebut.

```
         50
        /  \
       12   62
      /  \  / \
     1   20 61 63
```

Cek pada root `50`: seluruh node di kiri (`12`, `1`, `20`) lebih kecil dari `50`, dan seluruh node di kanan (`62`, `61`, `63`) lebih besar. Aturan yang sama berlaku di setiap node, misalnya `12` dengan `1` di kirinya dan `20` di kanannya.

> **Awas jebakan!** Aturan BST berlaku untuk **seluruh subtree**, bukan hanya untuk child langsung. Perhatikan tree berikut:
>
> ```
>          50
>         /  \
>       12    62
>      /  \
>     1    60      <- 60 > 12 (tampak benar), tetapi 60 > 50
> ```
>
> Secara lokal `60` memang lebih besar dari parent-nya (`12`), tapi `60` berada di **sisi kiri** `50`, padahal nilainya lebih besar dari `50`. Tree ini **bukan** BST.

Pada implementasi kita, **tidak boleh ada nilai kembar** di dalam BST.

### Kenapa BST Cepat? (Analogi Tebak Angka)

Kamu pasti pernah main tebak angka 1 sampai 100. Strategi terbaiknya adalah menebak **angka tengah** lebih dulu. Kalau dijawab "terlalu besar", separuh angka langsung tersingkir.

BST bekerja dengan cara yang sama. Setiap membandingkan dengan sebuah node, kita langsung membuang **satu subtree penuh** dari pencarian. Pada tree yang rapi, banyak langkah pencarian yang dibutuhkan sebanding dengan **tinggi tree**, bukan dengan jumlah seluruh data. Sebagai gambaran, tree seimbang yang berisi sekitar satu juta node hanya setinggi sekitar 20 level.

---

## Representasi Tree di C

Setiap node menyimpan data dan dua pointer: ke child kiri dan child kanan. Pointer yang bernilai `NULL` berarti "tidak ada child di sisi itu".

```c
struct TreeNode {
    int data;                 /* data yang disimpan */
    struct TreeNode *left;    /* child kiri; NULL jika tidak ada */
    struct TreeNode *right;   /* child kanan; NULL jika tidak ada */
};
```

Mirip dengan linked list, tree juga merupakan *self-referential structure*. Bedanya, tiap node punya **dua** pointer yang mengarah ke bawah.

Untuk membuat satu node, kita pakai fungsi bantu:

```c
struct TreeNode *createNode(int value) {
    struct TreeNode *node;

    node = (struct TreeNode *)malloc(sizeof(struct TreeNode));
    if (node != NULL) {
        node->data = value;
        node->left = NULL;
        node->right = NULL;
    }
    return node;
}
```

Seluruh tree cukup diwakili oleh **pointer ke root-nya**. Tree kosong berarti pointer root bernilai `NULL`:

```c
struct TreeNode *root = NULL;   /* tree masih kosong */
```

---

## Insersi (Menambah Node) pada BST

Insersi adalah proses menambahkan node baru ke BST **tanpa merusak aturan urutan**.

### Algoritma

Mulai dari root, lalu:

1. Jika nilai baru **lebih kecil** dari node saat ini, pindah ke **kiri**.
2. Jika nilai baru **lebih besar** dari node saat ini, pindah ke **kanan**.
3. Jika nilainya **sama**, tolak (tidak boleh ada nilai kembar).
4. Jika menemukan posisi yang masih **`NULL`**, buat node baru di sana.

### Contoh Penelusuran

Masukkan angka berurutan: `20, 12, 32, 1, 21, 63`.

```
Masukkan 20 : tree kosong, 20 menjadi root.
                20

Masukkan 12 : 12 < 20, ke kiri. Kiri kosong, pasang di sana.
                20
               /
             12

Masukkan 32 : 32 > 20, ke kanan. Kanan kosong, pasang di sana.
                20
               /  \
             12    32

Masukkan 1  : 1 < 20 (kiri), 1 < 12 (kiri). Kiri kosong, pasang di sana.
Masukkan 21 : 21 > 20 (kanan), 21 < 32 (kiri). Kiri kosong, pasang di sana.
Masukkan 63 : 63 > 20 (kanan), 63 > 32 (kanan). Kanan kosong, pasang di sana.

Hasil akhir:
                20
               /  \
             12    32
            /     /  \
           1     21   63
```

Kamu bisa melihat animasi proses insersi di [visualisasi BST ini](https://www.cs.usfca.edu/~galles/visualization/BST.html).

### Kode Insersi

```c
void insertNode(struct TreeNode **tree, int value) {
    if (*tree == NULL) {
        /* posisi kosong ditemukan: pasang node baru di sini */
        *tree = createNode(value);
    } else if (value < (*tree)->data) {
        insertNode(&(*tree)->left, value);     /* lanjut ke kiri */
    } else if (value > (*tree)->data) {
        insertNode(&(*tree)->right, value);    /* lanjut ke kanan */
    } else {
        printf("Nilai %d sudah ada, tidak ditambahkan\n", value);
    }
}
```

### Kenapa Memakai `struct TreeNode **` (Pointer ke Pointer)?

Ini bagian yang paling sering membingungkan. Perhatikan: yang perlu diubah saat insersi adalah **pointer** (misalnya `root`, atau `left` milik suatu node), bukan isi node-nya. Kalau kita mengirim pointer biasa, fungsi hanya menerima **salinan** pointer, sehingga perubahan di dalam fungsi tidak terlihat dari luar.

Dengan mengirim **alamat dari pointer** (`**`), fungsi bisa mengubah pointer aslinya:

```
insertNode(&root, 20);
            |
            v
   [ root ] ----> NULL        <-- *tree adalah "slot" tempat node baru akan dipasang
```

Karena itu, saat turun ke kiri kita mengirim `&(*tree)->left`, yaitu alamat dari slot `left` milik node saat ini. Jika slot itu kosong, node baru langsung dipasang ke sana lewat `*tree = createNode(value)`.

---

## Pencarian pada BST

Pencarian memakai ide yang persis sama dengan insersi: bandingkan, lalu belok kiri atau kanan.

```c
struct TreeNode *searchNode(struct TreeNode *tree, int value) {
    if (tree == NULL) {
        return NULL;                          /* ujung jalan: tidak ditemukan */
    }
    if (value < tree->data) {
        return searchNode(tree->left, value); /* cari di kiri */
    }
    if (value > tree->data) {
        return searchNode(tree->right, value);/* cari di kanan */
    }
    return tree;                              /* nilainya sama: ketemu! */
}
```

Fungsi ini mengembalikan pointer ke node yang ditemukan, atau `NULL` jika tidak ada. **Selalu cek hasilnya** sebelum dipakai.

Dua fungsi bantu yang berguna dan akan dipakai nanti:

```c
/* node dengan nilai terkecil = terus belok kiri */
struct TreeNode *findMin(struct TreeNode *tree) {
    while (tree->left != NULL) {
        tree = tree->left;
    }
    return tree;  /* tree tidak boleh NULL saat memanggil fungsi ini */
}

/* tinggi tree = jumlah level */
int treeHeight(const struct TreeNode *tree) {
    int leftHeight, rightHeight;

    if (tree == NULL) {
        return 0;
    }
    leftHeight = treeHeight(tree->left);
    rightHeight = treeHeight(tree->right);
    return 1 + ((leftHeight > rightHeight) ? leftHeight : rightHeight);
}
```

---

## Traversal (Menjelajahi Seluruh Node)

**Traversal** adalah teknik mengunjungi seluruh node tree. Caranya: anggap setiap tree hanya terdiri dari tiga bagian, yaitu **root**, **subtree kiri**, dan **subtree kanan**. Tiga jenis traversal berbeda hanya pada **kapan root dikunjungi**:

| Traversal | Urutan | Root dikunjungi |
|---|---|---|
| **Pre-Order** | root, left, right | di **awal** |
| **In-Order** | left, root, right | di **tengah** |
| **Post-Order** | left, right, root | di **akhir** |

> **Tips menghafal:** *Pre* artinya "sebelum", jadi root dikunjungi **sebelum** anak-anaknya. *Post* artinya "sesudah", jadi root dikunjungi **sesudah** anak-anaknya. *In* artinya "di antara", jadi root berada **di antara** kiri dan kanan. Posisi *left* selalu mendahului *right*.

### Kode Traversal

```c
void preOrder(const struct TreeNode *tree) {
    if (tree != NULL) {
        printf("%d ", tree->data);   /* root  */
        preOrder(tree->left);        /* left  */
        preOrder(tree->right);       /* right */
    }
}

void inOrder(const struct TreeNode *tree) {
    if (tree != NULL) {
        inOrder(tree->left);         /* left  */
        printf("%d ", tree->data);   /* root  */
        inOrder(tree->right);        /* right */
    }
}

void postOrder(const struct TreeNode *tree) {
    if (tree != NULL) {
        postOrder(tree->left);       /* left  */
        postOrder(tree->right);      /* right */
        printf("%d ", tree->data);   /* root  */
    }
}
```

Ketiganya **hampir identik**. Satu-satunya perbedaan adalah posisi baris `printf` relatif terhadap dua panggilan rekursif.

### Contoh Penelusuran

```
         50
        /  \
       12   62
      /  \  / \
     1   20 61 63
```

Hasil traversal:

```
Pre-Order  : 50, 12, 1, 20, 62, 61, 63
In-Order   : 1, 12, 20, 50, 61, 62, 63
Post-Order : 1, 20, 12, 61, 63, 62, 50
```

Untuk memahami *In-Order*, telusuri langkahnya: dari `50` kita turun ke kiri ke `12`, lalu ke kiri lagi ke `1`. `1` tidak punya child, jadi cetak `1`. Kembali ke `12`, cetak `12`, lalu pindah ke kanan ke `20` dan cetak `20`. Subtree kiri `50` selesai, sekarang cetak `50`, dan seterusnya untuk sisi kanan.

> **Fakta penting:** Hasil **In-Order pada BST selalu terurut menaik**. Lihat hasil di atas: `1, 12, 20, 50, 61, 62, 63`. Inilah alasan BST bisa dipakai untuk menyimpan data yang harus tetap terurut.

### Kegunaan Masing-Masing Traversal

| Traversal | Kegunaan umum |
|---|---|
| **In-Order** | Menampilkan data BST secara terurut |
| **Pre-Order** | Menyalin tree, atau menyimpan struktur tree (root dicatat lebih dulu) |
| **Post-Order** | Menghapus seluruh tree (anak dibebaskan dulu, baru root) |

Ada satu lagi jenis traversal, yaitu **Level-Order** (per level, dari atas ke bawah). Teknik ini memakai **queue** dari materi sebelumnya, dan akan kamu temui di topik lanjutan.

---

## Penghapusan Node pada BST

Penghapusan adalah operasi yang paling rumit pada BST, karena setelah sebuah node dibuang, **aturan urutan harus tetap terjaga**. Ada tiga kasus, tergantung banyaknya child dari node yang dihapus.

### Kasus 1: Node adalah Leaf (tanpa child)

Paling mudah: cukup lepaskan dan bebaskan node-nya.

```
Hapus 1:

     12            12
    /  \    -->      \
   1    20            20
```

### Kasus 2: Node Punya Satu Child

Node digantikan oleh **satu-satunya child-nya**. Anggap child itu "naik" menggantikan posisi parent-nya.

```
Hapus 12 (hanya punya anak kanan):

     50                50
    /  \              /  \
   12   62    -->   20    62
     \
      20
```

### Kasus 3: Node Punya Dua Child

Ini kasus tersulit. Kita tidak bisa sekadar mengangkat salah satu child, karena child yang satunya akan kehilangan tempat. Solusinya memakai **successor**:

> **Successor** adalah node dengan nilai **terkecil di subtree kanan**, yaitu node yang nilainya tepat satu langkah lebih besar dari node yang dihapus.

Langkahnya:

1. Cari successor (`findMin` pada subtree kanan).
2. **Salin** data successor ke node yang akan dihapus.
3. Hapus successor dari subtree kanan. Karena successor adalah node paling kiri, ia pasti **tidak punya child kiri**, sehingga penghapusannya jatuh ke Kasus 1 atau 2.

```
Hapus 62 (punya dua anak: 61 dan 63):

        50                      50
       /  \                    /  \
     20    62        -->     20    63
          /  \                    /
        61    63                 61

Successor dari 62 = nilai terkecil di subtree kanan = 63.
Data 62 diganti 63, lalu node 63 yang lama dihapus.
```

Aturan urutan BST tetap terjaga, karena successor lebih besar dari semua node di kiri dan lebih kecil atau sama dengan semua node di kanan.

### Kode Penghapusan

```c
void deleteNode(struct TreeNode **tree, int value) {
    struct TreeNode *cur;

    if (*tree == NULL) {
        return;                               /* nilai tidak ditemukan */
    }

    cur = *tree;
    if (value < cur->data) {
        deleteNode(&cur->left, value);        /* cari di kiri */
    } else if (value > cur->data) {
        deleteNode(&cur->right, value);       /* cari di kanan */
    } else {
        /* node yang dicari ditemukan: `cur` */
        if (cur->left == NULL) {
            /* Kasus 1 (leaf) dan Kasus 2 (hanya anak kanan) */
            *tree = cur->right;               /* NULL jika leaf */
            free(cur);
        } else if (cur->right == NULL) {
            /* Kasus 2 (hanya anak kiri) */
            *tree = cur->left;
            free(cur);
        } else {
            /* Kasus 3: dua anak */
            struct TreeNode *successor = findMin(cur->right);

            cur->data = successor->data;              /* salin data successor */
            deleteNode(&cur->right, successor->data); /* hapus successor asli */
        }
    }
}
```

Kuncinya ada pada `*tree = ...`. Karena kita memakai pointer ke pointer, `*tree` adalah **slot** milik parent (atau `root` itu sendiri) yang menunjuk ke node yang dihapus. Dengan menimpa slot ini, kita otomatis menyambungkan parent ke pengganti yang tepat, **tanpa perlu menyimpan pointer parent**, dan tanpa kasus khusus untuk root.

---

## Membebaskan Seluruh Tree

Setiap node dibuat dengan `malloc`, jadi harus di-`free`. Di sini **Post-Order** adalah pilihan yang tepat: kita harus membebaskan kedua anak **sebelum** membebaskan node induknya. Kalau induknya dibebaskan lebih dulu, kita kehilangan pointer ke anak-anaknya.

```c
void freeTree(struct TreeNode **tree) {
    if (*tree != NULL) {
        freeTree(&(*tree)->left);    /* left  */
        freeTree(&(*tree)->right);   /* right */
        free(*tree);                 /* root  */
        *tree = NULL;
    }
}
```

---

## Contoh Program Lengkap (BST Bilangan Bulat)

Program berikut menggabungkan semua operasi di atas dan bisa langsung dikompilasi.

```c
#include <stdio.h>
#include <stdlib.h>

struct TreeNode {
    int data;
    struct TreeNode *left;
    struct TreeNode *right;
};

struct TreeNode *createNode(int value) {
    struct TreeNode *node;

    node = (struct TreeNode *)malloc(sizeof(struct TreeNode));
    if (node != NULL) {
        node->data = value;
        node->left = NULL;
        node->right = NULL;
    }
    return node;
}

void insertNode(struct TreeNode **tree, int value) {
    if (*tree == NULL) {
        *tree = createNode(value);
    } else if (value < (*tree)->data) {
        insertNode(&(*tree)->left, value);
    } else if (value > (*tree)->data) {
        insertNode(&(*tree)->right, value);
    } else {
        printf("Nilai %d sudah ada, tidak ditambahkan\n", value);
    }
}

struct TreeNode *searchNode(struct TreeNode *tree, int value) {
    if (tree == NULL) {
        return NULL;
    }
    if (value < tree->data) {
        return searchNode(tree->left, value);
    }
    if (value > tree->data) {
        return searchNode(tree->right, value);
    }
    return tree;
}

struct TreeNode *findMin(struct TreeNode *tree) {
    while (tree->left != NULL) {
        tree = tree->left;
    }
    return tree;
}

int treeHeight(const struct TreeNode *tree) {
    int leftHeight, rightHeight;

    if (tree == NULL) {
        return 0;
    }
    leftHeight = treeHeight(tree->left);
    rightHeight = treeHeight(tree->right);
    return 1 + ((leftHeight > rightHeight) ? leftHeight : rightHeight);
}

void preOrder(const struct TreeNode *tree) {
    if (tree != NULL) {
        printf("%d ", tree->data);
        preOrder(tree->left);
        preOrder(tree->right);
    }
}

void inOrder(const struct TreeNode *tree) {
    if (tree != NULL) {
        inOrder(tree->left);
        printf("%d ", tree->data);
        inOrder(tree->right);
    }
}

void postOrder(const struct TreeNode *tree) {
    if (tree != NULL) {
        postOrder(tree->left);
        postOrder(tree->right);
        printf("%d ", tree->data);
    }
}

void deleteNode(struct TreeNode **tree, int value) {
    struct TreeNode *cur;

    if (*tree == NULL) {
        return;
    }

    cur = *tree;
    if (value < cur->data) {
        deleteNode(&cur->left, value);
    } else if (value > cur->data) {
        deleteNode(&cur->right, value);
    } else {
        if (cur->left == NULL) {
            *tree = cur->right;
            free(cur);
        } else if (cur->right == NULL) {
            *tree = cur->left;
            free(cur);
        } else {
            struct TreeNode *successor = findMin(cur->right);

            cur->data = successor->data;
            deleteNode(&cur->right, successor->data);
        }
    }
}

void freeTree(struct TreeNode **tree) {
    if (*tree != NULL) {
        freeTree(&(*tree)->left);
        freeTree(&(*tree)->right);
        free(*tree);
        *tree = NULL;
    }
}

/* fungsi bantu untuk demo: tampilkan tree dalam dua traversal */
void showTree(const struct TreeNode *tree) {
    printf("  Inorder  : ");
    inOrder(tree);
    printf("\n  Preorder : ");
    preOrder(tree);
    printf("\n\n");
}

int main() {
    struct TreeNode *root = NULL;
    struct TreeNode *skewed = NULL;
    struct TreeNode *found;
    int values[] = {50, 12, 62, 1, 20, 61, 63};
    int count = sizeof(values) / sizeof(values[0]);
    int i;

    for (i = 0; i < count; i++) {
        insertNode(&root, values[i]);
    }

    printf("Preorder  : ");
    preOrder(root);
    printf("\nInorder   : ");
    inOrder(root);
    printf("\nPostorder : ");
    postOrder(root);
    printf("\nTinggi tree: %d\n\n", treeHeight(root));

    found = searchNode(root, 20);
    printf("Cari 20: %s\n", (found != NULL) ? "ketemu" : "tidak ketemu");
    found = searchNode(root, 99);
    printf("Cari 99: %s\n\n", (found != NULL) ? "ketemu" : "tidak ketemu");

    printf("Menghapus 1 (leaf)...\n");
    deleteNode(&root, 1);
    showTree(root);

    printf("Menghapus 12 (satu anak)...\n");
    deleteNode(&root, 12);
    showTree(root);

    printf("Menghapus 62 (dua anak)...\n");
    deleteNode(&root, 62);
    showTree(root);

    printf("Menghapus 50 (root, dua anak)...\n");
    deleteNode(&root, 50);
    showTree(root);

    /* BST bisa "miring" jika data dimasukkan dalam keadaan sudah terurut */
    for (i = 1; i <= 5; i++) {
        insertNode(&skewed, i);
    }
    printf("Tinggi tree setelah insert 1,2,3,4,5 berurutan: %d\n", treeHeight(skewed));

    freeTree(&root);
    freeTree(&skewed);
    return 0;
}

/*
Output:

Preorder  : 50 12 1 20 62 61 63
Inorder   : 1 12 20 50 61 62 63
Postorder : 1 20 12 61 63 62 50
Tinggi tree: 3

Cari 20: ketemu
Cari 99: tidak ketemu

Menghapus 1 (leaf)...
  Inorder  : 12 20 50 61 62 63
  Preorder : 50 12 20 62 61 63

Menghapus 12 (satu anak)...
  Inorder  : 20 50 61 62 63
  Preorder : 50 20 62 61 63

Menghapus 62 (dua anak)...
  Inorder  : 20 50 61 63
  Preorder : 50 20 63 61

Menghapus 50 (root, dua anak)...
  Inorder  : 20 61 63
  Preorder : 61 20 63

Tinggi tree setelah insert 1,2,3,4,5 berurutan: 5

*/
```

Perhatikan bahwa urutan **In-Order selalu tetap terurut** setelah penghapusan apa pun. Itu tanda bahwa aturan BST tetap terjaga.

---

## Efisiensi BST dan Masalah "Tree Miring"

Kecepatan operasi BST bergantung pada **tinggi tree** (h):

| Operasi | Tree seimbang | Tree miring (kasus terburuk) |
|---|---|---|
| Insersi | O(log n) | O(n) |
| Pencarian | O(log n) | O(n) |
| Penghapusan | O(log n) | O(n) |
| Traversal (semua node) | O(n) | O(n) |

Kasus terburuk terjadi ketika data dimasukkan dalam keadaan **sudah terurut**:

```
Masukkan 1, 2, 3, 4, 5 berurutan:

 1
  \
   2
    \
     3
      \
       4
        \
         5
```

Hasilnya adalah tree yang tingginya sama dengan jumlah node, atau dengan kata lain **berubah menjadi linked list**. Pencarian pun kembali lambat, O(n). Demo di atas membuktikannya: tinggi tree dari insert `1,2,3,4,5` adalah `5`.

Untuk mengatasinya, ada varian BST yang **menyeimbangkan dirinya sendiri** secara otomatis, seperti **AVL Tree** dan **Red-Black Tree**. Keduanya tidak kita bahas di sini, tapi ide dasarnya tetap BST yang sudah kamu pelajari.

---

## Contoh Penerapan BST: Penyimpanan Berbasis Key

Pernahkah kamu bertanya-tanya, bagaimana cara mengakses data lewat indeks yang bukan angka, melainkan string? Misalnya:

```c
/* mahasiswa teridentifikasi bukan oleh indeks angka, melainkan NIM */
printf("Nama mahasiswa: %s\n", mhsList["M0520047"].nama); /* mampukah C melakukan ini? */
```

Bahasa C **tidak** mendukung hal ini secara bawaan. Di bahasa lain, konsep ini dikenal sebagai **dictionary**, **map**, atau **associative array**. Data disimpan sebagai pasangan **key** (kunci pengenal, misalnya NIM) dan **value** (data lengkapnya).

BST sangat cocok untuk keperluan ini: **key menjadi dasar pembanding** di setiap node, dan **value** ikut tersimpan di node yang sama.

> **Catatan agar tidak keliru:** Tidak semua map di bahasa lain berbasis BST. `dict` Python, `HashMap` Java, dan *associative array* PHP umumnya memakai **hash table**. Sementara itu `std::map` di C++ dan `TreeMap` di Java umumnya memakai BST yang menyeimbangkan dirinya (Red-Black Tree). Keunggulan versi BST adalah datanya **selalu terurut berdasarkan key**.

### Perbedaan dengan BST Bilangan Bulat

Hanya ada dua perubahan:

1. Node menyimpan **`key`** (string NIM) dan **`value`** (data mahasiswa).
2. Pembandingan memakai **`strcmp(a, b)`** yang mengembalikan:
   - nilai **negatif** jika `a` urutannya lebih awal dari `b`,
   - **nol** jika sama,
   - nilai **positif** jika `a` urutannya lebih akhir dari `b`.

Alur insersi, pencarian, traversal, dan penghapusannya **sama persis** dengan versi bilangan bulat.

### Program Lengkap

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define KEY_SIZE 16

struct Mahasiswa {
    char nama[100];
    char prodi[50];
    int angkatan;
};

/* merepresentasikan suatu node dalam BST */
struct MahasiswaNode {
    char key[KEY_SIZE];     /* key: indeks berupa string yang menyatakan NIM */
    struct Mahasiswa value; /* value: data mahasiswa sesuai dengan key tersebut */

    /* link yang mengarah ke anak kiri dan anak kanan */
    struct MahasiswaNode *leftChild, *rightChild;
};

/* menambah mahasiswa (insersi BST) */
void tambahMahasiswa(struct MahasiswaNode **tree, const char *key, const struct Mahasiswa *value) {
    int cmp;

    if (*tree == NULL) {
        /* posisi kosong ditemukan: buat node baru */
        struct MahasiswaNode *newNode;

        newNode = (struct MahasiswaNode *)malloc(sizeof(struct MahasiswaNode));
        if (newNode == NULL) {
            return;
        }
        strncpy(newNode->key, key, KEY_SIZE - 1);
        newNode->key[KEY_SIZE - 1] = '\0';
        newNode->value = *value;
        newNode->leftChild = NULL;
        newNode->rightChild = NULL;

        *tree = newNode;
        return;
    }

    cmp = strcmp(key, (*tree)->key);
    if (cmp < 0) {
        tambahMahasiswa(&(*tree)->leftChild, key, value);
    } else if (cmp > 0) {
        tambahMahasiswa(&(*tree)->rightChild, key, value);
    } else {
        printf("Mahasiswa dengan NIM %s sudah ada sebelumnya\n", key);
    }
}

/* menampilkan secara inorder: key/NIM akan tampil terurut */
void tampilkanDataMahasiswa(const struct MahasiswaNode *tree) {
    if (tree != NULL) {
        tampilkanDataMahasiswa(tree->leftChild);   /* left */

        printf("%s: %s, Prodi %s, Angkatan %d\n",   /* root */
               tree->key, tree->value.nama, tree->value.prodi, tree->value.angkatan);

        tampilkanDataMahasiswa(tree->rightChild);  /* right */
    }
}

/* mencari mahasiswa berdasarkan NIM; NULL jika tidak ada */
struct MahasiswaNode *cariMahasiswa(struct MahasiswaNode *tree, const char *key) {
    int cmp;

    if (tree == NULL) {
        return NULL;
    }

    cmp = strcmp(key, tree->key);
    if (cmp < 0) {
        return cariMahasiswa(tree->leftChild, key);
    } else if (cmp > 0) {
        return cariMahasiswa(tree->rightChild, key);
    }
    return tree;
}

/* menghapus mahasiswa berdasarkan NIM */
void hapusMahasiswa(struct MahasiswaNode **tree, const char *key) {
    struct MahasiswaNode *curNode;
    int cmp;

    if (*tree == NULL) {
        printf("Mahasiswa dengan NIM %s tidak ditemukan\n", key);
        return;
    }

    curNode = *tree;
    cmp = strcmp(key, curNode->key);
    if (cmp < 0) {
        hapusMahasiswa(&curNode->leftChild, key);
    } else if (cmp > 0) {
        hapusMahasiswa(&curNode->rightChild, key);
    } else if (curNode->leftChild == NULL) {
        /* leaf, atau hanya punya anak kanan */
        *tree = curNode->rightChild;
        free(curNode);
    } else if (curNode->rightChild == NULL) {
        /* hanya punya anak kiri */
        *tree = curNode->leftChild;
        free(curNode);
    } else {
        /* punya dua anak: gantikan dengan successor */
        struct MahasiswaNode *successor = curNode->rightChild;
        char successorKey[KEY_SIZE];

        while (successor->leftChild != NULL) {
            successor = successor->leftChild;
        }

        strcpy(successorKey, successor->key);   /* simpan key sebelum node dihapus */
        strcpy(curNode->key, successor->key);
        curNode->value = successor->value;
        hapusMahasiswa(&curNode->rightChild, successorKey);
    }
}

/* menghapus seluruh tree secara postorder */
void hapusSemuaMahasiswa(struct MahasiswaNode **tree) {
    if (*tree != NULL) {
        hapusSemuaMahasiswa(&(*tree)->leftChild);   /* left */
        hapusSemuaMahasiswa(&(*tree)->rightChild);  /* right */
        free(*tree);                                /* root */
        *tree = NULL;
    }
}

int main() {
    struct MahasiswaNode *tree = NULL;
    struct MahasiswaNode *cari;
    struct Mahasiswa mhs;

    strcpy(mhs.nama, "Budi Setiawan");
    strcpy(mhs.prodi, "Informatika");
    mhs.angkatan = 2020;
    tambahMahasiswa(&tree, "M0520015", &mhs);

    strcpy(mhs.nama, "Rizal Setiabudi");
    strcpy(mhs.prodi, "Informatika");
    mhs.angkatan = 2019;
    tambahMahasiswa(&tree, "M0519064", &mhs);

    strcpy(mhs.nama, "Yusuf Hendrawan");
    strcpy(mhs.prodi, "Informatika");
    mhs.angkatan = 2021;
    tambahMahasiswa(&tree, "M0521079", &mhs);

    strcpy(mhs.nama, "Joko Susilo");
    strcpy(mhs.prodi, "Informatika");
    mhs.angkatan = 2020;
    tambahMahasiswa(&tree, "M0520041", &mhs);

    printf("Menampilkan semua data ...\n");
    tampilkanDataMahasiswa(tree);
    printf("\n");

    printf("Mencari nama dari M0519064 ...\n");
    cari = cariMahasiswa(tree, "M0519064");
    if (cari != NULL) {
        printf("Nama: %s\n", cari->value.nama);
    } else {
        printf("Tidak ketemu :(\n");
    }
    printf("\n");

    printf("Mencari nama dari M0519065 ...\n");
    cari = cariMahasiswa(tree, "M0519065");
    if (cari != NULL) {
        printf("Nama: %s\n", cari->value.nama);
    } else {
        printf("Tidak ketemu :(\n");
    }
    printf("\n");

    printf("Mengganti nama dari M0519064 ...\n");
    cari = cariMahasiswa(tree, "M0519064");
    if (cari != NULL) {
        strcpy(cari->value.nama, "Rizal Setiaji");
    }
    tampilkanDataMahasiswa(tree);
    printf("\n");

    printf("Menghapus M0520041 (leaf) ...\n");
    hapusMahasiswa(&tree, "M0520041");
    tampilkanDataMahasiswa(tree);
    printf("\n");

    printf("Menambahkan M0520015 ...\n");
    strcpy(mhs.nama, "Bagus Haryono");
    strcpy(mhs.prodi, "Informatika");
    mhs.angkatan = 2020;
    tambahMahasiswa(&tree, "M0520015", &mhs); /* gagal: NIM sudah ada */
    printf("\n");

    printf("Menghapus M0520015 (root, dua anak) ...\n");
    hapusMahasiswa(&tree, "M0520015");
    tampilkanDataMahasiswa(tree);
    printf("\n");

    printf("Menghapus semua mahasiswa ...\n");
    hapusSemuaMahasiswa(&tree);
    tampilkanDataMahasiswa(tree); /* seharusnya tidak menampilkan apa-apa */
    printf("\n");

    return 0;
}

/*
Output:

Menampilkan semua data ...
M0519064: Rizal Setiabudi, Prodi Informatika, Angkatan 2019
M0520015: Budi Setiawan, Prodi Informatika, Angkatan 2020
M0520041: Joko Susilo, Prodi Informatika, Angkatan 2020
M0521079: Yusuf Hendrawan, Prodi Informatika, Angkatan 2021

Mencari nama dari M0519064 ...
Nama: Rizal Setiabudi

Mencari nama dari M0519065 ...
Tidak ketemu :(

Mengganti nama dari M0519064 ...
M0519064: Rizal Setiaji, Prodi Informatika, Angkatan 2019
M0520015: Budi Setiawan, Prodi Informatika, Angkatan 2020
M0520041: Joko Susilo, Prodi Informatika, Angkatan 2020
M0521079: Yusuf Hendrawan, Prodi Informatika, Angkatan 2021

Menghapus M0520041 (leaf) ...
M0519064: Rizal Setiaji, Prodi Informatika, Angkatan 2019
M0520015: Budi Setiawan, Prodi Informatika, Angkatan 2020
M0521079: Yusuf Hendrawan, Prodi Informatika, Angkatan 2021

Menambahkan M0520015 ...
Mahasiswa dengan NIM M0520015 sudah ada sebelumnya

Menghapus M0520015 (root, dua anak) ...
M0519064: Rizal Setiaji, Prodi Informatika, Angkatan 2019
M0521079: Yusuf Hendrawan, Prodi Informatika, Angkatan 2021

Menghapus semua mahasiswa ...

*/
```

---

## Contoh Penerapan Tree di Dunia Nyata

- **Sistem file**: folder dan subfolder membentuk tree umum.
- **Dokumen HTML/XML**: elemen di dalam elemen membentuk tree (disebut DOM).
- **Indeks database**: banyak database memakai varian tree (misalnya B-Tree) agar pencarian data cepat.
- **Map/dictionary terurut** (`std::map`, `TreeMap`): berbasis BST seimbang.
- **Pohon keputusan** dan **ekspresi matematika** pada kompiler dan kalkulator.

---

## Kesalahan Umum

| Kesalahan | Akibat | Cara menghindari |
|---|---|---|
| Mengirim `struct TreeNode *` (bukan `**`) ke fungsi insersi/hapus | Root atau link tidak berubah di luar fungsi | Gunakan pointer ke pointer untuk fungsi yang mengubah struktur |
| Lupa kasus `NULL` pada fungsi rekursif | Crash (*segmentation fault*) | Selalu tulis basis rekursi `if (tree == NULL)` lebih dulu |
| Menganggap BST valid hanya karena child langsung benar | Tree yang dibuat melanggar aturan BST | Aturan berlaku untuk **seluruh subtree** |
| Menghapus node dengan dua anak tanpa successor | Subtree anak hilang atau bocor | Gunakan successor (min dari subtree kanan) |
| Menghapus root saat hanya punya satu anak lalu mengisi `*tree = NULL` | Seluruh subtree hilang | Isi `*tree` dengan **anak yang tersisa**, bukan `NULL` |
| Membebaskan root sebelum anak-anaknya | Kehilangan pointer ke anak, *memory leak* | Bebaskan memakai **Post-Order** |
| Memakai `==` untuk membandingkan string | Hasil salah (membandingkan alamat) | Gunakan `strcmp` |
| Menyalin key lebih panjang dari ukuran `key[]` | *Buffer overflow* | Pakai `strncpy` dan sisakan ruang untuk `'\0'` |

---

## Ringkasan

- **Tree** adalah struktur data bercabang. **Binary tree** punya maksimal dua child per node, dan **BST** menambahkan aturan: seluruh subtree kiri lebih kecil, seluruh subtree kanan lebih besar.
- **Insersi** dan **pencarian** mengikuti pola yang sama: bandingkan, lalu belok kiri atau kanan sampai menemukan posisinya atau bertemu `NULL`.
- **Traversal**: *Pre-Order* (root, kiri, kanan), *In-Order* (kiri, root, kanan), *Post-Order* (kiri, kanan, root). **In-Order pada BST menghasilkan data terurut.**
- **Penghapusan** punya tiga kasus: leaf, satu anak, dan dua anak (pakai *successor*).
- Fungsi yang mengubah tree memakai **pointer ke pointer**, supaya perubahan link terlihat di luar fungsi.
- Kecepatan BST bergantung pada tinggi tree: **O(log n)** jika seimbang, tetapi bisa turun menjadi **O(n)** jika tree miring.
- BST dapat dipakai sebagai penyimpanan berbasis **key-value**, seperti mencari mahasiswa lewat NIM.

---

## Latihan

1. **Mudah:** Gambarlah BST hasil insersi berurutan `40, 20, 60, 10, 30, 50, 70`, lalu tuliskan hasil Pre-Order, In-Order, dan Post-Order-nya.
2. **Mudah:** Dari tree pada soal 1, tentukan: root, leaf, tinggi tree, dan sibling dari `30`.
3. **Sedang:** Tulis fungsi `int countNodes(const struct TreeNode *tree)` yang mengembalikan jumlah node. Petunjuk: pikirkan basis rekursinya terlebih dahulu.
4. **Sedang:** Tulis fungsi `struct TreeNode *findMax(struct TreeNode *tree)` yang mencari nilai terbesar dalam BST. Petunjuk: kebalikan dari `findMin`.
5. **Sedang:** Pada tree soal 1, hapus node `20`, lalu `40`. Gambarlah tree setelah setiap penghapusan, lalu cocokkan dengan hasil program.
6. **Tantangan:** Tulis fungsi `int isBST(const struct TreeNode *tree)` yang memeriksa apakah sebuah binary tree memenuhi aturan BST. Petunjuk: ingat jebakan `60` pada contoh di atas. Cara yang mudah adalah memanfaatkan sifat hasil In-Order.
7. **Tantangan:** Ubah program Mahasiswa agar bisa menampilkan data **terbalik** (NIM terbesar lebih dulu) hanya dengan mengubah urutan traversal.

[Silabus >>](../silabus.md)
