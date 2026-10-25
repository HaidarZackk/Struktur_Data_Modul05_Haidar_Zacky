# <h1 align="center"> Modul 5 SINGLY LINKED LIST (BAGIAN KEDUA) </h1>
<p align="center">Muhammad Haidar Az Zacky - 109082530035</p>

## Dasar Teori

### A. Searching pada Singly Linked List

*Searching* adalah operasi dasar pada list berupa pencarian terhadap *node* tertentu. Prosesnya berjalan dengan mengunjungi setiap *node* dari `first`, lalu berhenti setelah *node* yang dicari ditemukan atau setelah seluruh list selesai ditelusuri (pointer mencapai `Nil`). Karena singly linked list hanya memiliki satu arah pointer dan tidak bisa diakses langsung lewat indeks seperti array, pencarian dilakukan secara **sekuensial** (*sequential search*) dari node pertama ke node berikutnya.

Menurut modul, operasi *searching* membuat operasi lain seperti *insert after*, *delete after*, dan *update* menjadi lebih mudah, sebab operasi-operasi tersebut membutuhkan alamat node yang tepat sebagai acuan.

Kompleksitas pencarian sekuensial adalah O(1) pada kasus terbaik (data ada di node pertama) dan O(n) pada kasus terburuk (data berada di node terakhir atau tidak ada), dengan n adalah jumlah node.

Langkah umum pencarian berdasarkan nilai:
1. Mulai dari `P = L.first`.
2. Selama `P != Nil`, bandingkan data node `P` dengan data yang dicari.
3. Jika sama, hentikan pencarian dan kembalikan alamat `P` (atau catat posisinya).
4. Jika tidak, maju ke node berikutnya dengan `P = P->next`.
5. Jika `P` mencapai `Nil`, data tidak ada dan dikembalikan `Nil` (atau penanda tidak ditemukan).

### B. Primitif Pencarian pada ADT

Pada ADT singly linked list di modul, terdapat tiga primitif pencarian:

| Primitif | Keterangan |
|---|---|
| `findElm(L, X)` | Mencari elemen list dengan `P->info = X`. Jika ada, mengembalikan alamat elemen tersebut, jika tidak mengembalikan `Nil` |
| `fFindElm(L, P)` | Mencari apakah ada elemen list dengan alamat `P`. Mengembalikan `true` jika ada dan `false` jika tidak ada |
| `findBefore(L, P)` | Mengembalikan alamat elemen sebelum `P`. Jika `P` berada di awal list, mengembalikan `Nil` |

Selain primitif pencarian, ADT pada modul juga memuat primitif lain: `ListEmpty`, `CreateList`, `dealokasi`, `insertFirst`, `insertAfter`, `insertLast`, `delFirst`, `delLast`, `delAfter`, `delP`, `printInfo`, `nbList`, `delAll`, `invertList`, `copyList`, dan `fCopyList`. Seluruhnya disimpan dalam file `*.h` (tipe dan prototipe) dan `*.cpp` (realisasi).

### C. Update Data Node

*Update* adalah operasi mengubah isi data pada node yang sudah ada tanpa mengubah susunan pointer. Pola kerjanya adalah menemukan node target terlebih dahulu, lalu mengganti `isidata` node tersebut. Pada praktikum ini ada tiga bentuk update: node pertama (`updateFirst`), node terakhir (`updateLast`, dengan menelusuri sampai `next == Nil`), dan node sesudah node tertentu (`updateAfter`, yang mengubah `nodePrev->next`).

### D. Variasi Pencarian pada Program Guided

Selain pencarian berdasarkan nilai info, pencarian dapat dilakukan berdasarkan kriteria lain, yaitu:
1. **Berdasarkan data**, membandingkan salah satu field (misalnya nama buah) dengan data yang dicari, lalu menampilkan posisinya.
2. **Berdasarkan alamat**, membandingkan alamat setiap node dengan alamat yang dicari.
3. **Berdasarkan range**, menampilkan semua node yang nilainya berada pada rentang tertentu (misalnya rentang harga). Pencarian ini tidak berhenti pada kecocokan pertama karena bisa ada lebih dari satu node yang sesuai.

## Guided

### 1. Program listBuah.h

