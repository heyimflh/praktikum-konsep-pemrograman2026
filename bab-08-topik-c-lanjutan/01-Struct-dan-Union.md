<div align="center">

![Banner](https://capsule-render.vercel.app/api?type=waving&color=e65100&height=150&section=header&text=8.1%20Struct%20dan%20Union&fontSize=35&fontColor=ffffff&animation=fadeIn&desc=Pengelompokan%20Data%20Heterogen%20dan%20Alokasi%20Memori%20Bersama&descSize=14&descAlignY=75)

[⬅️ Overview Bab 8](Bab8-Overview.md) &nbsp;•&nbsp; [📋 Daftar Materi](../Daftar_Materi.md) &nbsp;•&nbsp; [Modul 8.2: Bit Manipulation ➡️](02-Bit-Manipulation.md)

---

</div>

## 🎯 Tujuan Pembelajaran

Setelah mempelajari modul ini, kamu diharapkan mampu:
1. Menjelaskan perbedaan antara tipe data primitif, array homogen, dan tipe bentukan (`struct`).
2. Mendeklarasikan, mendefinisikan, dan menginisialisasi variabel bertipe `struct`.
3. Mengakses data anggota struct menggunakan operator titik (`.`) dan operator panah (`->`).
4. Mengoperasikan struct bersama pointer dan alokasi memori dinamis (`malloc`).
5. Memahami konsep alokasi memori bersama pada `union` dan penerapannya pada *tagged union*.
6. Menyederhanakan sintaks penulisan kode menggunakan *type aliasing* (`typedef`).

---

## 1. Struct (Struktur Data)

### Pengantar Masalah

Coba perhatikan kode di bawah ini:

```c
char weapon_names[5][50] = {"Desert Eagle", "H&K MP5", "AK-47", "M4A1", "AWP Magnum"};
int weapon_prices[5] = {750, 1500, 2500, 3100, 4750};
int weapon_damages[5] = {35, 15, 25, 20, 100};
int weapon_rounds[5] = {7, 30, 30, 30, 10};
```

**Apa yang dapat kamu simpulkan dari kode di atas?**  
Kode di atas mendeklarasikan 4 array terpisah yang memiliki relasi implisit: tiap elemen pada indeks yang sama merepresentasikan deskripsi satu obyek senjata (*weapon*) yang sama.

- Indeks `0`: *Desert Eagle* (harga `750`, damage `35`, rounds `7`)
- Indeks `1`: *H&K MP5* (harga `1500`, damage `15`, rounds `30`)
- Indeks `2`: *AK-47* (harga `2500`, damage `25`, rounds `30`)
- dst...

**Permasalahan:**  
Bayangkan saat kamu ingin mengirimkan seluruh informasi senjata ke dalam sebuah fungsi melalui argumen:

```c
void print_weapon(const char *name, int price, int damage, int rounds) {
    printf("Weapon name: %s\n", name);
    printf(" Price: $%d\n", price);
    printf(" Damage: %d%%\n", damage);
    printf(" Rounds: %d\n", rounds);
}

int main() {
    char weapon_names[5][50] = {"Desert Eagle", "H&K MP5", "AK-47", "M4A1", "AWP Magnum"};
    int weapon_prices[5] = {750, 1500, 2500, 3100, 4750};
    int weapon_damages[5] = {35, 15, 25, 20, 100};
    int weapon_rounds[5] = {7, 30, 30, 30, 10};

    /* Print informasi senjata H&K MP5 (indeks: 1) */
    print_weapon(weapon_names[1], weapon_prices[1], weapon_damages[1], weapon_rounds[1]);

    return 0;
}
```

> [!WARNING]
> Parameter fungsi `print_weapon` memuat 4 variabel terpisah untuk 1 entitas obyek yang sama. Jika kita ingin menambah atribut baru (misalnya `clips`, `ammo_type`, `manufacturer`), kita harus mengubah tanda tangan fungsi serta semua pemanggilannya. Ini rentan kesalahan dan sulit dirawat.

---

### Solusi Menggunakan Struct

Kita dapat membungkus seluruh atribut ke dalam sebuah tipe data bentukan bernama `struct Weapon`:

```c
#include <stdio.h>

/* Definisikan tipe data struct yang merepresentasikan senjata */
struct Weapon {
    char name[50];
    int price;
    int damage;
    int rounds;
};

/* Gunakan pointer `const struct Weapon *` untuk efisiensi */
void print_weapon(const struct Weapon *wpn) {
    /* Akses member menggunakan operator panah (->) */
    printf("Weapon name: %s\n", wpn->name);
    printf(" Price: $%d\n", wpn->price);
    printf(" Damage: %d%%\n", wpn->damage);
    printf(" Rounds: %d\n", wpn->rounds);
}

int main() {
    struct Weapon weapon_list[5] = {
        {"Desert Eagle", 750, 35, 7},
        {"H&K MP5", 1500, 15, 30},
        {"AK-47", 2500, 25, 30},
        {"M4A1", 3100, 20, 30},
        {"AWP Magnum", 4750, 100, 10}
    };

    /* Mengirim alamat elemen (&) agar tidak ada penyalinan memori besar */
    print_weapon(&weapon_list[1]);
    print_weapon(&weapon_list[3]);

    return 0;
}
```

**Output:**
```
Weapon name: H&K MP5
 Price: $1500
 Damage: 15%
 Rounds: 30
Weapon name: M4A1
 Price: $3100
 Damage: 20%
 Rounds: 30
```

---

### Deklarasi & Definisi Tipe Struct

**1. Deklarasi (Forward Declaration):**
```c
struct Weapon;
```

**2. Definisi Struktur:**
```c
struct Weapon {
    char name[50];
    int price;
    int damage;
    int rounds;
};
```

Variabel di dalam struct disebut **member variables** (variabel anggota). Setiap anggota dapat memiliki tipe data yang berbeda-beda.

---

### Deklarasi & Inisialisasi Variabel Struct

**1. Bersamaan dengan deklarasi:**
```c
struct Weapon deagle = {"Desert Eagle", 750, 35, 7};
```

**2. Terpisah dari deklarasi:**
```c
#include <string.h>

struct Weapon deagle;

strcpy(deagle.name, "Desert Eagle");
deagle.price = 750;
deagle.damage = 35;
deagle.rounds = 7;
```

**3. Designated Initializer (C99):**
```c
struct Weapon ak47 = {
    .name = "AK-47",
    .price = 2500,
    .damage = 25,
    .rounds = 30
};
```

---

### Operasi Assignment Pada Struct

Variabel struct dapat langsung disalin ke variabel struct lain bertipe sama menggunakan operator assignment (`=`):

```c
struct Weapon w1 = {"M4A1", 3100, 20, 30};
struct Weapon w2;

/* Seluruh isi memori w1 disalin ke w2 */
w2 = w1;
```

> [!NOTE]
> Assignment pada struct melakukan *shallow copy* (menyalin nilai tiap anggota bit demi bit). Jika salah satu anggota adalah pointer, alamat memorinya yang tersalin, bukan data yang ditunjuk pointer tersebut.

---

### Membaca dan Menulis Member Struct

Gunakan operator titik (`.`) saat mengakses member dari variabel objek langsung:

```c
struct Weapon deagle = {"Desert Eagle", 750, 35, 7};

/* Membaca */
printf("Nama: %s, Damage: %d%%\n", deagle.name, deagle.damage);

/* Menulis */
deagle.damage = 50;
printf("Damage Baru: %d%%\n", deagle.damage);
```

---

### Pointer Pada Struct & Operator Panah (`->`)

Di dunia nyata, kita hampir selalu melewatkan struct ke fungsi menggunakan **pointer** (*pass-by-reference*):

| Jenis Parameter | Sintaks Akses | Dampak Memori |
|---|:---:|---|
| By Value (`struct Weapon wpn`) | `wpn.damage` | Menyalin seluruh ukuran struct ke stack (boros) |
| By Pointer (`struct Weapon *wpn`) | `wpn->damage` atau `(*wpn).damage` | Hanya mengirim 4 atau 8 byte alamat (sangat efisien) |

```c
/* Pass by reference untuk mengubah nilai asli */
void upgrade_weapon(struct Weapon *wpn) {
    wpn->damage += 10;
}

/* Pass by reference (const) untuk pembacaan aman */
void print_weapon(const struct Weapon *wpn) {
    printf("%s: %d dmg\n", wpn->name, wpn->damage);
}
```

---

### Dynamic Memory Allocation Pada Struct

Struct dapat dialokasikan di Heap menggunakan `malloc()`:

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct Weapon *deagle = (struct Weapon *)malloc(sizeof(struct Weapon));
if (deagle != NULL) {
    strcpy(deagle->name, "Desert Eagle");
    deagle->price = 750;
    deagle->damage = 35;
    deagle->rounds = 7;

    print_weapon(deagle);
    free(deagle);
}
```

Alokasi array dinamis dari struct:
```c
int count = 2;
struct Weapon *list = (struct Weapon *)malloc(count * sizeof(struct Weapon));

strcpy(list[0].name, "Desert Eagle");
list[0].price = 750;

strcpy(list[1].name, "M4A1");
list[1].price = 3100;

free(list);
```

---

### Contoh Lengkap Struct Dinamis

<details>
<summary><b>🔍 Klik untuk melihat kode lengkap program input senjata dinamis</b></summary>

```c
#include <stdio.h>
#include <stdlib.h>

struct Weapon {
    char name[50];
    int price;
    int damage;
    int rounds;
};

void print_weapons(const struct Weapon *weapon_list, int weapon_count);

int main() {
    int i, weapon_count;
    struct Weapon *weapon_list;

    printf("Enter number of weapons: ");
    if (scanf("%d", &weapon_count) != 1 || weapon_count <= 0) {
        return 1;
    }

    weapon_list = (struct Weapon *)malloc(weapon_count * sizeof(struct Weapon));
    if (weapon_list == NULL) {
        printf("Gagal mengalokasikan memori.\n");
        return 1;
    }

    for (i = 0; i < weapon_count; i++) {
        printf("\nWeapon #%d name (no spaces): ", i + 1);
        scanf("%49s", weapon_list[i].name);

        printf("Weapon #%d price: ", i + 1);
        scanf("%d", &weapon_list[i].price);

        printf("Weapon #%d damage: ", i + 1);
        scanf("%d", &weapon_list[i].damage);

        printf("Weapon #%d rounds: ", i + 1);
        scanf("%d", &weapon_list[i].rounds);
    }

    print_weapons(weapon_list, weapon_count);

    free(weapon_list);
    return 0;
}

void print_weapons(const struct Weapon *weapon_list, int weapon_count) {
    int i;
    printf("\n=== WEAPONS LIST ===\n");
    for (i = 0; i < weapon_count; i++) {
        printf("[%d] %-15s | Price: $%d | Dmg: %d%% | Rounds: %d\n",
               i + 1, weapon_list[i].name, weapon_list[i].price,
               weapon_list[i].damage, weapon_list[i].rounds);
    }
}
```

</details>

---

## 2. Union (Memori Bersama)

Konsep union secara sintaks mirip dengan struct. Namun perbedaannya sangat mendasar pada **layout memori**:

- Pada **`struct`**: setiap member menempati slot memori tersendiri yang berdampingan. Ukurannya adalah total ukuran semua anggota (+ padding).
- Pada **`union`**: seluruh member **berbagi alamat memori awal yang sama**. Ukuran union hanya sebesar anggota terbesarnya.

```c
/* Struct: 4 + 4 + 8 = 16 byte (+ padding jika ada) */
struct StructContoh {
    int iVal;
    float fVal;
    double dVal;
};

/* Union: Hanya 8 byte (sebesar double) */
union UnionContoh {
    int iVal;
    float fVal;
    double dVal;
};
```

> [!CAUTION]
> Pada union, hanya **satu** member yang dapat menyimpan nilai valid pada satu waktu. Menulis ke salah satu member akan menimpa bit representasi member lainnya!

Perhatikan contoh berikut:
```c
#include <stdio.h>

union Number {
    int iVal;
    float fVal;
};

int main() {
    union Number num;

    num.iVal = 32;
    num.fVal = 64.0f; // Menimpa bit iVal!

    // num.iVal kini menginterpretasikan bit float 64.0 sebagai integer
    printf("num.iVal = %d\n", num.iVal);
    printf("num.fVal = %f\n", num.fVal);

    return 0;
}
```

---

### Penerapan Union: Tagged Union

Pola umum penggunaan union di dunia nyata adalah digabungkan dengan struct untuk membuat *tagged union* (atau *variant*):

<details>
<summary><b>🔍 Klik untuk melihat kode Tagged Union (Tipe Dinamis)</b></summary>

```c
#include <stdio.h>

#define NUM_INT     0
#define NUM_DOUBLE  1

union NumberValue {
    int iVal;
    double dblVal;
};

struct Number {
    int type;                  // Tag penanda tipe aktif
    union NumberValue value;   // Nilai data
};

void printNumber(struct Number num) {
    switch (num.type) {
    case NUM_INT:
        printf("Integer: %d\n", num.value.iVal);
        break;
    case NUM_DOUBLE:
        printf("Double : %lf\n", num.value.dblVal);
        break;
    }
}

int main() {
    struct Number a, b;

    a.type = NUM_INT;
    a.value.iVal = 42;

    b.type = NUM_DOUBLE;
    b.value.dblVal = 3.14159;

    printNumber(a);
    printNumber(b);

    return 0;
}
```

</details>

---

## 3. Type Aliasing (`typedef`)

Kata kunci `typedef` digunakan untuk memberikan nama alias (*alias name*) pada tipe data sehingga kita tidak perlu menuliskan kata kunci `struct` berulang kali.

### Tiga Cara Penulisan `typedef` Pada Struct:

**Versi 1: Terpisah**
```c
struct Weapon {
    char name[50];
    int price;
};
typedef struct Weapon Weapon;
```

**Versi 2: Bersamaan (Bernama)**
```c
typedef struct Weapon {
    char name[50];
    int price;
} Weapon;
```

**Versi 3: Anonim**
```c
typedef struct {
    char name[50];
    int price;
} Weapon;
```

Dengan `typedef`, kita dapat mendeklarasikan variabel dan pointer secara langsung:
```c
Weapon deagle;
Weapon *ptr = &deagle;
```

---

<div align="center">

[⬅️ Sebelumnya: Overview Bab 8](Bab8-Overview.md) &nbsp;&nbsp;|&nbsp;&nbsp; [Lanjut ke 8.2: Bit Manipulation ➡️](02-Bit-Manipulation.md)

</div>

