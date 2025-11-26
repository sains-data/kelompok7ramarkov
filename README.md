# 📊 Pemodelan Stokastik Perpindahan Aktivitas Mahasiswa TPB ITERA Menggunakan Rantai Markov
Tugas Besar Pemodelan Stokastik – Kelompok 7 RA
Program Studi Sains Data – Institut Teknologi Sumatera (ITERA)

---

## 🎯 Tentang Proyek

Proyek ini menganalisis pola perpindahan aktivitas mahasiswa Tahap Persiapan Bersama (TPB) ITERA menggunakan model **Rantai Markov (Markov Chain)**. Tujuannya adalah untuk memahami bagaimana mahasiswa bergerak antar lokasi kegiatan akademik dan non-akademik yang telah didefinisikan sebagai *state* (keadaan):

* `Gedung E`
* `GKU 1`
* `Kantin`
* `Labtek`
* `Perpustakaan`
* *State* **`Kosong`** (merepresentasikan waktu jeda, istirahat, atau perpindahan)

Model stokastik ini digunakan untuk:
✔ Memahami probabilitas perpindahan antar aktivitas (Matriks Transisi)
✔ Menentukan distribusi jangka panjang (**Steady State**)
✔ Menganalisis struktur ruang keadaan (**Transient/Recurrent**)
✔ Menghasilkan simulasi lintasan aktivitas mahasiswa

---

## 🧩 Mengapa Proyek Ini Penting?

* Membantu kampus memahami dinamika mobilitas mahasiswa TPB.
* Mendukung perencanaan fasilitas kampus (alokasi ruang kelas, lab, kantin, perpustakaan).
* Memberikan gambaran *data-driven* tentang perilaku mahasiswa TPB.
* Menjadi contoh nyata penerapan **Markov Chain** dalam analisis perilaku di dunia pendidikan.

---

## 📈 Ringkasan Temuan Utama

### 1. Frekuensi Kunjungan

| State | Frekuensi |
| :--- | :--- |
| Kosong | 1211 |
| GKU 1 | 940 |
| Labtek | 313 |
| Kantin | 98 |
| Perpustakaan | 83 |
| Gedung E | 50 |

> ➡️ **Kesimpulan awal:** `GKU 1` dan `Kosong` adalah *state* yang paling dominan dikunjungi oleh mahasiswa TPB.

### 2. Hasil Analisis Rantai Markov

* **Probabilitas Self-Transition:** Tertinggi pada **Kosong (0.799)** dan **GKU 1 (0.749)**, menunjukkan kecenderungan mahasiswa untuk tetap berada di lokasi yang sama selama beberapa slot waktu.
* **Pola Transisi Umum:** Perpindahan yang paling sering terjadi ialah dari aktivitas akademik (`GKU 1`, `Labtek`) menuju *state* **`Kosong`**.
* **Probabilitas Langkah ke-n (P³):** Pada langkah ke-3, probabilitas menuju `Kosong` semakin besar, mengindikasikan mahasiswa cenderung sudah berpindah ke masa jeda atau transisi setelah beberapa jam.

### 3. Distribusi Stasioner (Steady State)

| State | Proporsi Jangka Panjang (π) |
| :--- | :--- |
| **Kosong** | **0.437** |
| **GKU 1** | **0.359** |
| Labtek | 0.117 |
| Kantin | 0.038 |
| Perpustakaan | 0.032 |
| Gedung E | 0.018 |

> 🔥 **Interpretasi Kunci:** Mahasiswa menghabiskan sekitar **44%** waktunya dalam kondisi jeda/transisi, dan **36%** di GKU 1 (pusat kegiatan perkuliahan).

### 4. Klasifikasi State

* Semua *state* diklasifikasikan sebagai **Transient** atau **Recurrent**.
* **Tidak ada *absorbing state***, yang menunjukkan bahwa sistem perpindahan mahasiswa bersifat **dinamis** (mobilitas mahasiswa tinggi) dan selalu berpindah mengikuti jadwal.

---

## 🛠️ Metodologi

| Kategori | Detail |
| :--- | :--- |
| **Teknik Pengumpulan Data** | Survei *online* (Google Form). |
| **Responden** | 50 mahasiswa TPB ITERA. |
| **Periode Pengamatan** | Aktivitas dicatat setiap 1 jam (07.00 – 17.00) dari Senin hingga Jumat. |
| **Perangkat Lunak** | Analisis dilakukan menggunakan **R-Studio**. |

### Tahapan Analisis:

1.  Menentukan ruang keadaan (State Space).
2.  Menyusun Matriks Frekuensi Perpindahan.
3.  Menghitung Matriks Probabilitas Transisi (P).
4.  Menganalisis Graf Transisi dan *Heatmap*.
5.  Menghitung $P^n$ dan Distribusi Stasioner ($\pi$).
6.  Klasifikasi *State* (Transient/Recurrent).

---


## 👥 Tim Proyek – Kelompok 7 RA

| Nama | NIM |
| :--- | :--- |
| Nathanael Daniel Santoso | 122450059 |
| Kharisa Harvanny | 122450061 |
| Tria Yunanni | 122450062 |
| Nabila Anilda Zahrah | 122450063 |

---
*Terima kasih telah mengunjungi repository ini!*