```C++
#ifndef LISTBUAH_H
#define LISTBUAH_H
#define Nil NULL

#include<iostream>
using namespace std;

struct buah{
    string nama;
    int jumlah;
    float harga;
};

typedef buah dataBuah;

typedef struct node *address;

struct node{
    dataBuah isidata;
    address next;
};

struct linkedlist{
    address first;
};

bool isEmpty(linkedlist List);
void createList(linkedlist &List);
address alokasi(string nama, int jumlah, float harga);
void dealokasi(address &node);
void printList(linkedlist List);
void insertFirst(linkedlist &List, address nodeBaru);
void insertAfter(linkedlist &List, address nodeBaru, address Prev);
void insertLast(linkedlist &List, address nodeBaru);
void delFirst(linkedlist &List);
void delLast(linkedlist &List);
void delAfter(linkedlist &List, address nodeHapus, address nodePrev);
int nbList(linkedlist List);
void deleteList(linkedlist &List);

void updateFirst(linkedlist List);
void updateLast(linkedlist List);
void updateAfter(linkedlist List, address prev);

void FindNodeByData(linkedlist list, string data);
void FindNodeByAddress(linkedlist list, address node);
void FindNodeByRange(linkedlist list, float hargaAwal, float HargaAkhir);

#endif
```
**Penjelasan:** Ini adalah *header* yang menjadi "kontrak" ADT singly linked list untuk data buah.
- `#ifndef LISTBUAH_H / #define LISTBUAH_H / #endif` adalah header guard supaya file tidak terbaca dua kali dalam satu program. `#define Nil NULL` memberi nama `Nil` untuk pointer kosong sesuai istilah pada materi.
- `struct buah` adalah data majemuk berisi `nama` (string), `jumlah` (int), dan `harga` (float). `typedef buah dataBuah` memberi nama alias `dataBuah` untuk struct tersebut.
- `typedef struct node *address` membuat `address` menjadi alias pointer ke `node`. `struct node` adalah elemen list yang berisi `isidata` (bertipe `dataBuah`) dan pointer `next` ke node berikutnya.
- `struct linkedlist` hanya menyimpan satu pointer `first` yang menunjuk node pertama.
- Prototipe dibagi dua kelompok. **Materi Modul 4**: `isEmpty`, `createList`, `alokasi`, `dealokasi`, `printList`, `insertFirst/After/Last`, `delFirst/Last/After`, `nbList`, `deleteList`. **Materi Modul 5**: tiga prosedur *update* (`updateFirst`, `updateLast`, `updateAfter`) dan tiga prosedur *searching* (`FindNodeByData`, `FindNodeByAddress`, `FindNodeByRange`).

### 2. Program listBuah.cpp

