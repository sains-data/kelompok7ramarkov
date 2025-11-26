# 📊 Pemodelan Stokastik Perpindahan Aktivitas Mahasiswa TPB ITERA Menggunakan Rantai Markov

## Deskripsi Proyek

[cite_start]Proyek ini memodelkan pola perpindahan aktivitas mahasiswa Tahap Persiapan Bersama (TPB) Institut Teknologi Sumatera (ITERA) menggunakan **Rantai Markov (Markov Chain)**[cite: 266].
[cite_start]Penelitian ini bertujuan menganalisis dinamika transisi antar lokasi aktivitas (state) secara probabilistik untuk mendapatkan gambaran kuantitatif mengenai mobilitas mahasiswa TPB[cite: 266, 283].

## 🔑 Kata Kunci
[cite_start]Rantai Markov, Pemodelan Stokastik, Perpindahan Aktivitas, TPB ITERA, Probabilitas Transisi, Steady State[cite: 273].

## 🧪 Metode dan Data

### 1. Data
* [cite_start]**Jenis Data:** Data primer hasil survei *online* (Google Form) dari responden mahasiswa TPB ITERA[cite: 385].
* **Waktu Pengamatan:** Aktivitas harian dari pukul 07.00 s.d. [cite_start]17.00, hari Senin hingga Jumat[cite: 386].

### 2. Ruang Keadaan (State Space)
[cite_start]Aktivitas mahasiswa dikelompokkan menjadi 6 *state* yang merepresentasikan lokasi kegiatan[cite: 394]:
* `Gedung E`
* `GKU 1`
* `Kantin`
* `Kosong` (Kondisi jeda atau transisi antar aktivitas)
* `Labtek`
* `Perpustakaan`

### 3. Analisis
[cite_start]Pemodelan dilakukan menggunakan Rantai Markov di **R-Studio** [cite: 401] [cite_start]untuk menghitung Matriks Probabilitas Transisi, Probabilitas Langkah ke-n, dan Distribusi Stasioner[cite: 402].

## 💡 Hasil Utama (Distribusi Stasioner)

[cite_start]Distribusi stasioner ($\pi$) menunjukkan proporsi waktu yang dihabiskan mahasiswa di setiap *state* dalam jangka panjang[cite: 464].

| State | Proporsi Jangka Panjang ($\pi$) | Frekuensi Kunjungan |
| :---: | :---: | :---: |
| **Kosong** | 43.7% | [cite_start]1211 [cite: 416] |
| **GKU 1** | 35.9% | [cite_start]940 [cite: 416] |
| **Labtek** | 11.7% | [cite_start]313 [cite: 416] |
| **Kantin** | 3.8% | [cite_start]98 [cite: 416] |
| **Perpustakaan**| 3.2% | [cite_start]83 [cite: 416] |
| **Gedung E** | 1.8% | [cite_start]50 [cite: 416] |

**Interpretasi Kunci:**
* [cite_start]Aktivitas mahasiswa didominasi oleh kondisi **Kosong** dan kegiatan perkuliahan di **GKU 1**[cite: 485].
* [cite_start]Model ini bersifat dinamis karena tidak memiliki *state absorbing* (mahasiswa selalu memiliki peluang berpindah)[cite: 475, 488].

## 🧑‍💻 Anggota Tim

| Nama | NIM |
| :--- | :--- |
| NATHANAEL DANIEL SANTOSO | 122450059 |
| **KHARISA HARVANNY** | **122450061** |
| TRIA YUNANNI | 122450062 |
| NABILA ANILDA ZAHRAH | 122450063 |

## 📁 Struktur Repository
