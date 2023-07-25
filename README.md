# Laporan Proyek Machine Learning - Hisyam Athaya

## Project Overview

*E-Learning* didefinisikan sebagai komunikasi sinkron dan asinkron menggunakan teknologi elektronik untuk kebutuhan berfikir dan belajar secara kolaboratif [1].  Pada masa pandemi COVID-19, lebih dari 1.5 milyar siswa dan 63 juta pengajar [2, 3] terpaksa mengalami disrupsi dalam kegiatan belajar mengajar dan memaksa mereka untuk memodifikasi kurikulum dan sistem pengajaran mereka dari tatap muka ke daring [4]. 

Pada studi kasus ini, University of Lille bersama dengan Mandarine Academy mengembangkan suatu *Massive Open Online Course* (MOOC) untuk mengoperasikan [Office 365](https://dileap.com/en/). MOOC memiliki lebih dari 3000 video yang memiliki total 120 jam pembelajaran. Tujuan dari MOOC ini sendiri adalah untuk mempercepat adaptasi pengguna terhadap peralatan Office 365 [5]. 

![Picture](https://raw.githubusercontent.com/athayahisyam/MARS-Recommender/main/media/officemoocMandarine.jpg)

Gambar 1. Portal MOOC Office 365 Mandarine Academy.

Untuk membantu pembelajar melakukan eksplorasi materi dalam MOOC, sistem rekomendasi digunakan. Sistem rekomendasi memprediksikan peringkat atau preferensi pengguna dalam menggunakan suatu produk [6]. Pada sistem *e-Learning*, sistem rekomendasi membantu pembelajar MOOC melakukan eksplorasi terhadap materi pembelajaran dan membantu pembelajar menerima konten sesuai dengan preferensi dan tujuan mereka [7], serta tempo pembelajaran mereka [4].

## Business Understanding

Bagian ini menjelaskan permasalahan yang dihadapi, berdasarkan dataset yang tersedia, dan tujuan yang ingin dicapai pada penyelesaian masalah.

### Problem Statements

Penelitian ini fokus untuk menyelesaikan beberapa permasalahan tertentu, terangkum dalam daftar pertanyaan berikut.

- Apa pendekatan yang digunakan dalam mengembangkan model rekomendasi?
- Bagaimana langkah pengembangan model rekomendasi *course* untuk MOOC dilakukan?

### Goals

Untuk menjawab pertanyaan (masalah) tersebut, projek ini dikembangkan dengan tujuan sebagai berikut: 

- Model rekomendasi dikembangkan menggunakan pendekatan *Content-Based Filtering*.
- Langkah yang dilakukan adalah Analisis Data Univariat, *Data Preprocessing*, *Data Preparation*, dan Pemodelan *Content-Based Filtering*.

Salah satu tujuan dari pengembangan sistem rekomendasi adalah mencegah terjadinya *overload* informasi dengan menyaring dan menyingkirkan sumber pembelajaran yang tidak relevan dan menghasilkan rekomendasi konten yang lebih personal pada pelajar [7].

## Data Understanding

Pada bagian ini akan dijelaskan hasil analisis data univariat dan tahap data *preprocessing*. Dataset yang digunakan adalah dataset dari sistem pembelajaran [Office 365 MOOC](https://dileap.com/en/) yang diselenggarakan oleh Mandarine Academy, University of Lille. Data diterbitkan tahun 2022 [8] dan disimpan pada repositori [Harvard Dataverse](https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/BMY3UD). Data terbagi kedalam empat dataset dalam format *Comma-Separated Value* (CSV), yang disebut *Items*, *Users*, *Implicit Ratings*, dan *Explicit Ratings*. Penjabaran berikut diperoleh dari artikel Hafsa [8].

- *Users*

Tabel ini menampilkan data pengguna pada MOOC Office 365. Tabel 1 menampilkan struktur tabel *Users*.

Tabel 1. Deskripsi Tabel `Users`

| Fitur   | Deskripsi          | Tipe     | `Count` | 
| ------- | ------------------ | -------- | ------- |
| User ID | Identifikasi unik  | Int64    | 9902    |
| Job     | Kategori pekerjaan | Category | 1409    |

Pada tabel 1, dapat dilihat bahwa jumlah pengguna adalah 9902 pengguna. Adapun data pekerjaan tidak sama dengan data pengguna, hal ini disebabkan karena menambahkan pekerjaan pada sistem bukanlah hal yang *mandatory* [8].

- *Items*

Tabel ini menampilkan data konten yang tersimpan pada MOOC Office 365.  Struktur tabel *Items* ditampilkan pada tabel 2. 

Tabel 2. Deskripsi Tabel `Items`

| Fitur         | Deskripsi                                | Tipe     | `Count` |
| ------------- | ---------------------------------------- | -------- | ------- |
| Item ID       | Identifikasi unik                        | Int64    | 1167    |
| Language      | Bahasa                                   | Category | 1167    |
| Title         | Judul konten                             | Text     | 1167    |
| Views         | Jumlah penonton                          | Int64    | 1119    |
| Description   | Deskripsi konten                         | Text     | 1009    |
| Creation Date | Tanggal Unggah konten                    | Date     | 1167    |
| Duration      | Durasi Konten                            | Int64    | 1167    |
| Tipe          | Kategori Konten: *Tutorial*, *Use-case*  | Category | 1167    |
|               | dan *Webcast*                            |          |         |
| Level         | Kategori Level: *Beginner, Intermediate* | Category | 475     |
|               | *Advanced* atau *Undefined*              |          |         |
| Job           | Pekerjaan Terkait                        | Category | 1167    |
| Software      | Software terkait                         | Category | 1167    |
| Theme         | Tema Relevan                             | Category | 1167    |

Pada tabel *Items*, terdapat beberapa fitur yang memiliki *null values*:  *Description*, *Level* dan *View*.

- *Implicit Ratings*

Tabel *implicit ratings* berisi data akses pencarian pengguna pada sistem MOOC. Data bersifat transaksional, dalam artian terdapat data yang duplikatif. Tabel 3 menampilkan struktur tabel *Implicit Ratings*. 

Tabel 3. Tabel *Implicit Rating*

| Feature       | Description       | Type  | Count |
| ------------- | ----------------- | ----- | ----- |
| Item ID       | Identifikasi Unik | Int64 | 21908 |
| User ID       | Identifikasi Unik | Int64 | 21908 |
| Creation Date | Tanggal Event     | Date  | 21908 | 

- *Explicit Ratings*

Serupa dengan tabel *Implicit Rating*, Tabel *explicit rating* bersifat transaksional, yang mencatat berapa kali `user` menonton suatu konten. *Rating* yang dimaksud ialah "selesainya `user` dalam menonton `item`" [8]. Karena merupakan catatan transaksi, tabel ini berisi data duplikatif. Tabel 4 menampilkan struktur tabel *explicit rating*.

Tabel 4. Tabel *Explicit Rating*

| Feature          | Description                      | Type     | `Count` |
| ---------------- | -------------------------------- | -------- | ------- |
| Item ID          | Identifikasi Item                | Int64    | 3659    |
| User ID          | Identifikasi User                | Int64    | 3659    |
| Watch Percentage | Persentasi selesai menonton      | Float    | 3659    |
| Rating           | Kategorisasi persentasi menonton | Category | 3659    |
| Creation Date    | Tanggal event                    | Date     | 3659    |

Setelah dipahami kondisi dataset. Dilakukan preproses dengan membersihkan *null value*.  Preproses utamanya terjadi pada dataset `Items`. 

Pada dataset `Users` preproses yang dilakukan adalah dengan mengabaikan/*drop* fitur pekerjaan. Hal ini dilakukan melihat kondisi yang tidak seimbang antara data yang ada dengan yang tidak ada, dan juga variasi data text yang sudah ada membutuhkan kapabilitas pemrosesan *encoding* yang tidak *feasible*. 

Pada tabel `Items`, preproses yang dilakukan adalah dengan mengisi data *null values* pada data *difficulty* dan *number of views*. Data deskripsi yang memiliki nilai kosong diabaikan/*drop* karena tidak *feasible* untuk diisi. Pada fitur *number of views*, data yang kosong menunjukkan bahwa video belum pernah ditonton, hal yang mungkin terjadi adalah karena video itu baru diunggah atau memang memiliki akses tertutup [8]. Data ini harus tetap diisi untuk mencegah adanya bias pada model. Karena itu, digunakan metode pengisian nilai kosong dengan rata-rata (*Mean-value based imputation*) untuk mengisi nilai kosong ini.

Data fitur *difficulty* diisi dengan algoritma *Random Forest*, yang mampu menangkap hubungan kompleks antara variabel dalam dataset, selain itu RF juga menjaga distribusi data. Pada studi kasus ini, fitur-fitur yang digunakan untuk mengisi kategori yang kosong pada variabel *difficulty* adalah *number of views* dan *duration*. Dengan data *train* adalah data yang memiliki fitur *difficulty* terisi dan data *test* berisi data dengan fitur *difficulty* kosong. Hasil dari imputasi menggunakan algoritma RF ditampilkan pada gambar 2 berikut.

![Random Forest Imputation Result](https://raw.githubusercontent.com/athayahisyam/MARS-Recommender/main/media/randomforest_result.png)

Gambar 2. Hasil Imputasi menggunakan Algoritma Random Forest

Dari hasil yang telah diberikan, dapat dilihat bahwa penambahan yang terjadi lebih banyak pada kategorisasi item sebagai level *beginner*.

Sampai pada tahap ini, preproses data telah dilaksanakan, dan pada subbab berikutnya akan dilakukan *data preparation*.

## Data Preparation

Pada tahap ini, dilakukan *data preparation* dengan (1) melakukan *merging*, lalu *one-hot encoding* pada data kategorikal dan pembuatan *user profile*.

Dikarenakan keterbatasan sumber daya pada Google Colaboratory, hanya beberapa fitur dari dataset yang digunakan untuk membangun model *Content-Based Filtering*. Fitur fitur yang digunakan adalah sebagai berikut.

1. `user_id`
2. `item_id`
3. `rating`
4. `duration`
5. `Difficulty`
6. `type`

Hasil dari *merging* ini ditampilkan pada tabel 5 berikut.

Tabel 5. Hasil *merging* yang didapatkan menggunakan metode `head()`.

| user_id | item_id | rating | duration | Difficulty   | type     |     |
| ------- | ------- | ------ | -------- | ------------ | -------- | --- |
| 224557  | 510     | 10     | 42.0     | Beginner     | tutorial |     |
| 224557  | 615     | 10     | 57.0     | Beginner     | tutorial |     |
| 224557  | 7680    | 10     | 72.0     | Intermediate | tutorial |     |
| 224293  | 510     | 10     | 42.0     | Beginner     | tutorial |     |
| 224293  | 515     | 10     | 87.0     | Beginner     | tutorial |     |

Setelah dilakukan *merging*, data kategorikal diproses menggunakan *one-hot encoding* yang mengubah tiap kategori `Difficulty` dan `type` menjadi fitur tersendiri dan memberikan nilai 0 atau 1 pada data. Metode tersebut dipilih karena data kategori kurang dari lima, sehingga tidak memberikan penambahan yang signifikan pada fitur. Hasil dari proses ini ditampilkan pada tabel 6. 

Tabel 6. Hasil proses *encoding*.

|user_id|item_id|rating|duration|type_tutorial|type_use_case|type_webcast|Difficulty_Advanced|Difficulty_Beginner|Difficulty_Intermediate|
|---|---|---|---|---|---|---|---|---|---|
|224557|510|10|42.0|1|0|0|0|1|0|
|224557|615|10|57.0|1|0|0|0|1|0|
|224557|7680|10|72.0|1|0|0|0|0|1|
|224293|510|10|42.0|1|0|0|0|1|0|
|224293|515|10|87.0|1|0|0|0|1|0|

*Content-based Filtering* menggunakan data *embedding* fitur konten. Pada tahap berikutnya data *embedding* disusun pada variabel *course features*, yang terdiri dari:

1. `item_id`
2. `duration`
3. `Difficulty`
4. `type`

Karena melibatkan dua fitur kategori, pada tahap ini juga dilakukan *one hot encoding*. Hasil dari kompilasi tabel tersebut ditampilkan pada Tabel 7.

Tabel 7. Tahap pembuatan tabel *course features*

| item_id | duration | Difficulty_Advanced | Difficulty_Beginner | Difficulty_Intermediate | type_tutorial | type_use_case | type_webcast |     |
| ------- | -------- | ------------------- | ------------------- | ----------------------- | ------------- | ------------- | ------------ | --- |
| 510     | 0.006719 | 0.0                 | 1.0                 | 0.0                     | 1.0           | 0.0           | 0.0          |     |
| 511     | 0.030091 | 0.0                 | 1.0                 | 0.0                     | 1.0           | 0.0           | 0.0          |     |
| 512     | 0.045866 | 0.0                 | 1.0                 | 0.0                     | 1.0           | 0.0           | 0.0          |     |
| 513     | 0.007888 | 0.0                 | 1.0                 | 0.0                     | 1.0           | 0.0           | 0.0          |     |
| 514     | 0.042068 | 0.0                 | 1.0                 | 0.0                     | 1.0           | 0.0           | 0.0          |     |

Tahap berikutnya adalah membuat *user profile* yang menghubungkan antara data user dengan durasi, tipe konten dan tingkat kesulitan yang diambil. Hasil dari *user profile* ditampilkan pada tabel 8.

Tabel 8. *User Profile*

| user_id | duration | type_tutorial | type_use_case | type_webcast | Difficulty_Advanced | Difficulty_Beginner | Difficulty_Intermediate |     |
| ------- | -------- | ------------- | ------------- | ------------ | ------------------- | ------------------- | ----------------------- | --- |
| 607339  | 238.5    | 1.0           | 0.0           | 0.0          | 1.0                 | 0.0                 | 0.0                     |     |
| 607825  | 203.0    | 0.0           | 1.0           | 0.0          | 0.0                 | 1.0                 | 0.0                     |     |
| 608098  | 80.0     | 1.0           | 0.0           | 0.0          | 0.0                 | 1.0                 | 0.0                     |     |
| 608559  | 102.5    | 1.0           | 0.0           | 0.0          | 0.0                 | 1.0                 | 0.0                     |     |
| 610262  | 150.5    | 0.5           | 0.5           | 0.0          | 0.0                 | 1.0                 | 0.0                     |     |

Sampai pada tahap ini, proses *data preparation* telah selesai, pada subbab berikutnya model dilatih untuk menghasilkan rekomendasi.

## Modeling

Pada tahap ini dilakukan pelatihan model *Content-based Filtering*. Untuk mengukur similaritas, digunakan *cosine similarity*. Data pada data similaritas kemudian digunakan untuk membangun rekomendasi konten yang kemungkinan juga disukai oleh *user* tertentu. 

Kemiripan kosinus (*cosine similarity*) adalah ukuran kesamaan antara dua vektor bukan nol dalam ruang produk dalam. Dalam konteks *content-based filtering*, metode ini mengukur kesamaan antara `user-profiles` (mewakili preferensi pengguna) dan `course-embeddings` (mewakili karakteristik konten).

*Cosine similarity* berkisar dari -1 hingga 1, di mana 1 menunjukkan vektor yang identik (*user-profile* dan *course embedding* memiliki kemiripan yang kuat), 0 menunjukkan vektor bersifat ortogonal (tidak berkorelasi), dan -1 menunjukkan vektor dalam arah yang berlawanan (berbeda secara jauh).

*Cosine similarity* umumnya digunakan dalam sistem rekomendasi dengan *content-based filtering* karena bekerja dengan baik dengan data yang sedikit namun berdimensi tinggi, yang tipikal dalam sistem pembelajaran, dimana data yang tersedia hanya sedikit namun terdiri dari ragam fitur. *Cosine similarity* berfokus pada orientasi vektor ketimbang ukuran besarnya, membuatnya cocok untuk membandingkan representasi berbasis fitur (pada studi kasus ini, `user-profiles` dan `course-embeddings`).

Projek ini menggunakan fungsi `cosine_similarity` dari scikit-learn yang menghitung *cosine similarity* untuk semua pasangan vektor (baris) pada `user-profiles` dan `course-embeddings`.
Hasilnya adalah matriks kesamaan (*similarity matrix*), di mana setiap baris sesuai dengan skor kesamaan pengguna tertentu dengan semua kursus.

Tujuan pembuatan rekomendasi adalah mengidentifikasi N-konten teratas yang memiliki kemiripan yang kuat dengan preferensi pengguna. Rekomendasi yang dipersonalisasi sangat penting untuk memberikan saran konten kepada pengguna yang selaras dengan minat dan preferensi. Dengan menawarkan rekomendasi yang dipersonalisasi, pengalaman pengguna disempurnakan dan kemungkinan keterlibatan dan kepuasan pengguna ditingkatkan.

Untuk ID pengguna tertentu, indeks baris yang sesuai dari DataFrame profil pengguna diambil. Kemudian skor kesamaan dari matriks kesamaan diekstrak untuk pengguna tertentu tersebut. Berdasarkan skor kesamaan, diidentifikasi indeks konten N teratas yang memiliki nilai kesamaan tertinggi dengan preferensi pengguna. Terakhir, indeks konten dipetakan kembali ke ID item (ID konten) dan mengekstrak nama konten untuk memberikan rekomendasi konten yang telah dipersonalisasi.

Dari hasil pelatihan dapat dilihat *Top 10* rekomendasi yang ditampilkan pada tabel 9 berikut ini.

Tabel 9. Hasil *Top 10 Recommendation* 

| ID  | Course Title                                  |
| --- | --------------------------------------------- |
| 197 | How to communicate: who can help me?          |
| 199 | How to search: find what you need             |
| 205 | Office 2016 - new features and enhancements   |
| 249 | Uses by job role: Human resources             |
| 250 | Uses by job role: Assistant(s)                |
| 251 | Uses by job role: Marketing and communication |
| 585 | Teams - Collaborate as a team                 |
| 586 | Teams - Communicate as a team                 |
| 773 | Run effective meetings with Teams             |
| 835 | Discover the note taking app Name: name       |

## Evaluasi

Evaluasi pada sistem rekomendasi dilakukan dengan metrik *precision*. Metrik tersebut digunakan dengan cara membandingkan antara rekomendasi yang diberikan dengan data konten yang diambil oleh pengguna. Semisal dari 10 rekomendasi, ditemukan bahwa 5 dari rekomendasi digunakan oleh pengguna, artinya presisi dari model rekomendasi adalah 50%

Pada studi kasus ini, metrik *precision* dihitung sebagai berikut:

1. Untuk setiap pengguna dalam data *ground truth*:    
    - Ambil daftar item yang telah pengguna berinteraksi dengan atau beri peringkat tinggi (item-item  relevan).
    - Ambil daftar item yang direkomendasikan kepada pengguna oleh sistem rekomendasi.
2. Hitung jumlah item yang relevan dalam rekomendasi. Ini adalah irisan antara himpunan item-item yang relevan dan himpunan item-item yang direkomendasikan.
3. Hitung precision untuk pengguna tersebut:    
    - *Precision* = (Jumlah item yang relevan dalam rekomendasi) / (Total jumlah item yang direkomendasikan)
4. Ulangi langkah 1 hingga 3 untuk semua pengguna dalam data *ground truth*.
5. Hitung rata-rata *precision* dengan menjumlahkan nilai precision untuk semua pengguna dan membaginya dengan jumlah total pengguna.
Dari fungsi perhitungan diatas, nilai *precision* akan berbentuk dalam rentang antara 0 dan 1. *Precision* 1 menunjukkan bahwa semua item yang direkomendasikan relevan bagi pengguna, sementara *precision* 0 berarti tidak ada item yang direkomendasikan yang relevan.

Sebagai contoh pada tabel 10 ditampilkan perbandingan konten yang ditonton dan yang direkomendasikan pada`user 605220`. 

| ID     | Video | Recommendation | Keterangan |
| ------ | ----- | -------------- | ---------- |
| 605220 | 197   | 197            | Match      |
|        | 199   | 199            | Match      |
|        | 251   | 205            | Unmatched  |
|        | 513   | -              | Unmatched  | 

Hasil dari uji presisi menunjukkan model rekomendasi dapat memberikan rekomendasi akurat sebanyak 2/4 atau 50% (0.5555...) dari data pada penggunaan *explicit rating*. Hal ini bisa terjadi karena kurangnya dimensi yang digunakan pada data `users_profile`.

## Kesimpulan

Sampai disini model telah bekerja dan menghasilkan rekomendasi konten untuk pengguna MOOC Office 365. Karena keterbatasn sumber daya pengolahan data, hanya dilakukan pemodelan dengan satu jenis pendekatan. Berdasarkan hasil. Pada penelitian berikutnya beberapa saran hal yang bisa dilakukan adalah:

1. Melakukan pengolahan data deskripsi menggunakan model *Natural Language Processing*.
2. Melakukan eksperimen pada hubungan antara empat tabel lebih lanjut.
3. Melakukan pemodelan rekomendasi dengan pendekatan *Collaborative Filtering*


## References

[1] D. R. Garrison, _E-learning in the 21st century: a community of inquiry framework for research and practice_, Third edition. New York: Routledge, 2017.

[2] “Education: from school closure to recovery | UNESCO.” [https://www.unesco.org/en/covid-19/education-response](https://www.unesco.org/en/covid-19/education-response) (accessed Jul. 20, 2023).

[3] “World Bank Education COVID-19 School Closures Map,” _World Bank_. [https://www.worldbank.org/en/data/interactive/2020/03/24/world-bank-education-and-covid-19](https://www.worldbank.org/en/data/interactive/2020/03/24/world-bank-education-and-covid-19) (accessed Jul. 20, 2023).

[4] J. Valverde-Berrocoso, M. del C. Garrido-Arroyo, C. Burgos-Videla, and M. B. Morales-Cevallos, “Trends in educational research about e-learning: A systematic literature review (2009–2018),” Sustainability, vol. 12, no. 12, p. 5153, Jun. 2020, doi: 10.3390/su12125153

[5] “Discover who we are,” _Mandarine Academy_. https://mandarine.academy/en/about-us/ (accessed Jul. 23, 2023).

[6] F. Ricci, L. Rokach, B. Shapira, and P. B. Kantor, Eds., _Recommender Systems Handbook_. Boston, MA: Springer US, 2011. doi: 10.1007/978-0-387-85820-3.

[7] J. Joy and R. V. G. Pillai, “Review and classification of content recommenders in E-learning environment,” _J. King Saud Univ. - Comput. Inf. Sci._, vol. 34, no. 9, pp. 7670–7685, Oct. 2022, doi: 10.1016/j.jksuci.2021.06.009.

[8] M. Hafsa, P. Wattebled, J. Jacques, and L. Jourdan, “E-learning recommender system dataset,” _Data Brief_, vol. 47, p. 108942, Apr. 2023, doi: 10.1016/j.dib.2023.108942.

**---Ini adalah bagian akhir laporan---**