```C++
#include "listBuah.h"
#include <iostream>
using namespace std;

bool isEmpty(linkedlist List) {
    if(List.first == Nil){
        return true;
    } else {
        return false;
    }
}

void createList(linkedlist &List) {
    List.first = Nil;
}

address alokasi(string nama, int jumlah, float harga) {
    address nodeBaru = new node;
    nodeBaru->isidata.nama = nama;
    nodeBaru->isidata.jumlah = jumlah;
    nodeBaru->isidata.harga = harga;
    nodeBaru->next = Nil;
    return nodeBaru;
}

void dealokasi(address &node) {
    node->next = Nil;
    delete node;
}

void insertFirst(linkedlist &List, address nodeBaru) {
    nodeBaru->next = List.first;
    List.first = nodeBaru;
}

void insertAfter(linkedlist &List, address nodeBaru, address Prev) {
    if (Prev != Nil) {
        nodeBaru->next = Prev->next;
        Prev->next = nodeBaru;
    } else {
        cout << "Node sebelumnya tidak valid!" << endl;
    }
}

void insertLast(linkedlist &List, address nodeBaru) {
    if (isEmpty(List)) {
        List.first = nodeBaru;
    } else {
        address nodeBantu = List.first;
        while (nodeBantu->next != Nil) {
            nodeBantu = nodeBantu->next;
        }
        nodeBantu->next = nodeBaru;
    }
}

void delFirst(linkedlist &List){
    address nodeHapus;
    if (isEmpty(List) == false) {
        nodeHapus = List.first;
        List.first = List.first->next;
        nodeHapus->next = Nil;
        dealokasi(nodeHapus);
        cout << "Node pertama berhasil terhapus!" << endl;
    } else {
        cout << "List kosong!" << endl;
    }
}

void delLast(linkedlist &List){
    address nodeHapus, nodePrev;
    if(isEmpty(List) == false){
        nodeHapus = List.first;
        if(nodeHapus->next == Nil){
            List.first->next = Nil;
            dealokasi(nodeHapus);
        } else {
            while(nodeHapus->next != Nil){
                nodePrev = nodeHapus;
                nodeHapus = nodeHapus->next;
            }
            nodePrev->next = Nil;
            dealokasi(nodeHapus);
        }
        cout << "Node terakhir berhasil terhapus!" << endl;
    } else {
        cout << "list kosong" << endl;
    }
}

void delAfter(linkedlist &List, address nodeHapus, address nodePrev){
    if(isEmpty(List) == true){
        cout << "List kosong!" << endl;
    } else {
        if (nodePrev != Nil && nodePrev->next != Nil) {
            nodeHapus = nodePrev->next;
            nodePrev->next = nodeHapus->next;
            nodeHapus->next = Nil;
            dealokasi(nodeHapus);
            cout << "Node setelah node " << nodePrev->isidata.nama << " berhasil terhapus!" << endl;
        } else {
            cout << "Node sebelumnya (prev) tidak valid!" << endl;
        }
    }
}

void printList(linkedlist List) {
    if (isEmpty(List)) {
        cout << "List kosong." << endl;
    } else {
        address nodeBantu = List.first;
        while (nodeBantu != Nil) {
            cout << "Nama Buah : " << nodeBantu->isidata.nama << ", Jumlah : " << nodeBantu->isidata.jumlah << ", Harga : " << nodeBantu->isidata.harga << endl;
            nodeBantu = nodeBantu->next;
        }
    }
}

int nbList(linkedlist List) {
    int count = 0;
    address nodeBantu = List.first;
    while (nodeBantu != Nil) {
        count++;
        nodeBantu = nodeBantu->next;
    }
    return count;
}

void deleteList(linkedlist &List){
    address nodeBantu, nodeHapus;
    nodeBantu = List.first;
    while(nodeBantu != Nil){
        nodeHapus = nodeBantu;
        nodeBantu = nodeBantu->next;
        dealokasi(nodeHapus);
    }
    List.first = Nil;
    cout << "List berhasil terhapus!" << endl;
}

/*----- MATERI PERTEMUAN 5 - SINGLY LINKED LIST (BAGIAN KEDUA) - UPDATE -----*/
void updateFirst(linkedlist List){
    if(isEmpty(List) == true){
        cout << "List kosong!" << endl;
    } else {
        cout << "Masukkan update data node pertama : " << endl;
        cout << "Nama buah : ";
        cin >> List.first->isidata.nama;
        cout << "Jumlah : ";
        cin >> List.first->isidata.jumlah;
        cout << "Harga : ";
        cin >> List.first->isidata.harga;
        cout << "Data Berhasil Diupdate!" << endl;
        cout << endl;
    }
}

void updateLast(linkedlist List){
    if (isEmpty(List) == true) {
        cout << "List Kosong!" << endl;
    } else {
        address nodeBantu = List.first;
        while (nodeBantu->next != Nil) {
            nodeBantu = nodeBantu->next;
        }
        cout << "masukkan update data node terakhir : " << endl;
        cout << "Nama buah : ";
        cin >> nodeBantu->isidata.nama;
        cout << "Jumlah : ";
        cin >> nodeBantu->isidata.jumlah;
        cout << "Harga : ";
        cin >> nodeBantu->isidata.harga;
        cout << "Data Berhasil Diupdate!" << endl;
        cout << endl;
    }
}

void updateAfter(linkedlist List, address nodePrev){
    if(isEmpty(List) == true){
        cout << "List kosong!" << endl;
    } else {
        if (nodePrev != Nil && nodePrev->next != Nil){
            address nodeBantu = nodePrev->next;
            cout << "masukkan update data node setelah node " << nodePrev->isidata.nama << " : " << endl;
            cout << "Nama buah : ";
            cin >> nodeBantu->isidata.nama;
            cout << "Jumlah : ";
            cin >> nodeBantu->isidata.jumlah;
            cout << "Harga : ";
            cin >> nodeBantu->isidata.harga;
            cout << "Data Berhasil Diupdate!" << endl;
            cout << endl;
        } else {
            cout << "Node sebelumnya (prev) tidak valid!" << endl;
        }
    }
}

/*----- MATERI PERTEMUAN 5 - SINGLY LINKED LIST (BAGIAN KEDUA) - SEARCHING -----*/
void FindNodeByData(linkedlist list, string data){
    if(isEmpty(list) == true){
        cout << "List kosong!" << endl;
    } else {
        address nodeBantu = list.first;
        int posisi = 0;
        bool found = false;
        while(nodeBantu != Nil){
            posisi++;
            if(nodeBantu->isidata.nama == data){
                cout << "Data " << data << " ditemukan pada posisi ke-" << posisi << "!" << endl;
                cout << "Nama Buah : " << nodeBantu->isidata.nama << ", Jumlah : " << nodeBantu->isidata.jumlah << ", Harga : " << nodeBantu->isidata.harga << endl;
                found = true;
                break;
            }
            nodeBantu = nodeBantu->next;
        }
        if(found == false){
            cout << "Node dengan data " << data << " tidak ditemukan!" << endl;
        }
    }
    cout << endl;
}

void FindNodeByAddress(linkedlist list, address node) {
    if(isEmpty(list) == true) {
        cout << "List kosong!" << endl;
    } else {
        address nodeBantu = list.first;
        int posisi = 0;
        bool found = false;
        while (nodeBantu != Nil) {
            posisi++;
            if(nodeBantu == node) {
                cout << "Node ditemukan pada posisi ke-" << posisi << "!" << endl;
                cout << "Alamat node : " << nodeBantu << endl;
                cout << "Nama Buah : " << nodeBantu->isidata.nama << ", Jumlah : " << nodeBantu->isidata.jumlah << ", Harga : " << nodeBantu->isidata.harga << endl;
                found = true;
                break;
            }
            nodeBantu = nodeBantu->next;
        }
        if(found == false) {
            cout << "Node dengan alamat " << node << " tidak ditemukan dalam list!" << endl;
        }
    }
    cout << endl;
}

void FindNodeByRange(linkedlist list, float hargaAwal, float hargaAkhir) {
    if(isEmpty(list) == true) {
        cout << "List kosong!" << endl;
    } else {
        address nodeBantu = list.first;
        int posisi = 0;
        bool found = false;
        cout << "--- Buah dalam range harga " << hargaAwal << " - " << hargaAkhir << " ---" << endl;
        cout << "-------------------------------------------" << endl;
        while (nodeBantu != Nil) {
            posisi++;
            float harga = nodeBantu->isidata.harga;
            if(harga >= hargaAwal && harga <= hargaAkhir) {
                cout << "Data ditemukan pada posisi ke-" << posisi << " :" << endl;
                cout << "Nama Buah : " << nodeBantu->isidata.nama << ", Jumlah : " << nodeBantu->isidata.jumlah << ", Harga : " << nodeBantu->isidata.harga << endl;
                cout << "-------------------------------------------" << endl;
                found = true;
            }
            nodeBantu = nodeBantu->next;
        }
        if(found == false) {
            cout << "Tidak ada data buah dalam range harga tersebut!" << endl;
            cout << "-------------------------------------------" << endl;
        }
    }
    cout << endl;
}
```
**Penjelasan:** File ini berisi realisasi seluruh primitif yang dideklarasikan di `listBuah.h`.

