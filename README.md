# Analisis Kelayakan Pendidikan Indonesia Menggunakan Principal Component Analysis (PCA)

**Mata Kuliah:** Praktikum Pemodelan Statistika  
**Nama:** Iklil Najmi Hamzah  
**NRP:** 3325600003

---

## Deskripsi Proyek

Proyek ini bertujuan untuk menganalisis kelayakan pendidikan di seluruh provinsi Indonesia menggunakan metode **Principal Component Analysis (PCA)**. Data yang digunakan mencakup berbagai indikator pendidikan di tingkat provinsi, mulai dari jumlah sekolah, siswa, tenaga pengajar, hingga kondisi infrastruktur ruang kelas.

PCA digunakan sebagai teknik reduksi dimensi untuk menemukan pola tersembunyi dalam data multivariat yang kompleks, sehingga memudahkan interpretasi dan visualisasi distribusi kualitas pendidikan antar provinsi di Indonesia.

---

## Tujuan Analisis

- Mereduksi dimensi data indikator pendidikan yang berjumlah banyak ke dalam komponen-komponen utama yang merepresentasikan sebagian besar variansi data.
- Mengidentifikasi provinsi-provinsi yang memiliki karakteristik pendidikan serupa maupun yang berbeda signifikan.
- Menguji kelayakan penggunaan PCA pada data ini menggunakan uji **Kaiser-Meyer-Olkin (KMO)**.
- Menginterpretasikan kontribusi setiap variabel terhadap komponen utama melalui **loading matrix**.

---

## Dataset

**File:** `kelayakan-pendidikan-indonesia.csv`

Dataset mencakup **40 provinsi di Indonesia** (termasuk Luar Negeri) dengan 13 variabel indikator pendidikan:

| Variabel | Deskripsi |
|---|---|
| Provinsi | Nama provinsi (digunakan sebagai index) |
| Sekolah | Jumlah sekolah |
| Siswa | Jumlah siswa |
| Mengulang | Jumlah siswa yang mengulang |
| Putus Sekolah | Jumlah siswa yang putus sekolah |
| Kepala Sekolah dan Guru (<S1) | Jumlah kepala sekolah dan guru dengan pendidikan di bawah S1 |
| Kepala Sekolah dan Guru (>= S1) | Jumlah kepala sekolah dan guru dengan pendidikan S1 ke atas |
| Tenaga Kependidikan (SM) | Jumlah tenaga kependidikan setingkat SMA/SMK |
| Tenaga Kependidikan (>SM) | Jumlah tenaga kependidikan di atas SMA/SMK |
| Rombongan Belajar | Jumlah rombongan belajar (kelas aktif) |
| Ruang Kelas (Baik) | Jumlah ruang kelas dalam kondisi baik |
| Ruang Kelas (Rusak Ringan) | Jumlah ruang kelas rusak ringan |
| Ruang Kelas (Rusak Sedang) | Jumlah ruang kelas rusak sedang |
| Ruang Kelas (Rusak Berat) | Jumlah ruang kelas rusak berat |

Untuk keperluan analisis PCA, digunakan 8 variabel positif/kapasitas (X1 hingga X8) yang merepresentasikan kapasitas dan kualitas infrastruktur pendidikan:

| Kode | Variabel Asli |
|---|---|
| X1 | Sekolah |
| X2 | Siswa |
| X3 | Kepala Sekolah dan Guru (<S1) |
| X4 | Kepala Sekolah dan Guru (>= S1) |
| X5 | Tenaga Kependidikan (SM) |
| X6 | Tenaga Kependidikan (>SM) |
| X7 | Rombongan Belajar |
| X8 | Ruang Kelas (Baik) |

---

## Struktur Proyek

