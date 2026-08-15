# opaque

q:
apa itu Tipe Opaque

<br>

a-ai:
**Tipe Opaque** (_opaque data type_ atau jenis data buram) adalah **tipe data dalam ilmu komputer di mana detail struktur internal dan cara kerjanya disembunyikan dari pengguna atau pemanggil antarmuka (interface)**.

Pemakai tipe data ini hanya tahu nama dan fungsi yang bisa dilakukan, tetapi tidak bisa melihat atau mengubah isi bagian dalamnya secara langsung. Sifat ini berlawanan dengan tipe data transparan yang memperlihatkan struktur aslinya.

Fungsi utama dan karakteristik dari tipe opaque:
- **Penyembunyian Informasi (_Information Hiding_)**: Mencegah kode luar bergantung pada detail struktur internal yang bisa berubah sewaktu-waktu.
- **Keamanan Kode**: Memastikan data hanya bisa diakses atau diubah melalui fungsi resmi (subroutine) yang telah disediakan oleh pembuatnya.
- **Kemudahan Pemeliharaan (_Maintenance_)**: Memungkinkan pengembang memperbarui struktur atau isi pustaka (_library_) tanpa merusak kode program lain yang memakai tipe tersebut.
- **Penerapan Bahasa Pemrograman**: Sering ditemukan pada bahasa seperti C (untuk _pointer_ struktur tersembunyi), Swift (menggunakan kata kunci `some` untuk _opaque result types_), atau Scala.

<br>

q-ai:
Jika Anda sedang mempelajari bahasa pemrograman tertentu, beri tahu saya **bahasa apa yang sedang Anda gunakan (seperti C, Swift, atau TypeScript)** agar saya bisa memberikan **contoh kode penggunaan tipe opaque yang spesifik**.

<br>

q:
Saya sedang mempelajari bahasa pemrograman C

<br>

a-ai:
Dalam bahasa pemrograman C, **Tipe Opaque** (Opaque Data Type) diimplementasikan menggunakan **pointer ke struktur yang belum didefinisikan secara lengkap** (_pointer to an incomplete struct_).

Artinya, struktur data dideklarasikan di file _header_ (`.h`), tetapi isi variabel di dalamnya disembunyikan dan baru didefinisikan di dalam file implementasi (`.c`).

Cara Kerja dan Contoh Kode

Berikut adalah contoh penerapan tipe opaque untuk membuat objek `Dompet` yang aman. Kode luar hanya bisa menggunakan fungsi yang disediakan tanpa bisa memanipulasi saldo secara ilegal.

1. **File Antarmuka: `dompet.h`**
File ini dibagikan kepada pengguna kode. Pengguna hanya tahu ada tipe data bernama Dompet, tetapi tidak tahu variabel apa saja di dalamnya.
```c
#ifndef DOMPET_H
#define DOMPET_H

// Deklarasi tipe opaque (struktur belum lengkap)
typedef struct Dompet Dompet;

// Fungsi-fungsi resmi untuk berinteraksi dengan tipe opaque
Dompet* buat_dompet(int saldo_awal);
void tambah_saldo(Dompet* d, int jumlah);
int cek_saldo(Dompet* d);
void hancurkan_dompet(Dompet* d);

#endif

```

2. **File Implementasi: `dompet.c`**
File ini disembunyikan atau dikompilasi menjadi pustaka (_library_). Di sinilah isi struktur `Dompet` sebenarnya ditentukan.
```c
#include <stdlib.h>
#include "dompet.h"

// Struktur data baru didefinisikan secara lengkap di sini
struct Dompet {
    int saldo; 
    // Anda bisa menambah variabel lain di sini tanpa merusak kode pengguna
};

Dompet* buat_dompet(int saldo_awal) {
    Dompet* d = malloc(sizeof(struct Dompet));
    if (d != NULL) {
        d->saldo = saldo_awal;
    }
    return d;
}

void tambah_saldo(Dompet* d, int jumlah) {
    if (d != NULL && jumlah > 0) {
        d->saldo += jumlah;
    }
}

int cek_saldo(Dompet* d) {
    if (d != NULL) {
        return d->saldo;
    }
    return 0;
}

void hancurkan_dompet(Dompet* d) {
    free(d);
}

```