*Bagian Modul 4 (dasar list):*
- `isEmpty` mengembalikan `true` bila `first == Nil`. `createList` mengisi `first` dengan `Nil` sehingga terbentuk list kosong.
- `alokasi` membuat node baru dengan `new node`, mengisi `nama`, `jumlah`, `harga`, dan mengatur `next = Nil`. `dealokasi` mengembalikan memori node dengan `delete`.
- `insertFirst`: `next` node baru diarahkan ke `first` lama, lalu `first` menunjuk node baru. `insertAfter`: `next` node baru mengambil `Prev->next`, lalu `Prev->next` diarahkan ke node baru. `insertLast`: jika list kosong node baru menjadi `first`, jika tidak list ditelusuri sampai node terakhir lalu `next`-nya diarahkan ke node baru.
- `delFirst`, `delLast`, dan `delAfter` memutus node dari list dengan mengatur ulang pointer `next`, lalu mendealokasinya. Pada versi ini ketiganya juga menampilkan pesan konfirmasi.
- `printList` dan `nbList` menelusuri list dari `first` sampai `Nil` untuk mencetak data dan menghitung jumlah node. `deleteList` mendealokasi seluruh node lalu mengembalikan `first` menjadi `Nil`.

*Bagian Modul 5 - Update:*
- `updateFirst` memeriksa list terlebih dahulu. Jika tidak kosong, program meminta masukan nama, jumlah, dan harga baru, lalu langsung menuliskannya ke `List.first->isidata`. Parameter `List` cukup dikirim by value karena yang diubah adalah isi node yang ditunjuk pointer, bukan pointer `first` itu sendiri.
- `updateLast` menelusuri list dengan `nodeBantu` sampai `nodeBantu->next == Nil`, yaitu node terakhir, lalu mengganti datanya.
- `updateAfter` mengganti data node yang berada tepat setelah `nodePrev` (yaitu `nodePrev->next`). Jika `nodePrev` bernilai `Nil` atau tidak punya node sesudahnya, ditampilkan pesan "Node sebelumnya (prev) tidak valid!".
- Karena menggunakan `cin >> nama`, nama buah yang dimasukkan harus satu kata (tanpa spasi).

*Bagian Modul 5 - Searching:*
- `FindNodeByData` menelusuri list sambil menghitung `posisi`. Setiap node dibandingkan dengan `isidata.nama == data`. Begitu cocok, posisi dan data node ditampilkan lalu pencarian dihentikan dengan `break`. Variabel `found` menandai hasil, sehingga bila sampai akhir tidak cocok ditampilkan "tidak ditemukan". Perbandingan string bersifat *case-sensitive*, jadi "kelapa" berbeda dengan "Kelapa".
- `FindNodeByAddress` bekerja serupa, tetapi yang dibandingkan adalah alamat node (`nodeBantu == node`). Bila ditemukan, ditampilkan posisi, alamat node, dan datanya.
- `FindNodeByRange` mencari semua node yang harganya berada pada rentang `hargaAwal` sampai `hargaAkhir` (batas termasuk, karena memakai `>=` dan `<=`). Berbeda dengan dua pencarian sebelumnya, prosedur ini tidak memakai `break` sehingga seluruh list ditelusuri dan semua node yang sesuai ditampilkan.

### 3. Program main.cpp

