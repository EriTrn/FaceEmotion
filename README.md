---
### **Penjelasan Isi `README.md`**
1. **Latar Belakang**:
  - Menjelaskan pentingnya klasifikasi emosi wajah dan tantangan dataset RAF-DB, seperti ketidakseimbangan data.
2. **Tujuan**:
  - Merinci tujuan proyek, termasuk target akurasi 95% dan kebutuhan submission.
3. **Model yang Digunakan**:
  - Menjelaskan mengapa MobileNetV2 dipilih (efisiensi, performa, pre-trained weights) dan hasil pelatihan (akurasi rendah, underfitting).
4. **Alur Pengerjaan**:
  - Langkah-langkah terperinci mulai dari persiapan dataset, preprocessing, pelatihan, evaluasi, hingga submission.
5. **Struktur Direktori Submission**:
  - Sesuai dengan ketentuan submission yang Anda berikan sebelumnya.
6. **Cara Menjalankan**:
  - Instruksi sederhana untuk menjalankan proyek.
7. **Tantangan dan Rencana Perbaikan**:
  - Merangkum masalah yang dihadapi (akurasi rendah, fluktuasi, ketidakseimbangan data) dan solusi potensial untuk perbaikan.
---

### **Cara Menyimpan File `README.md`**

Anda bisa menyimpan file ini ke direktori `submission/` menggunakan perintah berikut di Colab:

