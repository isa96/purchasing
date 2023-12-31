# Online Purchasing 


## Table of Contents
* [Exploratory Data Analysis](#Stage-1-Exploratory-Data-Analysis)
    * [Data Exploration](#11-Data-Exploration)
    * [Data Understanding](#12-Data-Understanding)
    * [Exploratory Data Analysis](#13-Exploratory-Data-Analysis)    
* [Data Preprocessing](#Stage-2-Data-Preprocessing)
    * [Data Cleansing](#21-Data-Cleansing)
    * [Feature Engineering](#22-Feature-Engineering)

## Project Organization
```
.
├── Dataset/                              : contains csv file
│   ├── about.txt                         : about dataset
│   ├── online_shoppers_intention.csv     : Online Shoppers Purchasing Intention dataset
│   ├── test.csv                          : test set
│   └── train.csv                         : train set
├── Notebooks/                            : contains ipynb file
│   ├── 1_Exploratory_Data_Analysis.ipynb : Data analytics process to understand the data in depth
│   ├── 2_Data_Preprocessing.ipynb        : Data preprocessing for machine learning (ML)
│   └── 3_Modeling.ipynb                  : Random Forest model to predict the customers purchase intention
├── LICENSE                               : MIT License
├── README.md                             : Report
└── requirements.txt                      : requirements to run this model
```

## Stage 1. Exploratory Data Analysis

### 1.1. Data Exploration
Dataset [Online Shoppers Purchasing Intention](https://archive.ics.uci.edu/ml/datasets/Online+Shoppers+Purchasing+Intention+Dataset/) merupakan dataset yang dibentuk secara khusus, sehingga setiap sesi akan dimiliki oleh pelanggan yang berbeda selama periode 1 tahun. Dataset ini terdiri dari 12.330 baris dan 18 kolom fitur, setiap baris berisi data yang berkaitan dengan sesi kunjungan (waktu yang dihabiskan) pelanggan pada website e-commerce.

### 1.2. Data Understanding
#### 1.2.0. Features Definition
##### 1.2.0.1. Numerical Features
| Feature Name              | Feature Description                                                    |
|:--------------------------|:-----------------------------------------------------------------------|
| `Administrative`          | Number of times the visitor visited the administrative section         |
| `Administrative_Duration` | Total time the user spent in the administrative section                |
| `Informational`           | Number of times the visitor visited the informational section          |
| `Informational_Duration`  | Total time the user spent in the informational section                 |
| `ProductRelated`          | Number of times the visitor visited the related products section       |
| `ProductRelated_Duration` | Total time the user spent in the related products section              |
| `BounceRates`             | Average bounce rate value of the pages visited by the visitor          |
| `ExitRates`	              | Average exit rate value of the pages visited by the visitor            |
| `PageValues`              | Average page value of the pages visited by the visitor                 |
| `SpecialDay`              | The proximity to a special date                                        |

Notes:
- For all pageviews to the page, [Exit Rate](https://support.google.com/analytics/answer/2525491?hl=en&ref_topic=6156780) is the percentage that were the last in the session.
- For all sessions that start with the page, [Bounce Rate](https://support.google.com/analytics/answer/2525491?hl=en&ref_topic=6156780) is the percentage that were the only one of the session.
- [Page Value](https://support.google.com/analytics/answer/2695658?hl=en&ref_topic=6156780) is the average value for a page that a visitor visited before completing an Ecommerce transaction.

##### 1.2.0.2. Categorical Features
| Feature Name              | Feature Description                                                    |
|:--------------------------|:-----------------------------------------------------------------------|
| `Month`                   | Month of the visit to the website                                      |
| `OperatingSystems`        | Type of operating system                                               |
| `Browser`                 | Name of the web browser                                                |
| `Region`                  | Visitor's geographic region                                            |
| `TrafficType`             | Type of the web traffic                                                |
| `VisitorType`             | Type of the visitor as "New Visitor", "Returning Visitor", and "Other" |
| `Weekend`                 | "True" indicates that it is a weekend day                              |

##### 1.2.0.3. Target Feature
| Feature Name              | Feature Description                                                    |
|:--------------------------|:-----------------------------------------------------------------------|
| `Revenue`                 | "True" indicates that the visitor has purchased                        |

#### 1.2.1. Data Dimension
Dataset ini memiliki dimensi data, yaitu
- Jumlah baris: 12.330
- Jumlah kolom: 18 
#### 1.2.2. Data Types and Structure
Untuk mendapatkan ringkasan singkat tentang dataset, kami menggunakan fungsi `info()`. Hasil observasi yang didapatkan adalah sebagai berikut.
- Dari total 18 kolom, ada 4 kolom atau fitur dengan tipe data yang kurang sesuai, yaitu `OperatingSystems`, `Browser`, `Region`, dan `TrafficType`, seharusnya string bukan integer, karena kemungkinan sudah melalui proses _label encoding_, sedangkan kolom lainnya sudah sesuai.
- Tidak ada kolom yang memiliki nilai kosong atau _missing values_.
- Tipe data berupa boolean (2), float (7), integer (7), dan string (2).
#### 1.2.3. Detect Missing Values
Untuk memastikan adanya _missing values_ dalam dataset, kita menggunakan metode `isna()`.
- Tidak ada kolom yang _null_ (bernilai None ataupun NaN).
#### 1.2.4. Detect Duplicates
Untuk menemukan adanya _duplicates_, kita menggunakan metode `duplicated()`. Ternyata ditemukan data duplikat sebanyak 127 baris. Walaupun demikian, kita berasumsi bahwa data tersebut merupakan data unik, yang terkait dengan sesi kunjungan pelanggan.
#### 1.2.5. Unique Elements
Untuk mencari elemen unik dalam dataset, kita menggunakan fungsi `nunique()`.
- Fitur `Administrative_Duration`, `Informational_Duration`, dan `ProductRelated_Duration` memiliki elemen unik atau kategori yang cukup banyak, sehingga kita bisa melakukan
_feature selection/dimensionality reduction_ atau kita bisa
buat fitur baru `TotalPage_Duration` atau
`AvgPage_Duration`.
- Fitur `Administrative`, `Informational`, dan `ProductRelated` juga bisa buat fitur baru `TotalPage` atau
`AvgPage`.
- Fitur `BounceRates`, `ExitRates`, dan `PageValues` akan dipertahankan.

### 1.3. Descriptive Statistics
Untuk mendapatkan perincian statistik dasar dari dataset, kita menggunakan metode `describe()`.
#### 1.3.1. Numerical Features
Dalam fitur numerikal, ada perbedaan yang signifikan antara mean dan median (P50), yaitu nilai mean > median, karena kemungkinan dipengaruhi oleh adanya _outlier_ atau pencilan, sehingga distribusi data akan cenderung menceng ke kanan atau _positively skewed_.
#### 1.3.2. Categorical Features
Berikut ini nilai yang paling umum dalam fitur kategorikal, berturut-turut adalah:
- `Month` : May (27,3%),
- `OperatingSystems` : 2 (53,5%),
- `Browser` : 2 (64,6%),
- `Region` : 1 (38,8%),
- `TrafficType` : 2 (31,7%),
- `VisitorType` : Returning_Visitor (85,6%),
- `Weekend` : 0 atau False (76,7%), dan
- `Revenue` : 0 atau False (84,5%).

Dalam fitur kategorikal, ada 5 dari 8 fitur memiliki kategori yang sangat mendominasi (dengan proporsi > 50%).

#### 1.3.3. Target Feature
Fitur `Revenue` digunakan sebagai _target feature_ atau label kelas.
- Dari total 12.330 sesi, 84,5% atau 10.422 sesi merupakan **kelas negatif** yang tidak diakhiri dengan pembelian, sedangkan 15,5% sisanya atau 1.908 sesi merupakan **kelas positif** yang diakhiri dengan pembelian.
- Dataset _imbalance_ atau tidak seimbang, karena proporsi data minoritas (dalam hal ini kelas positif) relatif rendah, dengan _degree of imbalance_: [moderate](https://developers.google.com/machine-learning/data-prep/construct/sampling-splitting/imbalanced-data/).
- Pada saat data pre-processing, kita perlu melakukan _handling imbalance data_, seperti
  - Oversampling: menduplikasi data minoritas,
  - Undersampling: menghapus data mayoritas, atau
  - Class weight.

### 1.4. Univariate Analysis
_Univariate analysis_ dilakukan untuk melihat distribusi data dari setiap fitur secara terpisah. Bisa melihat distribusi data apakah berdistribusi normal, _left skew_, atau _right skew_ dengan menggunakan kdeplot. Kemudian melihat ada berapa banyak _outlier_ yang ada pada setiap fitur dengan menggunakan boxplot.
#### 1.4.1. Data Distribution
Dari distribusi data dapat disimpulkan bahwa:
- Sebagian besar fitur memiliki distribusi yang _positively skewed_, karena nilai mean > median.
- Sebagian besar fitur memiliki _outlier_ atau pencilan.
- Fitur `OperatingSystem` distribusinya multimodal, karena nilai mode > 2.
- Fitur `Month` distribusinya mendekati bimodal dengan data tertinggi pada bulan Mei dan November.
- Fitur `VisitorType` dengan nilai Returning_Visitor sangat mendominasi.
- Fitur `Weekend` dengan nilai False mendominasi.
- Fitur `Revenue` dengan nilai False (tidak melakukan _purchasing_) sangat mendominasi.
- Fitur `Browser` dan `TrafficType` memiliki kategori yang
cukup banyak (terdapat > 10 kategori)

Pada saat data pre-processing, kita perlu:
- Menghapus _outlier_ pada setiap fitur bisa menggunakan IQR atau Z-Score.
- Melakukan _Feature Transformation_ dengan _Log Transformation_, karena terdapat banyak fitur yang memiliki sebaran _right skew_.
- Melakukan _Feature Encoding_ untuk fitur `Month`, `Weekend`, dan `Revenue` menggunakan _Label Encoding_, sedangkan untuk fitur `VisitorType` menggunakan _One Hot Encoding_, karena terdapat > 2 kategori dan bukan tipe ordinal.
- Melakukan _Handling Imbalanced Data_ untuk fitur `Revenue`, karena fitur ini merupakan target yang mempunyai ketimpangan data yang signifikan.

### 1.5. Multivariate Analysis
Untuk melakukan _multivariate analysis_ bisa menggunakan pairplot. Pairplot digunakan untuk menganalisis antara dua variabel pada data (bivariate analysis).
Pada bagian scatterplot dapat dilihat sebaran data dari dua variabel yang dipilih. Parameter _hue_ bisa ditambahkan untuk mempertegas sebaran scatterplot setiap fitur terhadap target. Hasil observai didapatkan, antara lain:
- Hampir tidak ada scatterplot dengan warna yang terpisah dengan baik.
- Fitur dengan pemisahan warna yang kurang baik bisa berpengaruh terhadap tingkat akurasi model.
- Scatterplot dengan sebaran warna yang terpisah mengindikasikan bahwa dataset memiliki kombinasi fitur yang baik.
- Satu-satunya kolom yang cukup baik untuk dijadikan fitur adalah `PageValues` yang menunjukkan pemisahan warna secara jelas.

#### 1.5.1. Data Correlation
Dari korelasi data dapat disimpulkan bahwa:
- Dari scatterplot bisa dilihat perbedaan sebaran warna yang cukup mencolok, hal ini menunjukkan bahwa `PageValues` dengan `Revenue` memiliki korelasi yang cukup baik.
- Untuk memperjelas pola hubungan `PageValues` dengan `Revenue` bisa digunakan pointplot, tingginya nilai `PageValues` berbanding lurus dengan naiknya nilai `Revenue`, sehingga fitur `PageValues` harus dipertahankan.
- Fitur `PageValues` dan `Revenue` memiliki pola hubungan _positive linear association_ yang membentuk pola garis lurus berdasarkan Pearson Correlation.
- Untuk mencari korelasi data secara mudah juga bisa menggunakan correlation heatmap, fitur `PageValues` dan `Revenue` berkorelasi sedang (0,49), karena ketika tidak ada pembelian yang dilakukan, jumlah sesi dengan Page Values = 0 relatif tinggi, yaitu sebanyak 9.230 sesi. 
- Fitur `BounceRates` dan `ExitRates` berkorelasi tinggi (0,91), karena ketika Bounce Rate meningkat, Exit Rate juga meningkat berdasarkan hasil perhitungan oleh Google Analytics, sehingga kita bisa memilih salah satu fitur, yaitu yang memiliki correlation lebih besar(`ExitRates`) atau bisa juga melakukan _Principal Component Analysis_ (PCA).
- Korelasi antara durasi atau waktu yang dihabiskan pelanggan di halaman tertentu terhadap jumlah halamannya terlihat cukup jelas, sehingga untuk menghasilkan `Revenue`, maka harus memiliki Bounce Rates yang rendah, Exit Rates yang rendah, dan Page Values yang tinggi.

## Stage 2. Data Preprocessing

### 2.1. Data Cleansing
### 2.1.1. Handle Missing Values
- Pada saat dilakukan handle missing values didapatkan jumlah nilai pada dataset adalah 0, sehingga dapat disimpulkan dataset ini bersih karena tidak memiliki nilai kosong
### 2.1.2. Handle Duplicated Data
- Pada saat dilakukan Handle ducpliated data didapatkan jumlah duplikat 757 baris yang sebelumnya hanya 125 baris. Hal ini disebabkan sesudah dilakukan drop beberapa fitur
### 2.1.3. Handle Outliers
- Sebelum melakukan _handle outliers_, kita melakukan _splitting_ menjadi train dan test set yang kemudian kita hanya melakukan handle outliers pada data train dan selanjutnya dilakukan evaluasi performa model pada data test.
- Kita menggunakan metode Z-Score untuk menghapus data outlier ekstrem pada X_train set.
- Pada saat dilakukan handle outliers pada data train didapatkan X-train memiliki jumlah baris sebelum dilakukan filter outlier sebanyak 9.864 baris, sedangkan sesudah dilakukan filter outlier dihasilkan 8.377 baris.
### 2.1.4. Feature Transformation
- Kita menggunakan Logarithmic Transformation untuk mengubah fitur numerikal terdistribusi menceng ke kanan menjadi terdistribusi lebih normal.

| Feature                   | Skewness                                                    |
|:--------------------------|:------------------------------------------------------------|
| `Administrative`          | 1.485398                                                    |
| `Administrative_Duration` | 2.398119                                                    |
| `Informational`           | 2.727807                                                    |
| `Informational_Duration`  | 4.828783                                                    |
| `ProductRelated`          | 1.981740                                                    |
| `ProductRelated_Duration` | 2.018775                                                    |
| `BounceRates`             | 3.087599                                                    |
| `ExitRates`               | 1.787320                                                    |
| `PageValues`              | 3.203185                                                    |
| `Weekend`                 | 1.246781                                                    |

- Dari tabel di atas dapat dilihat bahwa semua fitur numerikal memiliki skewness yang tinggi (nilai absolut dari skewness > 1), sehingga perlu di-log transformasi.
- Kita menggunakan fungsi np.log untuk melakukan log transformasi pada variabel, dan menambahkan 1 untuk menghindari pembagian dengan nol. Setelah transformasi selesai, kita dapat memeriksa skewness kembali untuk memastikan bahwa nilai skewness sudah berkurang.

| Feature                   | Skewness                                                    |
|:--------------------------|:------------------------------------------------------------|
| `Administrative`          | 0.528311                                                    |
| `Administrative_Duration` | 0.233996                                                    |
| `Informational`           | 2.110203                                                    |
| `Informational_Duration`  | 2.168179                                                    |
| `ProductRelated`          | 0.090613                                                    |
| `ProductRelated_Duration` | 0.897208                                                    |
| `BounceRates`             | 2.964477                                                    |
| `ExitRates`               | 1.690123                                                    |
| `PageValues`              | 1.919541                                                    |
| `Weekend`                 | 1.246781                                                    |

- Setelah itu, kami menggunakan Min Max Scaler untuk menormalisasi data numerikal dalam rentang 0–1.

| Feature                     | count   |   mean    |    std    | min |   25%     |   50%     |   5%      | max |
|:----------------------------|:--------|:----------|:----------|:----|:----------|:----------|:----------|:----|
| `Administrative`            | 8377.0  | 0.292301  | 0.315649  | 0.0 | 0.000000  | 0.270238  | 0.540476	| 1.0 |
| `Administrative_Duration`   | 8377.0  | 0.343896  | 0.353708  | 0.0 | 0.000000  | 0.303012  | 0.687338	| 1.0 |
| `Informational`             | 8377.0  | 0.110753  | 0.250974  | 0.0 | 0.000000  | 0.000000  | 0.000000	| 1.0 |
| `Informational_Duration`    | 8377.0  | 0.104560  | 0.250072  | 0.0 | 0.000000  | 0.000000  | 0.000000	| 1.0 |
| `ProductRelated`            | 8377.0  | 0.569338  | 0.187310  | 0.0 | 0.430840  | 0.577356  | 0.708042	| 1.0 |
| `ProductRelated_Duration`   | 8377.0  | 0.699615  | 0.159284  | 0.0 | 0.607905  | 0.723116  | 0.814775	| 1.0 |
| `BounceRates`               | 8377.0  | 0.075369  | 0.137139  | 0.0 | 0.000000  | 0.007575  | 0.092018	| 1.0 |
| `ExitRates`                 | 8377.0  | 0.198254  | 0.168897  | 0.0 | 0.082879  | 0.145081  | 0.255714	| 1.0 |
| `PageValues`                | 8377.0  | 0.127191  | 0.277474  | 0.0 | 0.000000  | 0.000000  | 0.000000	| 1.0 |

### 2.1.5. Feature Encoding
Kita akan melakukan _feature encoding_ terhadap fitur `VisitorType`.
- Terdapat 18 Fitur sebelum encoding dan jumlah fitur sesudah encoding sebanyak 13 Fitur.
- Setelah Encoding kita mengeluarkan beberapa fitur seperti `SpecialDay`, `Month`, `OperatingSystem`, `Browser`, `Region`, `TrafficType` dan untuk `VisitorType` kita mengubah menjadi 3 fitur yaitu `VisitorType_New_Visitor` dan `VisitorType_Returning_Visitor'.
- Berikut adalah fitur terbaru setelah Encoding : `Administrative`, `Administrative_Duration`, `Informational`, `Informational_Duration`, `ProductRelated`, `ProductRelated_Duration`, `BounceRates`, `ExitRates`, `PageValues`, `Weekend`, `VisitorType_New_Visitor`, `VisitorType_Other`, `VisitorType_Returning_Visitor`
### 2.1.6. Handle Class Imbalance
Kami menggunakan metode Random Over-Sampling untuk _handle_ fitur target yang tidak seimbang dengan menambahkan jumlah sample pada minority class sehingga setara dengan majority class
- Dari proses pengerjaan yang kami lakukan, kami menemukan bahwa jumlah pelanggan yang melakukan transaksi hanya 1159 dibanding yang tidak melakukan transaksi 7218.
- Setelah melakukan proses handling terhadap imbalance class, data yang melakukan transaksi dan tidak masing-masing berjumlah 8350

### 2.2. Feature Engineering
#### 2.2.1. Feature Checking
#### 2.2.2. Feature Selection
Dari hasil feature selection menggunakan metode Chi-Square, menampilkan 10 fitur yang memiliki score tertinggi, yaitu:
- PageValues memiliki score paling dominan, yaitu 463,2.
- VisitorType_New_Visitor dan VisitorType_Returning_Visitor dapat memprediksi, karena tipe pelanggan dapat memengaruhi Purchase Rate.
- Administrative_Duration dan Informational_Duration dapat memprediksi, karena ketika makin lama durasi atau waktu yang dihabiskan pada suatu halaman, maka makin besar kemungkinan terjadi Purchase Rate.
- ExitRates dan BounceRates dapat memprediksi pelanggan itu melakukan purchase atau tidak, karena ketika ExitRates atau BounceRates yang rendah akan terjadi Purchase Rate yang tinggi.
- Administrative, Informational, dan ProductRelated dapat memprediksi, karena ketika makin banyak jumlah halaman yang dikunjungi, maka makin besar kemungkinan terjadi Purchase Rate.

Selain itu, kami juga mencoba menggunakan metode Extra Trees Classifier dan metode F-Classif yang menghasilkan nilai relatif mirip dengan metode Chi-Square. namun ketika kami menggunakan metode ANOVA F-value-based Feature Selection (AF), hasil yang dihasilkan berbeda dari metode lainnya, di mana nilai yang tertinggi dimiliki oleh fitur VisitorType_Returning_Visitor, bukan PageValues.

Lalu, dari hasil beberapa metode feature selection tersebut kami mengambil ... feature, yaitu ...

#### 2.2.3. Feature Extraction
Kami melakukan PCA (Principal Component Analysis) dengan tujuan mereduksi dimensi dengan membentuk feature baru dengan menggabungkan feature-feature yang sudah ada.
- `TotalPage_view` sebuah featur baru yang berisi informasi total halaman yang dilihat customer selama mengunjungi web.
- `TotalPage_time` sebuah featur baru yang berisi informasi total waktu yang dihabiskan customer dalam mengunjungi web.

Untuk mempermudah ML dalam memprediksi, kami mencoba untuk menggali informasi data yang nantinya akan dibuat feature baru.
- `ProductView_Percent` merupakan feature yang berisi informasi persentase customer dalam melihat produk yang sejenis.
- `ProductTime_Percent` merupakan feature yang berisi informasi persentase customer dalam menghabiskan waktu untuk melihat produk yang sejenis.
- `Average_ProductTime` merupakan feature yang berisi informasi rata-rata waktu yang dihabiskan customer dalam melihat produk sejenis.
- `PageValues_x_BounceRates` merupakan feature yang berisi informasi keterkaitan antara PageValues dan BounceRate, dimana jika nilainya semakin besar maka semakin baik sebuah halaman tersebut.
- `PageValues_per_ProductView` merupakan feature yang berisi informasi rasio antara nilai halaman dan jumlah halaman produk sejenis, jika semakin tinggi nilainya maka bisa dikatakan halaman produk sejenis itu cenderung menciptakan transaksi.
- `PageValues_per_ProductTime` merupakan feature yang berisi informasi rasio antara nilai halaman dan waktu yang dihabiskan untuk melihat produk sejenis, jika semakin tinggi nilainya maka customer tertarik pada produk yang ditawarkan dan cenderung melakukan transaksi setelah menghabiskan di halaman produk yang cukup lama.

## Stage 3. Business Insights
- Di daerah `Region` 1 memiliki jumlah pengunjung situs web e-commerce yang terbanyak. Solusi untuk meningkatkan ketertarikan pengunjung, kita bisa melakukan promosi ke daerah-daerah yang jarang mengunjungi situs web dengan memberikan penawaran spesial, seperti gratis ongkos pengiriman (ongkir).

- Pengunjung yang berkunjung pada _weekend_ lebih sedikit dibandingkan dengan hari-hari biasa atau _weekday_, sehingga kita bisa mengadakan _event_ untuk menarik pelanggan melakukan transaksi pada waktu _weekend_.

- Bagi _Returning Visitor_ atau pelanggan yang sering berkunjung ke situs web dan melakukan transaksi, solusi yang dapat kita berikan yaitu memberikan kupon gratis belanja yang dapat digunakan pada transaksi berikutnya.

- Bagi _New Visitor_ atau pelanggan baru, agar melakukan transaksi pertama, solusi kita, bisa diberikan produk gratis dengan syarat melakukan pembelanjaan sejumlah tertentu.

- Pada fitur `Month`, diketahui bahwa bulan Maret, Mei, November, dan Desember merupakan bulan-bulan yang sering dikunjungi pengunjung, Solusi kita, coba untuk mengadakan suatu _event_ di setiap bulan seperti event (yang dilakukan kompetitor) 1.1 hingga 12.12.

- Pada bulan Februari jumlah pelanggan yang mengunjungi situs web sangat sedikit dan terlihat dari revenue yang dihasilkan juga sedikit. Solusi kita, diberikan promo di hari Valentine untuk menarik minat pelanggan melakukan transaksi.

## License

The source code for the site is licensed under the MIT license.

## References

Sakar, C. O. & Kasto, Y. (2018). UCI Machine Learning Repository. University of California, Irvine, School of Information and Computer Sciences. https://archive.ics.uci.edu/ml/datasets/Online+Shoppers+Purchasing+Intention+Dataset

Sakar, C. O., Polat, S. O., Katircioglu, M., & Kasto, Y. (2019). Real-time prediction of online shoppers' purchasing intention using multilayer perceptron and LSTM recurrent neural networks. Neural Computing and Applications, 31(11), 6893-6908. https://doi.org/10.1007/s00521-018-3523-0