```C++
#include "listBuah.h"

#include<iostream>
using namespace std;

int main(){
    linkedlist List;
    address nodeA, nodeB, nodeC, nodeD, nodeE = Nil;
    createList(List);

    dataBuah dtBuah;

    nodeA = alokasi("Jeruk", 100, 3000);
    nodeB = alokasi("Apel", 75, 4000);
    nodeC = alokasi("Pir", 87, 5000);
    nodeD = alokasi("Semangka", 43, 11500);
    nodeE = alokasi("Durian", 15, 31450);

    insertFirst(List, nodeA);
    insertLast(List, nodeB);
    insertAfter(List, nodeC, nodeA);
    insertAfter(List, nodeD, nodeC);
    insertLast(List, nodeE);

    cout << "--- ISI LIST SETELAH DILAKUKAN INSERT ---" << endl;
    printList(List);
    cout << "jumlah node : " << nbList(List) << endl;
    cout << endl;

    updateFirst(List);
    printList(List);
    updateLast(List);
    printList(List);
    updateAfter(List, nodeD);
    printList(List);

    cout << "--- ISI LIST SETELAH DILAKUKAN UPDATE ---" << endl;
    printList(List);
    cout << "jumlah node : " << nbList(List) << endl;
    cout << endl;

    FindNodeByData(List, "kelapa");
    FindNodeByAddress(List, nodeC);
    FindNodeByRange(List, 5000, 10000);

    delFirst(List);
    printList(List);
    delLast(List);
    printList(List);
    delAfter(List, nodeD, nodeC);
    printList(List);

    cout << "--- ISI LIST SETELAH DILAKUKAN DELETE ---" << endl;
    printList(List);
    cout << "jumlah node : " << nbList(List) << endl;
    cout << endl;

    deleteList(List);
    cout << "--- ISI LIST SETELAH DILAKUKAN HAPUS LIST ---" << endl;
    printList(List);
    cout << "jumlah node : " << nbList(List) << endl;
    cout << endl;

    return 0;
}
```
**Penjelasan:** Ini adalah *driver* yang memakai ADT. Program membuat list kosong, mengalokasikan lima node (Jeruk, Apel, Pir, Semangka, Durian), lalu menyisipkannya secara bertahap:
- `insertFirst(A)` menghasilkan **A**; `insertLast(B)` menghasilkan **A, B**; `insertAfter(C, A)` menghasilkan **A, C, B**; `insertAfter(D, C)` menghasilkan **A, C, D, B**; `insertLast(E)` menghasilkan **A, C, D, B, E**. Urutan tampil: Jeruk, Pir, Semangka, Apel, Durian dengan jumlah node 5.
- Tahap **update**: `updateFirst` mengganti data node pertama (Jeruk), `updateLast` mengganti node terakhir (Durian), dan `updateAfter(List, nodeD)` mengganti node setelah Semangka (yaitu Apel). Setiap pemanggilan meminta masukan dari keyboard, sehingga isi list akhir bergantung pada data yang diketikkan.
- Tahap **searching**: `FindNodeByData(List, "kelapa")` mencari nama "kelapa". Hasilnya baru ditemukan bila salah satu data hasil update memang ditulis persis "kelapa" (huruf kecil semua), jika tidak maka tampil pesan tidak ditemukan. `FindNodeByAddress(List, nodeC)` mencari node Pir, yang berada di posisi ke-2. `FindNodeByRange(List, 5000, 10000)` menampilkan semua buah berharga 5000 sampai 10000.
- Tahap **delete**: `delFirst` menghapus node pertama, `delLast` menghapus node terakhir, lalu `delAfter(List, nodeD, nodeC)` menghapus node setelah Pir. Tiap langkah diikuti `printList`.
- `deleteList` menghapus seluruh node sehingga `printList` menampilkan "List kosong." dan jumlah node 0.
- Contoh pengujian dengan masukan update `Mangga 120 6500`, `kelapa 30 8000`, dan `Anggur 60 9500`: list setelah update adalah Mangga, Pir, Semangka, Anggur, kelapa; "kelapa" ditemukan di posisi ke-5; Pir ditemukan di posisi ke-2; pencarian range 5000-10000 menampilkan Mangga (posisi 1), Pir (2), Anggur (4), dan kelapa (5); setelah delete tersisa Pir dan Anggur dengan jumlah node 2.
- Kompilasi harus menyertakan kedua file `.cpp`: `g++ main.cpp listBuah.cpp -o program`.

## Unguided

### 1. Membuat ADT Singly Linked List (Singlylist.h dan Singlylist.cpp) dan menampilkan isi list (Latihan no. 2)