```
Proyek/
|
|-- index.ipynb                          # Notebook utama analisis PCA
|-- kelayakan-pendidikan-indonesia.csv   # Dataset pendidikan per provinsi
|-- README.md                            # Dokumentasi proyek ini
|-- 3325600003_Iklil Najmi Hamzah.docx   # Laporan tertulis
|-- 3325600003_Iklil Najmi Hamzah.pdf    # Laporan dalam format PDF
```

---

## Alur Analisis

Analisis dilakukan secara bertahap di dalam notebook `index.ipynb` dengan urutan sebagai berikut:

### 1. Import Library

Library yang digunakan dalam proyek ini:

```python
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score
from sklearn.decomposition import PCA
from sklearn.preprocessing import StandardScaler
from factor_analyzer.factor_analyzer import calculate_kmo
```

### 2. Load Dataset

Data dibaca dari file CSV dengan kolom `Provinsi` sebagai index baris.

### 3. Pemeriksaan Missing Value

Pengecekan nilai yang hilang (`NaN`) pada setiap kolom. Kolom `Unnamed: 14` ditemukan memiliki banyak nilai kosong karena merupakan kolom kosong sisa dari file CSV.

### 4. Penanganan Missing Value

Nilai yang hilang pada kolom numerik diisi menggunakan nilai rata-rata kolom tersebut (`fillna(mean)`). Kolom `Unnamed: 14` yang tidak relevan kemudian dihapus saat pembentukan dataframe baru.

### 5. Pemilihan Variabel untuk PCA

Dibentuk dataframe baru `data_pca` yang hanya berisi 8 variabel yang relevan untuk analisis PCA (variabel kapasitas/positif), menghapus variabel negatif seperti `Mengulang`, `Putus Sekolah`, dan variabel kerusakan ruang kelas.

### 6. Standarisasi Data

Kolom direname menjadi X1-X8 untuk kemudahan notasi, lalu dilakukan standarisasi menggunakan `StandardScaler` agar setiap variabel memiliki mean = 0 dan standar deviasi = 1. Langkah ini penting agar variabel dengan skala besar tidak mendominasi hasil PCA.

### 7. Exploratory Data Analysis (EDA) Pasca Standarisasi

Visualisasi data terstandarisasi menggunakan scatter plot untuk melihat distribusi data sebelum transformasi PCA diterapkan.

### 8. Uji Kelayakan PCA (KMO Test)

Dilakukan uji Kaiser-Meyer-Olkin (KMO) untuk memverifikasi apakah data layak dianalisis menggunakan PCA. Nilai KMO yang diperoleh adalah:

```
KMO Model: 0.778
```

Nilai KMO di atas 0.7 menunjukkan bahwa data **cukup layak** untuk dianalisis dengan PCA (kategori "middling" hingga "meritorious").

### 9. Eksekusi PCA

PCA dijalankan menggunakan `sklearn.decomposition.PCA`. Dihasilkan tabel ringkasan yang menampilkan:

- **Principal Component**: PC1 hingga PC8
- **Eigen Value**: Nilai eigen masing-masing komponen
- **Explained Variance Ratio**: Proporsi variansi yang dijelaskan
- **Cumulative Explained Variance**: Variansi kumulatif

Hasil utama:

| PC | Eigen Value | Explained Variance | Cumulative |
|---|---|---|---|
| PC1 | 7.6038 | 92.61% | 92.61% |
| PC2 | 0.3289 | 4.01% | 96.62% |
| PC3 | 0.1686 | 2.05% | 98.67% |
| PC4 | 0.0538 | 0.66% | 99.32% |
| PC5 | 0.0366 | 0.45% | 99.77% |

**PC1 sendiri mampu menjelaskan 92.61% dari total variansi data**, menunjukkan bahwa satu komponen utama sudah sangat representatif untuk menggambarkan kondisi pendidikan antar provinsi.

### 10. Loading Matrix

Loading matrix menunjukkan kontribusi (korelasi) setiap variabel original terhadap masing-masing principal component. Nilai mendekati 1 atau -1 menandakan kontribusi yang kuat.

