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

Pada bagian ini akan dijelaskan hasil analisis data univariat dan tahap data *preprocessing*. Dataset yang digunakan adalah dataset dari sistem pembelajaran [Office 365 MOOC](https://dileap.com/en/) yang diselenggarakan oleh Mandarine Academy, University of Lille. Data diterbitkan tahun 2022 [8] dan disimpan pada repositori [Harvard Dataverse](https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/BMY3UD). Data terbagi kedalam empat dataset dalam format *Comma-Separated Value* (CSV), yang disebut *Items*, *Users*, *Implicit Ratings*, dan *Explicit Ratings*. Penjabaran berikut diperoleh dari artikel Hafsa [8]. Karena pada sistem ini akan mengukur kemiripan antar konten atau *content-based filtering* maka tabel yang digunakan hanya tabel `Items`.
abel ini menampilkan data konten yang tersimpan pada MOOC Office 365.  Struktur tabel *Items* ditampilkan pada tabel 2. 

Tabel 2. Deskripsi Tabel `Items`

| Fitur         | Deskripsi                                                             | Tipe     | `Count` |
| ------------- | --------------------------------------------------------------------- | -------- | ------- |
| Item ID       | Identifikasi unik                                                     | Int64    | 1165    |
| Language      | Bahasa                                                                | Category | 1165    |
| Title         | Judul konten                                                          | Text     | 1165    |
| Views         | Jumlah penonton                                                       | Int64    | 1117    |
| Creation Date | Tanggal Unggah konten                                                 | Date     | 1165   |
| Duration      | Durasi Konten                                                         | Int64    | 1165    |
| Tipe          | Kategori Konten: *Tutorial*, *Use-case*, dan *Webcast*                | Category | 1165    |
| Level         | Kategori Level: *Beginner, Intermediate*, *Advanced* atau *Undefined* | Category | 1165     |
| Theme         | Tema Relevan                                                          | Category | 1165    |

Terdapat beberapa fitur yang berisi tipe kategori, adapun persebaran dari item kategori tersebut ialah:

- *Language* (Bahasa)

MOOC ini menggunakan bahasa Inggris sebagai pengantar, maka seluruh item memiliki fitur `en`. Hal ini ditunjukkan pada Gambar 2.

![Distribusi Bahasa](https://raw.githubusercontent.com/athayahisyam/MARS-Recommender/main/media/lang.png)

Gambar 2. Distribusi Bahasa

- *Type* (Tipe)

Fitur ini berisi jenis konten yang disediakan MOOC, terdapat tiga kategori [8] yaitu:

1. *Webcast*: Rekaman layar
2. *Use_case*: Studi Kasus
3. *Tutorial*: Tutorial penggunaan alat.

Pada Dataset, sebaran dari data tipe ditunjukkan pada Gambar 3.

![Distribusi Tipe](https://raw.githubusercontent.com/athayahisyam/MARS-Recommender/main/media/type.png)

Gambar 3. Distribusi Tipe Konten

- *Difficulty* (Tingkat Kesulitan)

MOOC ini memiliki tiga kategori tingkat kesulitan [8] dalam pembelajaran konten:

1. *Beginner*: Konten untuk pengguna *tools* pemula
2. *Intermediate*: Konten untuk yang berpengalaman menggunakan *tools.*
3. *Advanced*: Konten untuk yang telah memiliki pemahaman dan pendalaman terhadap *tools* yang diajarkan.

Sebaran dari tiga kategori tersebut ditunjukkan dalam Gambar 4.

![Distribusi Tingkat Kesulitan](https://raw.githubusercontent.com/athayahisyam/MARS-Recommender/main/media/difficulty.png)

Gambar 4. Sebaran Kategori Tingkat Kesulitan

- *Theme* (Tema)

Pada MOOC, suatu konten dikelompokkan dalam kategori tema, menyesuaikan dengan topik, alat dan tingkat kesulitan suatu konten. Fitur ini memiliki jumlah kategori paling besar, hal ini ditunjukkan pada Gambar 5 berikut.

![Distribusi Tema](https://raw.githubusercontent.com/athayahisyam/MARS-Recommender/main/media/theme.png)

Gambar 5. Distribusi Tema

Pada tabel `Items`, preproses yang dilakukan adalah dengan mengisi data *null values* pada fitur *number of views*. Pada fitur *number of views*, data yang kosong menunjukkan bahwa video belum pernah ditonton, hal yang mungkin terjadi adalah karena video itu baru diunggah atau memang memiliki akses tertutup [8]. Data ini harus tetap diisi untuk mencegah adanya bias pada kesamaan kosinus model. Karena itu, digunakan metode pengisian nilai kosong dengan rata-rata (*Mean-value based imputation*) untuk mengisi nilai kosong ini.

## Data Preparation

Pada tahap ini, dilakukan *data preparation* dengan melakukan *one-hot encoding* pada data kategorikal.

Pada *content-based filtering* fitur yang digunakan untuk mengukur kesamaan (*similarity*) antar *item* pada tabel *Items*. Sesuai dengan deskripsi pada tahap *data understanding*, yang digunakan adalah fitur-fitur pada data *items*. Fitur-fitur tersebut terdiri dari fitur numerik dan fitur kategorikal yang telah melalui tahap *encoding*, sehingga daftar fitur-fitur yang digunakan adalah sebagai berikut. 

1. `nb_views `
2. `duration `
3. `language_en` 
4. `Difficulty_Advanced `
5. `Difficulty_Beginner `
6. `Difficulty_Intermediate` 
7. `Theme_Accessibility` 
8. `Theme_Analysis` 
9. `Theme_Analyze` 
10. `Theme_Collaborate `
11. `Theme_Collaboration` 
12. `Theme_Communicate` 
13. `Theme_Customize` 
14. `Theme_Discover` 
15. `Theme_Mobility` 
16. `Theme_New features` 
17. `Theme_Organize `
18. `Theme_Other Products` 
19. `Theme_Produce` 
20. `Theme_Research` 
21. `Theme_Security` 
22. `Theme_Share` 
23. `Theme_Telephony` 
24. `type_tutorial`
25. `type_use_case` 
26. `type_webcast`

Penampang dari hasil *encoding* Tabel `Items` tersebut dapat dilihat pada Tabel 1. 

Tabel 1. Hasil proses *encoding*.

|nb_views|duration|language_en|Difficulty_Advanced|Difficulty_Beginner|Difficulty_Intermediate|Theme_Accessibility|Theme_Analysis|Theme_Analyze|Theme_Collaborate|...|Theme_Organize|Theme_Other Products|Theme_Produce|Theme_Research|Theme_Security|Theme_Share|Theme_Telephony|type_tutorial|type_use_case|type_webcast|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|1114.0|42|1|0|1|0|0|0|0|0|...|0|0|0|0|0|0|0|1|0|0|
|547.0|122|1|0|1|0|0|0|0|0|...|0|0|0|0|0|0|0|1|0|0|
|607.0|176|1|0|1|0|0|0|0|0|...|0|0|0|0|0|0|0|1|0|0|
|278.0|46|1|0|1|0|0|0|0|0|...|0|0|0|0|0|0|0|1|0|0|
|312.0|163|1|0|1|0|0|0|0|0|...|0|0|0|0|0|0|0|1|0|0|

Sampai pada tahap ini, proses *data preparation* telah selesai, pada subbab berikutnya model dilatih untuk menghasilkan rekomendasi.

## Modeling

Dalam laporan ini, dijelaskan bagaimana variabel `cosine_similarity` digunakan sebagai bagian dari sistem rekomendasi dengan pendekatan *content-based filtering*. Rekomendasi dibuat berdasarkan fitur-fitur yang terdapat pada setiap *item* yang dalam kasus ini adalah konten video.

Langkah pertama dalam proses ini adalah mengubah fitur kategorikal menjadi format numerik. Teknik yang digunakan adalah *one-hot encoding*. Dengan teknik ini, vektor fitur baru untuk setiap item dapat dibentuk. Setiap dimensi dalam vektor tersebut menggambarkan fitur tertentu dari item, seperti `nb_views`, `duration`, `Difficulty`, `Theme`, dan `type`.

Setelah vektor-vektor fitur tersebut terbentuk, `cosine_similarity` dihitung antara setiap pasangan item. `Cosine_similarity` adalah ukuran yang menghitung "kosinus sudut" antara dua vektor. Nilainya berkisar antara -1 dan 1, dengan nilai yang lebih tinggi menunjukkan tingkat kesamaan yang lebih besar antara dua item.

Dengan perhitungan `cosine_similarity`, matriks kesamaan antara *item* dihasilkan. Dalam matriks ini, setiap baris dan kolom mewakili 'item_id', dan setiap sel berisi nilai `cosine_similarity` antara dua item tersebut.

Dengan matriks kesamaan ini, item yang paling mirip dengan item tertentu dapat dicari. Ketika `item_id` dimasukkan ke dalam fungsi `get_similar_items`, fungsi tersebut mencari item lain yang memiliki nilai `cosine_similarity` tertinggi dengan item tersebut. Hasilnya, daftar `item_id` yang paling mirip diperoleh, yang kemudian dapat direkomendasikan berdasarkan `item_id` yang diberikan. 

Sebagai contoh, apabila pengguna menonton konten dengan ID 825 dengan judul *Insert item in a presentation*, maka fungsi rekomendasi akan memberikan rekomendasi video dengan tema (`Theme`) serupa, karena tema adalah salah satu bagian dari matriks pada *consine similarity*. Detail *item* dengan ID 825 pada daftar *Items* ditampilkan pada tabel 2.

Tabel 2. Detail Item ID 825

| item_id | name                           | Theme   |
| ------- | ------------------------------ | ------- |
| 825     | Insert items in a presentation | Produce |

Dari hasil pelatihan berdasarkan kemiripan fitur dari konten dengan ID 825 dengan konten lain dapat dilihat *Top 10* rekomendasi yang ditampilkan pada tabel 3 berikut ini.

Tabel 3. Hasil *Top 10 Recommendation* berdasarkan tema dari konten ID 825

| item_id | name                                       | Theme    |
| ------- | ------------------------------------------ | -------- |
| 741     | The SUM function                           | Produce  |
| 752     | Customize charts                           | Produce  |
| 756     | Three ways to add numbers in Excel Online  | Produce  |
| 784     | Manage conditional formatting              | Produce  |
| 831     | Print Notes Pages as handouts              | Produce  |
| 870     | Create custom animations with motion paths | Produce  |
| 4410    | Introduction                               | Discover |
| 12671   | Insert and customize a footnote            | Produce  |
| 12695   | Start working together in a document       | Produce  |
| 65432   | Functions and formulas                     | Produce  |

## Evaluasi

Evaluasi pada sistem rekomendasi dilakukan dengan metrik *precision*. Metrik tersebut digunakan dengan cara membandingkan antara `Theme` pada item rekomendasi yang diberikan dengan  `Theme` pada *item* yang diinputkan pada fungsi. Semisal dari 10 rekomendasi, ditemukan bahwa 5 *item* pada kategori `Theme` dari rekomendasi sesuai dengan kategori `Theme` pada *item* yang diinputkan, artinya presisi dari model rekomendasi adalah 5/10.

Rumus dari metrik *precision* ini adalah:

P = Jumlah rekomendasi yang relevan/rekomendasi yang diberikan.

Melihat perbandingan antara Tabel 2 dan Tabel 3. Kategori `Theme` pada Item dengan ID 825 adalah `Produce`. Pada hasil rekomendasi, dari 10 item yang diberikan, terdapat 9 item dengan kategori `Theme` yang sama, yaitu `Produce`.

Dari hasil diatas dapat diletakkan kedalam persamaan

P = 9/10 = 0.9

Berdasarkan hasil dari perhitungan presisi, ditemukan bahwa presisi rekomendasi yang diberikan dari kemiripan item dengan ID 825 adalah 9/10 dari rekomendasi yang diberikan. 

## Kesimpulan

Sampai disini model telah bekerja dan menghasilkan rekomendasi konten untuk pengguna MOOC Office 365. Karena keterbatasan sumber daya pengolahan data, hanya dilakukan pemodelan dengan satu jenis pendekatan. Berdasarkan hasil, pada penelitian berikutnya beberapa saran hal yang bisa dilakukan adalah.

1. Melakukan pengolahan data deskripsi menggunakan model *Natural Language Processing*.
2. Melakukan eksperimen pada hubungan antara empat tabel lebih lanjut.
3. Melakukan pemodelan rekomendasi dengan pendekatan *Collaborative Filtering* yang menggabungkan antara data *user* maupun data *rating*. 


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