```C++
/* file : Singlylist.h */
#ifndef SINGLYLIST_H_INCLUDED
#define SINGLYLIST_H_INCLUDED

#include <iostream>
#include <cstddef>
using namespace std;

#define Nil NULL

typedef int infotype;
typedef struct ElmList *address;

struct ElmList {
    infotype info;
    address next;
};

struct List {
    address first;
};

void createList(List &L);

address alokasi(infotype x);

void dealokasi(address &P);

void insertFirst(List &L, address P);

void printInfo(List L);

#endif
```
```C++
/* file : Singlylist.cpp */
#include "Singlylist.h"

void createList(List &L) {
    L.first = Nil;
}

address alokasi(infotype x) {
    address P = new ElmList;
    P->info = x;
    P->next = Nil;
    return P;
}

void dealokasi(address &P) {
    delete P;
    P = Nil;
}

void insertFirst(List &L, address P) {
    P->next = L.first;
    L.first = P;
}

void printInfo(List L) {
    address P = L.first;
    while (P != Nil) {
        cout << P->info << " ";
        P = P->next;
    }
    cout << endl;
}
```
```C++
/* file : main.cpp */
#include "Singlylist.h"

int main(){
    List L;
    address P1, P2, P3, P4, P5 = Nil;
    createList(L);

    P1 = alokasi(2);
    insertFirst(L,P1);

    P2 = alokasi(0);
    insertFirst(L,P2);

    P3 = alokasi(8);
    insertFirst(L,P3);

    P4 = alokasi(12);
    insertFirst(L,P4);

    P5 = alokasi(9);
    insertFirst(L,P5);

    printInfo(L);
    return 0;
}
```
### Output Unguided 1 :

##### Output 1
![Output 1]()

**Penjelasan:**
- `Singlylist.h` ditulis mengikuti spesifikasi pada soal: `infotype` bertipe `int`, `address` berupa pointer ke `ElmList`, `ElmList` berisi `info` dan `next`, serta `List` berisi `first`. Prosedur dan fungsinya persis lima buah seperti di soal, yaitu `createList`, `alokasi`, `dealokasi`, `printInfo`, dan `insertFirst`.
- Pada spesifikasi, `dealokasi` bersifat *input/output* sehingga `P` dikirim by reference (`address &P`), sedangkan pada `insertFirst` hanya `L` yang *input/output* dan `P` berstatus *input* sehingga `P` dikirim by value (`address P`).
- Spesifikasi di soal menulis `CreateList`, sedangkan `main.cpp` di soal memanggil `createList`. Karena C++ membedakan huruf besar dan kecil, nama yang dipakai adalah `createList` agar sesuai dengan `main.cpp` soal.
- `alokasi` membuat node baru dengan `new ElmList` dan menyetel `next = Nil`. `insertFirst` selalu menaruh node baru di depan list, sehingga data yang dimasukkan berurutan 2, 0, 8, 12, 9 terbaca terbalik menjadi **9 12 8 0 2**, sesuai Gambar 5-1 dan Gambar 5-2 pada soal.
- `printInfo` menelusuri list dari `first` sampai `Nil` dan mencetak `info` tiap node.
- `main.cpp` sama dengan program utama di soal (hanya ditambah titik koma pada `printInfo(L);`). Program ini tidak memanggil penghapusan list, sehingga memori node dilepas oleh sistem operasi saat program berakhir.
- Kompilasi: `g++ main.cpp Singlylist.cpp -o main`.

### 2. Mencari elemen dengan info 8 menggunakan fungsi baru findElm (Latihan no. 3)

Soal ini melanjutkan Latihan no. 2. `Singlylist.h` dan `Singlylist.cpp` ditambah fungsi `findElm( L : List, x : infotype ) : address`.

```C++
/* file : Singlylist.h */
#ifndef SINGLYLIST_H_INCLUDED
#define SINGLYLIST_H_INCLUDED

#include <iostream>
#include <cstddef>
using namespace std;

#define Nil NULL

typedef int infotype;
typedef struct ElmList *address;

struct ElmList {
    infotype info;
    address next;
};

struct List {
    address first;
};

void createList(List &L);

address alokasi(infotype x);

void dealokasi(address &P);

void insertFirst(List &L, address P);

void printInfo(List L);

address findElm(List L, infotype X);

#endif
```
```C++
/* file : Singlylist.cpp */
#include "Singlylist.h"

void createList(List &L) {
    L.first = Nil;
}

address alokasi(infotype x) {
    address P = new ElmList;
    P->info = x;
    P->next = Nil;
    return P;
}

void dealokasi(address &P) {
    delete P;
    P = Nil;
}

void insertFirst(List &L, address P) {
    P->next = L.first;
    L.first = P;
}

void printInfo(List L) {
    address P = L.first;
    while (P != Nil) {
        cout << P->info << " ";
        P = P->next;
    }
    cout << endl;
}

address findElm(List L, infotype X) {
    address P = L.first;
    while (P != Nil) {
        if (P->info == X) {
            return P;
        }
        P = P->next;
    }
    return Nil;
}
```
```C++
/* file : main.cpp */
#include "Singlylist.h"

int main(){
    List L;
    address P1, P2, P3, P4, P5 = Nil;
    createList(L);

    P1 = alokasi(2);
    insertFirst(L,P1);

    P2 = alokasi(0);
    insertFirst(L,P2);

    P3 = alokasi(8);
    insertFirst(L,P3);

    P4 = alokasi(12);
    insertFirst(L,P4);

    P5 = alokasi(9);
    insertFirst(L,P5);

    address hasil = findElm(L, 8);
    if (hasil != Nil) {
        cout << hasil->info << " ditemukan dalam list" << endl;
    } else {
        cout << "8 tidak ditemukan dalam list" << endl;
    }
    return 0;
}
```
### Output Unguided 2 :