3. **File Pengguna: `main.c`**
File ini adalah kode yang ditulis oleh pengguna pustaka Anda.
```c
#include <stdio.h>
#include "dompet.h"

int main() {
    // Membuat objek menggunakan fungsi resmi
    Dompet* dompet_saya = buat_dompet(50000);

    // Menggunakan fungsi resmi (Berhasil)
    tambah_saldo(dompet_saya, 25000);
    printf("Saldo: %d\n", cek_saldo(dompet_saya)); // Output: 75000

    // ERROR JIKA DILAKUKAN:
    // dompet_saya->saldo = 1000000; 
    // Kode di atas akan menghasilkan "compile error" karena compiler tidak tahu isi dari struct Dompet

    hancurkan_dompet(dompet_saya);
    return 0;
}

```

<br>

q-ai:
Mengapa Ini Sangat Penting di Bahasa C?
- **Aman dari Modifikasi Ilegal**: Pengguna tidak bisa mengubah nilai `saldo` secara langsung tanpa lewat fungsi `tambah_saldo`.
- **Contoh Nyata di Standard Library C**: Tipe data `FILE*` yang sering Anda gunakan untuk membaca dokumen (`fopen`, `fprintf`) adalah sebuah tipe opaque. Anda tidak pernah tahu isi struktur internal `FILE`, Anda hanya memakai pointer-nya.

<br>

q-ai:
Jika Anda ingin mencoba mempraktikkannya, beri tahu saya **apakah Anda sudah paham cara kompilasi multi-file di C** atau **butuh bantuan untuk membuat _Makefile_ / perintah gcc** untuk menjalankan kode di atas.

<br>

q:
butuh bantuan untuk membuat Makefile / perintah gcc untuk menjalankan kode di atas

a-ai:
Untuk menjalankan kode multi-file di atas, Anda harus mengompilasi file `dompet.c` dan `main.c` secara bersamaan agar `compiler` bisa menggabungkan fungsinya.

Berikut adalah dua cara yang bisa Anda gunakan: menggunakan perintah GCC langsung di terminal, atau menggunakan file otomatisasi `Makefile`.

**Cara 1: Menggunakan Perintah GCC Langsung**
Buka terminal atau _command prompt_ di dalam folder tempat Anda menyimpan ketiga file di atas (`dompet.h`, `dompet.c`, `main.c`), lalu jalankan perintah berikut:
```c
gcc main.c dompet.c -o program_dompet
```
Penjelasan Perintah:
- `gcc`: Memanggil _compiler_ GCC.
- `main.c dompet.c`: File-file sumber C yang ingin dikompilasi.
- `-o program_dompet`: Menentukan nama file hasil kompilasi (output) menjadi `program_dompet` (atau `program_dompet.exe` jika di Windows).
Cara Menjalankannya:
- Linux / macOS: `./program_dompet`
- Windows: `program_dompet.exe`

**Cara 2: Menggunakan Makefile (Lebih Otomatis)**
Jika proyek Anda semakin besar, menggunakan `Makefile` jauh lebih efisien karena GCC hanya akan mengompilasi file yang baru saja diubah.

Buat sebuah file baru bernama **`Makefile`** (tanpa ekstensi file apa pun seperti .txt) di folder yang sama, lalu masukkan kode berikut:
```c
# Menentukan compiler yang digunakan
CC = gcc

# Menentukan flag compiler (opsional, untuk menampilkan semua peringatan/warning)
CFLAGS = -Wall -Wextra -std=c99

# Nama file hasil akhir (output)
TARGET = program_dompet

# Aturan utama untuk membuat program
all: $(TARGET)

$(TARGET): main.o dompet.o
	$(CC) $(CFLAGS) main.o dompet.o -o $(TARGET)

main.o: main.c dompet.h
	$(CC) $(CFLAGS) -c main.c

dompet.o: dompet.c dompet.h
	$(CC) $(CFLAGS) -c dompet.c

# Aturan untuk membersihkan file hasil kompilasi
clean:
	rm -f *.o $(TARGET)

```
⚠️ **PENTING DI MAKEFILE**: Pastikan setiap baris yang menjorok ke dalam setelah deklarasi aturan (seperti di bawah $(TARGET):, main.o:, dst) menggunakan **tombol TAB**, bukan spasi. Jika menggunakan spasi, make akan menghasilkan eror.

Cara Menggunakannya:
1. Ketik `make` di terminal untuk mengompilasi program.
2. Jalankan program dengan `./program_dompet` (atau `program_dompet.exe`).
3. Ketik `make clean` jika Anda ingin menghapus file `.o` dan file eksekusi untuk membersihkan folder.

<br>

q-ai:
Jika Anda mengalami kendala saat mencoba, beri tahu saya sistem operasi apa yang Anda gunakan (Windows, Linux, atau macOS) dan pesan eror apa yang muncul di terminal Anda.

<br>