### 11. Skor PCA (PCA Scores)

Data diproyeksikan ke ruang komponen utama menggunakan `pca.transform()`. Hasil berupa koordinat setiap provinsi dalam ruang PC baru, yang dapat digunakan untuk analisis lebih lanjut dan visualisasi kluster.

### 12. Visualisasi Scree Plot

Scree plot divisualisasikan untuk menentukan jumlah komponen yang optimal berdasarkan titik "elbow" pada kurva eigen value.

---

## Cara Menjalankan Proyek

### Prasyarat

Pastikan Python sudah terinstal di sistem Anda (disarankan versi 3.8 ke atas). Proyek ini menggunakan virtual environment yang tersimpan di folder `.env`.

### Langkah 1: Clone atau Download Proyek

Unduh atau clone seluruh isi direktori proyek ke komputer Anda.

### Langkah 2: Aktifkan Virtual Environment

Buka terminal atau command prompt di direktori proyek, lalu aktifkan virtual environment:

**Windows (Command Prompt):**
```
.env\Scripts\activate.bat
```

**Windows (PowerShell):**
```
.env\Scripts\Activate.ps1
```

**Linux / macOS:**
```bash
source .env/bin/activate
```

### Langkah 3: Instalasi Dependensi

Jika belum ada virtual environment atau ingin menginstal ulang dependensi, jalankan:

```bash
pip install pandas numpy seaborn matplotlib scikit-learn factor-analyzer jupyter
```

### Langkah 4: Jalankan Jupyter Notebook

Setelah dependensi terinstal, jalankan Jupyter Notebook:

```bash
jupyter notebook
```

Browser akan terbuka secara otomatis. Pilih file `index.ipynb` untuk membuka notebook utama.

### Langkah 5: Jalankan Seluruh Cell

Di dalam Jupyter Notebook, jalankan seluruh cell secara berurutan dari atas ke bawah dengan cara:

- Klik menu **Kernel** > **Restart & Run All**, atau
- Jalankan setiap cell satu per satu menggunakan shortcut **Shift + Enter**

Pastikan file `kelayakan-pendidikan-indonesia.csv` berada di direktori yang sama dengan `index.ipynb` agar data dapat dibaca dengan benar.

---

## Dependensi

| Library | Kegunaan |
|---|---|
| `pandas` | Manipulasi dan pembacaan data tabular |
| `numpy` | Operasi numerik dan array |
| `matplotlib` | Visualisasi data dasar |
| `seaborn` | Visualisasi data statistik |
| `scikit-learn` | StandardScaler dan PCA |
| `factor-analyzer` | Uji KMO (Kaiser-Meyer-Olkin) |
| `jupyter` | Menjalankan notebook interaktif |

---

## Hasil dan Kesimpulan

- Uji KMO menghasilkan nilai **0.778**, yang mengindikasikan data layak untuk dianalisis menggunakan PCA.
- **PC1** berhasil menangkap **92.61%** dari total variansi data, yang berarti satu komponen utama sudah sangat cukup untuk merepresentasikan keseluruhan profil pendidikan suatu provinsi.
- Provinsi dengan populasi dan infrastruktur besar seperti Jawa Barat, Jawa Timur, dan Jawa Tengah memiliki skor PC1 yang jauh lebih tinggi dibandingkan provinsi lainnya.
- Metode PCA terbukti efektif dalam mereduksi 8 dimensi variabel pendidikan menjadi representasi yang jauh lebih sederhana tanpa kehilangan banyak informasi.

---

## Catatan

- Dataset yang digunakan adalah data statis yang mencerminkan kondisi pendidikan pada periode tertentu.
- Analisis ini bersifat deskriptif dan tidak mencerminkan kausalitas antara variabel.
- Untuk interpretasi lebih mendalam, disarankan menggabungkan hasil PCA dengan data sosio-ekonomi atau indeks pembangunan manusia (IPM) per provinsi.