##### Output 2
![Output 2]()

**Penjelasan:**
- `findElm(L, X)` menelusuri list mulai dari `first`. Pada setiap node, `P->info` dibandingkan dengan `X`. Bila sama, fungsi langsung mengembalikan alamat node tersebut. Bila sampai `Nil` tidak ada yang cocok, fungsi mengembalikan `Nil`, sesuai spesifikasi pada modul.
- Pada `main.cpp`, hasil pencarian disimpan di `hasil`. Jika `hasil != Nil`, program mencetak `hasil->info` diikuti " ditemukan dalam list". Karena list berisi 9, 12, 8, 0, 2, nilai 8 ditemukan pada node ketiga (setelah tiga kali perbandingan), sehingga keluaran program adalah `8 ditemukan dalam list`, sama dengan Gambar 5-3 pada soal.
- Bila nilai yang dicari tidak ada (misalnya 99), `findElm` mengembalikan `Nil` dan program mencetak pesan "tidak ditemukan dalam list". Hal ini sudah diuji.
- Fungsi ini mengembalikan **alamat** node, bukan sekadar benar/salah, sehingga hasilnya dapat langsung dipakai untuk operasi lanjutan seperti `insertAfter`, `delAfter`, atau update data.

### 3. Menghitung jumlah total info seluruh elemen (Latihan no. 4)

Soal ini melanjutkan Latihan no. 2 dan 3. Ditambahkan fungsi `totalInfo` untuk menjumlahkan `info` semua elemen.

```C++
/* file : Singlylist.h */
#ifndef SINGLYLIST_H_INCLUDED
#define SINGLYLIST_H_INCLUDED

#include <iostream>
#include <cstddef>
using namespace std;

#define Nil NULL

typedef int infotype;
typedef struct ElmList *address;

struct ElmList {
    infotype info;
    address next;
};

struct List {
    address first;
};

void createList(List &L);

address alokasi(infotype x);

void dealokasi(address &P);

void insertFirst(List &L, address P);

void printInfo(List L);

address findElm(List L, infotype X);

int totalInfo(List L);

#endif
```
```C++
/* file : Singlylist.cpp */
#include "Singlylist.h"

void createList(List &L) {
    L.first = Nil;
}

address alokasi(infotype x) {
    address P = new ElmList;
    P->info = x;
    P->next = Nil;
    return P;
}

void dealokasi(address &P) {
    delete P;
    P = Nil;
}

void insertFirst(List &L, address P) {
    P->next = L.first;
    L.first = P;
}

void printInfo(List L) {
    address P = L.first;
    while (P != Nil) {
        cout << P->info << " ";
        P = P->next;
    }
    cout << endl;
}

address findElm(List L, infotype X) {
    address P = L.first;
    while (P != Nil) {
        if (P->info == X) {
            return P;
        }
        P = P->next;
    }
    return Nil;
}

int totalInfo(List L) {
    int total = 0;
    address P = L.first;
    while (P != Nil) {
        total = total + P->info;
        P = P->next;
    }
    return total;
}
```
```C++
/* file : main.cpp */
#include "Singlylist.h"

int main(){
    List L;
    address P1, P2, P3, P4, P5 = Nil;
    createList(L);

    P1 = alokasi(2);
    insertFirst(L,P1);

    P2 = alokasi(0);
    insertFirst(L,P2);

    P3 = alokasi(8);
    insertFirst(L,P3);

    P4 = alokasi(12);
    insertFirst(L,P4);

    P5 = alokasi(9);
    insertFirst(L,P5);

    cout << "Total info dari kelima elemen adalah " << totalInfo(L) << endl;
    return 0;
}
```
### Output Unguided 3 :

##### Output 3
![Output 3]()

**Penjelasan:**
- `totalInfo(L)` memakai variabel penampung `total` yang diawali 0, lalu menelusuri list dari `first` sampai `Nil` sambil menambahkan `P->info` ke `total`. Setelah semua node dikunjungi, `total` dikembalikan.
- Untuk list 9, 12, 8, 0, 2 perhitungannya 9 + 12 + 8 + 0 + 2 = **31**, sehingga keluaran program adalah `Total info dari kelima elemen adalah 31`, sama dengan Gambar 5-4 pada soal.
- Soal tidak menyediakan prototipe khusus untuk bagian ini, sehingga `totalInfo` ditambahkan sebagai fungsi tambahan di `Singlylist.h` dan direalisasikan di `Singlylist.cpp`, agar logika penjumlahan tetap berada di ADT dan `main.cpp` hanya memanggilnya. Kata "kelima" pada keluaran dituliskan langsung karena list pada soal memang berisi lima elemen.
- Proses penelusurannya sama dengan `printInfo` dan `findElm`, yaitu O(n) karena setiap node dikunjungi satu kali.