```python
readme_content = """# RAF-DB Emotion Classification Project

## Latar Belakang
Ekspresi wajah merupakan salah satu cara utama manusia untuk menyampaikan emosi. Dalam era digital, kemampuan untuk mengenali emosi melalui ekspresi wajah secara otomatis memiliki banyak aplikasi, seperti analisis sentimen, interaksi manusia-komputer, dan pengembangan teknologi berbasis kecerdasan buatan (AI). Dataset RAF-DB (Real-world Affective Faces Database) adalah dataset populer yang berisi gambar ekspresi wajah dengan 7 kategori emosi: Surprise, Fear, Disgust, Happiness, Sadness, Anger, dan Neutral. Dataset ini memiliki tantangan tersendiri karena ketidakseimbangan data antar kelas, misalnya, kelas Happiness memiliki jumlah data yang jauh lebih banyak (5957 gambar) dibandingkan kelas Fear (355 gambar).

Proyek ini dibuat sebagai bagian dari tugas atau kompetisi untuk mengembangkan model machine learning yang dapat mengklasifikasikan emosi wajah dengan akurasi tinggi, dengan target akurasi sebesar 95%. Namun, tantangan seperti ketidakseimbangan data, overfitting, dan underfitting menjadi fokus utama dalam pengembangan model.

## Tujuan
Tujuan utama proyek ini adalah:
1. Membangun model deep learning untuk mengklasifikasikan 7 kategori emosi wajah pada dataset RAF-DB.
2. Mencapai akurasi validasi sebesar 95% atau lebih.
3. Mengatasi tantangan ketidakseimbangan data dan meningkatkan generalisasi model pada data yang belum pernah dilihat (data validasi dan test).
4. Menyusun submission proyek sesuai ketentuan, termasuk menyimpan model dalam format `savedmodel`, `TJS`, dan `TF-Lite`.

## Model yang Digunakan
Proyek ini menggunakan **MobileNetV2** sebagai model utama untuk klasifikasi emosi wajah. MobileNetV2 dipilih karena beberapa alasan:
- **Efisiensi**: MobileNetV2 adalah arsitektur yang ringan dan dioptimalkan untuk perangkat dengan sumber daya terbatas, seperti perangkat mobile, sehingga cocok untuk deployment.
- **Performa**: Meskipun ringan, MobileNetV2 memiliki performa yang baik untuk tugas computer vision, termasuk klasifikasi gambar, berkat arsitektur berbasis depthwise separable convolutions.
- **Pre-trained Weights**: Model ini menggunakan bobot pre-trained pada dataset ImageNet, yang memungkinkan transfer learning untuk menangkap fitur gambar dengan lebih baik.

Meskipun hasil pelatihan menunjukkan akurasi yang tinggi (training: ~0.98, validation: ~0.85 setelah 50 epoch), ada gap antara training dan validasi ini mengindikasikan adanya overfitting. Hal ini menjadi fokus untuk perbaikan di masa depan.

## Alur Pengerjaan
Berikut adalah langkah-langkah yang dilakukan dalam proyek ini:

1. **Persiapan Dataset**:
   - Dataset RAF-DB (`shuvoalok/raf-db-dataset`) diunduh dari Kaggle menggunakan Kaggle CLI.
   - Dataset diekstrak ke direktori `raf_db_extracted/`, yang memiliki struktur:

raf_db_extracted/
├── DATASET/
│   ├── train/
│   │   ├── 1/  # Surprise
│   │   ├── 2/  # Fear
│   │   └── ...
│   └── test/
│       ├── 1/
│       ├── 2/
│       └── ...
├── test_labels.csv
└── train_labels.csv

- Ketidakseimbangan data diidentifikasi (misalnya, Happiness: 5957, Feart: 355). Untuk mengatasinya:
- Augmentasi data dilakukan dengan menambahkan gambar per kelas (kecuali Happiness).
- Data dibagi secara stratified menggunakan `sklearn.model_selection.train_test_split` untuk memastikan proporsi kelas seimbang di data training, validasi, dan test.

2. **Preprocessing Data**:
- Gambar dimuat menggunakan `ImageDataGenerator` dari TensorFlow dengan normalisasi (`rescale=1./255.`).
- Data training dibagi menjadi 80% training dan 20% validasi menggunakan `validation_split=0.2`.
- Data test dimuat secara terpisah dari direktori `DATASET/test/`.

3. **Pemilihan dan Pelatihan Model**:
- Model MobileNetV2 dengan bobot pre-trained dari ImageNet digunakan sebagai base model.
- Layer tambahan ditambahkan untuk klasifikasi 7 kelas:
- Conv2D layer dengan 512 unit untuk mengekstrak fitur.
- Global Average Pooling untuk mereduksi dimensi.
- Dense layer dengan 256 unit dan aktivasi ReLU.
- Dropout (0.2) dan (0,1) untuk mencegah overfitting.
- Output layer dengan 7 unit dan aktivasi softmax.
- Model dilatih selama 50 epoch, dengan hasil:
- Training accuracy: ~0.98
- Validation accuracy: ~0.85
- Grafik akurasi menunjukkan overfitting (akurasi tinggi namun ada fluktuasi kecil pada akurasi validasi).

4. **Evaluasi dan Analisis**:
- Akurasi validasi (~0.85) tidak mencapai target 95%, tapi masih tergolong tinggi.
- Fluktuasi kecil pada akurasi validasi menunjukkan pelatihan tidak stabil.
- Ketidakseimbangan data masih memengaruhi performa, meskipun telah dilakukan augmentasi dan stratifikasi.

5. **Submission**:
- Model disimpan dalam tiga format:
- `savedmodel`: Untuk format TensorFlow SavedModel.
- `TJS`: Untuk TensorFlow.js.
- `TF-Lite`: Untuk deployment di perangkat mobile.
- File `label.txt` dibuat dengan daftar kelas: Surprise, Fear, Disgust, Happiness, Sadness, Anger, Neutral.
- File `requirements.txt` dibuat untuk mencatat dependensi.
- Notebook (`notebook.ipynb`) disertakan dengan semua output pelatihan.
- Semua file dikemas dalam folder `submission/` dan di-zip menjadi `submission.zip`.

## Struktur Direktori Submission

submission/
├── tfjs_model/
│   ├── group1-shard1of10.bin
│   ├── group1-shard2of10.bin
│   ├── group1-shard3of10.bin
│   ├── group1-shard4of10.bin
│   ├── group1-shard5of10.bin
│   ├── group1-shard6of10.bin
│   ├── group1-shard7of10.bin
│   ├── group1-shard8of10.bin
│   ├── group1-shard9of10.bin
│   ├── group1-shard10of10.bin
│   └── model.json
├── tflite/
│   └── model.tflite
├── saved_model/
│   ├── saved_model.pb
│   └── variables/
│       ├── variables.data-00000-of-00001
│       └── variables.index
├── label.txt
├── notebook.ipynb
├── requirements.txt
└── README.md
```