## Kesimpulan

**Kesimpulan modul.** Modul ini melanjutkan singly linked list dengan fokus pada operasi *searching* dan *update*. Pencarian dilakukan secara sekuensial dari `first` sampai node yang dicari ditemukan atau pointer mencapai `Nil`, dengan kompleksitas O(n) pada kasus terburuk karena singly linked list tidak mendukung akses langsung seperti array. Hasil pencarian, baik berupa alamat maupun posisi node, menjadi dasar bagi operasi lain seperti *insert after*, *delete after*, dan *update*, sehingga ketiganya menjadi lebih mudah dan aman dilakukan. Seluruh operasi tetap dibungkus dalam ADT yang dipisah menjadi header (`.h`), implementasi (`.cpp`), dan driver (`main.cpp`).

**Kesimpulan Guided.** Pada bagian Guided, ADT linked list dengan data buah (nama, jumlah, harga) dipakai untuk mempraktikkan tiga operasi update (`updateFirst`, `updateLast`, `updateAfter`) dan tiga bentuk pencarian (berdasarkan data, alamat, dan range harga). Program memperlihatkan bahwa update hanya mengubah isi node tanpa mengubah susunan pointer, sehingga alamat node tetap sama dan masih dapat dicari dengan `FindNodeByAddress`. Pencarian berdasarkan data dan alamat berhenti pada kecocokan pertama, sedangkan pencarian berdasarkan range menelusuri seluruh list karena bisa ada banyak node yang sesuai. Pencarian string juga bersifat *case-sensitive*, sehingga penulisan data harus persis sama.

**Kesimpulan Unguided.** Pada bagian Unguided, Latihan no. 2 membangun ADT singly linked list bertipe `int` dengan `insertFirst`, yang menghasilkan list 9 12 8 0 2 dari data masukan 2, 0, 8, 12, 9. Latihan no. 3 menambahkan fungsi `findElm` yang mengembalikan alamat node berinfo 8 sehingga muncul pesan `8 ditemukan dalam list`. Latihan no. 4 menambahkan penelusuran dengan akumulator untuk menghitung total info, menghasilkan 31. Ketiga latihan menunjukkan bahwa banyak operasi pada linked list pada dasarnya adalah pola penelusuran yang sama (`P = P->next` sampai `Nil`) dengan tujuan berbeda: mencetak, mencari, atau menjumlahkan.

**Kesimpulan keseluruhan.** Secara keseluruhan, modul ini menunjukkan bahwa kemampuan menelusuri list dengan benar adalah inti dari hampir semua operasi singly linked list, baik untuk mencari, mengubah, maupun memproses data. Pemisahan ADT ke dalam header, implementasi, dan driver membuat primitif seperti `findElm` mudah dipakai ulang oleh program lain. Di sisi lain, karena akses bersifat sekuensial, linked list kurang cocok untuk pencarian yang sangat sering pada data besar, sehingga pemilihan struktur data perlu disesuaikan dengan operasi yang paling banyak dipakai. Pemahaman ini menjadi bekal untuk mempelajari doubly linked list, circular linked list, serta stack dan queue.

## Referensi

[1] Cormen, T. H., Leiserson, C. E., Rivest, R. L., & Stein, C. (2022). *Introduction to Algorithms* (4th ed.). Cambridge, MA: MIT Press. (Bab 10: Elementary Data Structures, subbab Linked Lists, termasuk prosedur LIST-SEARCH).
<br>[2] Knuth, D. E. (1998). *The Art of Computer Programming, Volume 3: Sorting and Searching* (2nd ed.). Boston: Addison-Wesley. ISBN 978-0-201-89685-5. (Bab 6: Searching, subbab Sequential Searching).
<br>[3] Weiss, M. A. (2014). *Data Structures and Algorithm Analysis in C++* (4th ed.). Boston: Pearson. (Bab 3: Lists, Stacks, and Queues).
<br>[4] Salim, R. D. (2011). Development a Sequential Search Algorithm by using Triple Structure. *Engineering and Technology Journal*, 29(10), 1886-1892. University of Technology, Baghdad. https://iasj.rdd.edu.iq/journals/uploads/2025/03/19/d675791d7981bf5ac7dde1a59fd2962c.pdf
<br>[5] Sonntag, B., & Colnet, D. (2023). *RIP Linked List: Empirical Study to Discourage You from Using Linked Lists Any Further*. arXiv:2306.06942. https://arxiv.org/abs/2306.06942
