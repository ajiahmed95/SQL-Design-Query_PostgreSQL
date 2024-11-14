# SQL-Design-Query_PostgreSQL
Portfolio Relational Database Design and Query using PostgreSQL and Dbeaver

PostgreSQL Project - Relational Database Design and Querying the Data

![d32e332fd345a66090d551b8222d22c3](https://github.com/user-attachments/assets/0b4aa8c8-0465-4e04-9008-1855a3bafc24)


Relational database adalah sistem untuk mengorganisir data ke dalam tabel-tabel yang saling berhubungan. Tabel-tabel ini memiliki baris (records) dan kolom (fields) yang terdefinisi dengan baik. Hubungan antara tabel-tabel ini memungkinkan untuk kita mencari, menyusun, dan memanipulasi data dengan efisien.

Proyek kali ini akan menggunakan PostgreSQL (dengan DBeaver) dalam mendesain database dan analisis data menggunakan query, serta pembuatan data dummy yang akan menggunakan python (Google Colab). 

![3699092f263f3bc2c9b0f0105a4f3a7c](https://github.com/user-attachments/assets/b52966dd-f9f3-40bc-b6fb-38214dfff47f)


Tujuan proyek ini adalah membangun database untuk sebuah website yang menawarkan penjualan mobil bekas. Gambaran umum tentang proyek ini adalah siapa saja (user) dapat menawarkan produknya (mobil bekas) dalam bentuk iklan dan calon pembeli (buyer) dapat melakukan pencarian berdasarkan beberapa kategori serta melakukan bid pada iklan yang menyediakan/mengaktifkan fitur bid.

Business Rule dalam project ini:
Setiap user aplikasi dapat menawarkan lebih dari satu produk mobil bekasnya.
-> Hubungan table users dengan table ads = one to many

Sebelum menjual produk mobil, user harus melengkapi data dirinya terlebih dahulu, seperti nama, kontak, dan domisili lokasi.
-> table users pada kolom nama, kontak, dan domisili lokasi = NOT NULL

User menawarkan produknya melalui iklan yang akan ditampilkan oleh website.
Iklan ini berisikan judul, detail informasi produk yang ditawarkan, serta kontak penjual.
Beberapa informasi yang harus ditulis dalam iklan adalah sebagai berikut
* Merek mobil: Toyota, Honda, Daihatsu, dll
* Model: Toyota Camry, Toyota Corolla Altis, Toyota Vios,Toyota Camry Hybrid, dll
* Jenis body mobil: MPV, SUV, Van, Sedan, Hatchback, dll
* Tipe mobil: manual atau automatic
* Tahun pembuatan mobil: 2005, 2010, 2011, 2020 
* Deskripsi lain, seperti warna, jarak yang telah ditempuh, dsb,  boleh ditambahkan sesuai kebutuhan.

-> table ads akan direlasikan dengan table users dan table car_product serta tambahan table seperti table color dan tipe mobil(transmission) dan juga kolom tambahan seperti jarak tempuh mobil.
Setiap user(buyer) bisa mencari mobil yg ditawarkan berdasarkan lokasi user(seller) penjual, merk mobil, dan jenis body mobil.
Jika calon pembeli tertarik terhadap sebuah mobil, ia dapat menawar (bid) harga produk jika penjual mengizinkan fitur tawar. 

Berdasarkan business rule yang ada maka table dan relasi yang akan digunakan dalam project ini adalah sebagai berikut:

![6b6dd2dbbb3de3160578ecabe61671cf](https://github.com/user-attachments/assets/ceb13026-3155-4c2a-b297-7d9d792b32b0)




Untuk membuat database baru dengan DBeaver, dapat dilakukan dengan create database menggunakan Alt + Insert dan masukkan judul database yang ingin digunakan.


Urutan table yang dibuat dalam DDL selalu dari table yang tidak memiliki foreign key, dapat dilihat pada table tersebut yang memiliki relasi 1:N adalah table users, ads dan bid_detail, maka table tersebut akan dibuat setelah table yang memiliki foreign key selesai dibuat. contoh: sebelum membuat table users, karena table ini memiliki relasi dengan table city dan nilai ‘one’ ada pada table city, maka table city harus dibuat terlebih dahulu.

Untuk membuat tiap table, dapat digunakan DDL dengan format:
CREATE TABLE table_name(
	column1 data_type constraint…
column2…
column-n
);

Seluruh code yang digunakan dalam membuat ERD adalah sebagai berikut:
```
create table city(
	kota_id integer primary key,
	name varchar(255) not null unique,
	latitude decimal not null,
	longitude decimal not null
);
create table car_product(
	product_id SERIAL primary key,
	brand varchar(250) not null,
	model varchar(250) not null,
	body_type varchar(250) not null,
	year numeric not null,
	price numeric not null,
	
);
create table users(
	user_id serial primary key,
	username varchar(20) unique not null,
	first_name varchar(255) not null,
	last_name varchar(255) not null,
	email varchar(255) unique not null,
	phone_number varchar(20) not null,
	kota_id int not null,
	constraint fk_user_city
		foreign key(kota_id)
		references city(kota_id)
);
create table transmission(
	transmission_id serial primary key,
	transmission_type varchar(255) unique not null
);
create table color(
	color_id serial primary key,
	color varchar(255) unique not null
);
create table ads(
	ads_id serial primary key,
	user_id int not null,
	product_id int not null,
	transmission_id int not null,
	color_id int not null,
	range_used_km int not null,
	bid_available boolean not null default false,
	created_at timestamp not null,
	constraint fk_ads_users
		foreign key(user_id)
		references users(user_id),
	constraint fk_ads_car_product_id
		foreign key(product_id)
		references car_product(product_id),
	constraint fk_ads_transmission_id
		foreign key(transmission_id)
		references transmission(transmission_id),
	constraint fk_ads_color
		foreign key(color_id)
		references color(color_id)
);
create table buyer(
	buyer_id serial primary key,
	first_name varchar(255) not null,
	last_name varchar(255) not null,
	email varchar(255) unique not null,
	phone_number varchar(20) not null,
	latitude decimal not null,
	longitude decimal not null
	username varchar(255) not null
);
create table bid_detail(
	bid_id serial primary key,
	ads_id int not null,
	buyer_id int not null,
	date_bid timestamp not null,
	bid_price numeric,
	bid_status varchar(20) not null,
	constraint fk_bid_detail_ads_id
		foreign key(ads_id)
		references ads(ads_id),
	constraint fk_bid_detail_buyer_id
		foreign key(buyer_id)
		references buyer(buyer_id)
);
```

Dapat dilihat pada code di atas, urutan pembuatan table selalu didahulukan pada table yang belum memiliki foreign key. Untuk penggunaan jenis-jenis tipe data di atas yaitu,

**INT**: Bilangan bulat. Biasanya digunakan untuk menyimpan ID atau kunci utama (primary key).

**NUMERIC**: Bilangan desimal dengan presisi dan skala yang dapat ditentukan. Digunakan untuk menyimpan nilai-nilai yang memerlukan tingkat akurasi tinggi, seperti harga atau mata uang.

**DECIMAL**: Sama seperti NUMERIC, digunakan untuk menyimpan bilangan desimal dengan presisi dan skala yang dapat ditentukan. Pada proyek ini digunakan dalam data koordinat (latitude, longitude)

**VARCHAR**: Rantai karakter dengan panjang variabel. Digunakan untuk menyimpan teks dengan panjang yang bervariasi, seperti nama, alamat, atau deskripsi.

**TIMESTAMP**: Tanggal dan waktu dengan zona waktu. Digunakan untuk menyimpan informasi tanggal dan waktu secara lengkap.

**BOOLEAN**: Berfungsi untuk menyimpan data berbentuk TRUE/FALSE

**SERIAL**: bukan merupakan tipe data asli melainkan hanya sebuah kemudahan notasi untuk membuat kolom pengenal unik (mirip dengan properti AUTO_INCREMENT yang didukung oleh beberapa database lain).

**Constraint**

Constraint merupakan aturan atau batasan yang diterapkan pada data dalam sebuah tabel untuk menjaga integritas dan konsistensi data. Constraint ini membantu memastikan bahwa data yang dimasukkan ke dalam tabel memenuhi kriteria tertentu, sehingga data yang tersimpan selalu akurat dan relevan.Penggunaan contraint pada proyek ini disesuaikan dengan objektif yang ada pada business rule.


Jenis-jenis Constraint:

**NOT NULL**:
Mencegah nilai NULL (kosong) pada kolom tertentu. Artinya, setiap baris dalam tabel harus memiliki nilai untuk kolom yang memiliki constraint NOT NULL.

**UNIQUE**:
Menjamin bahwa nilai dalam kolom tertentu harus unik (berbeda). Tidak ada dua baris yang boleh memiliki nilai yang sama pada kolom yang memiliki constraint UNIQUE.

**PRIMARY KEY**:
Kombinasi dari NOT NULL dan UNIQUE. Mendefinisikan kolom (atau kombinasi beberapa kolom) sebagai kunci utama tabel. Kunci utama secara unik mengidentifikasi setiap baris dalam tabel.

**FOREIGN KEY**:
Mendefinisikan relasi antara dua tabel. Mengacu pada kolom primary key pada tabel lain. Memastikan referensial integrity, yaitu nilai dalam kolom foreign key harus ada dalam kolom primary key pada tabel yang direferensikan.

**CHECK**:
Memungkinkan Anda mendefinisikan kondisi kustom yang harus dipenuhi oleh data dalam sebuah kolom. Kondisi ini dapat berupa perbandingan, operasi aritmatika, atau panggilan fungsi.

**DEFAULT**:
Menentukan nilai default yang akan diberikan kepada kolom jika tidak ada nilai yang diberikan saat data dimasukkan.

![515acc95e4869f5246a717453fe1f230](https://github.com/user-attachments/assets/7fe22ded-97d3-4e59-b515-25e54682346f)


Gambar di atas merupakan ERD hasil dari syntax DDL yang digunakan, dapat dilihat beberapa tabel memiliki garis yang terkoneksi dengan tabel lain karena menggunakan constraint fkey seperti pada tabel users memiliki fk kota_id yang dihubungkan dengan tabel city, dan tabel ads memiliki beberapa fkey seperti product_id, transmission_id, user_id, dan color_id terkoneksi dengan tabel yang memiliki fkey sebagai primary key dst…

Selanjutnya, kita akan membuat / mengenerate data yang akan nantinya akan di-import kedalam masing-masing tabel. data akan dibuat menggunakan python pada google colab dengan library Faker dan pandas. Untuk lebih detailnya dapat dilihat pada link berikut.

https://colab.research.google.com/drive/10yO0QfljmABgFSqW__Z9nOQ5hCgRz--h#scrollTo=IXctNpax0rTk

Setelah seluruh data sudah dibuat dalam bentuk csv, selanjutnya kita akan mengimport data-data yang sudah dibuat pada dbeaver. Untuk melakukan import dapat dilakukan dengan cara melakukan klik kanan pada tiap table dan pilih import data, dapat dilihat pada gambar di bawah. Proses import data secara berurutan mengikuti urutan tabel dibuat (mengikuti urutan create tabel pada syntax yang sudah dibuat sebelumnya) untuk menghindari terjadinya error karena jika import tabel yang memiliki foreign key dilakukan terlebih dahulu sebelum foreign key tersebut diregistrasi menjadi primary key, maka kemungkinan akan terjadi error.

![4d889311571974479f93ca9af8e2f4f6](https://github.com/user-attachments/assets/f0124a55-f5b2-4a5c-b798-ad1ebbf33ebb)


Contoh hasil import menggunakan csv pada tabel city:
![8e2e5b99a759ca6a16cd04d163204f22](https://github.com/user-attachments/assets/43717e01-20be-43dc-a11b-d93ebb8412c8)

Contoh hasil import pada tabel users:
![bcf77b0e455897e3b3e22657b012a1e9](https://github.com/user-attachments/assets/a8d793a7-92c5-4b69-81a0-d9559f7abb80)

Contoh hasil import pada tabel ads:
![790b82285dae94c4cb7c56ae72ee43ec](https://github.com/user-attachments/assets/eb46630a-f74c-4158-a2c3-47b53a37d5c7)

Contoh hasil import pada tabel bid_detail:
![e424acd0019c914c91616a8c52b1223c](https://github.com/user-attachments/assets/4432676a-fb6d-4b25-ab8d-c92590979d96)

Contoh hasil import pada tabel buyer:
![287566d04345b01bf097976493b1087e](https://github.com/user-attachments/assets/45db187d-c196-47ce-8dfe-3ed40f1ee2ce)


Selanjutnya, kita akan masuk ke dalam sesi query untuk mengetes data yang sudah kita generate menggunakan python.

Query pertama, kita akan mencari tahu mobil(dengan merk, model, dan harga) apa saja yang memiliki keluaran tahun 2015. Query yang digunakan yaitu SELECT lalu ditulis seluruh data yang ingin kita tampilkan, FROM lalu tulis nama tabel yang menyimpan data tersebut dan WHERE untuk menambahkan kondisi tahun data yang difilter.

Query:
```
--1. Mencari mobil keluaran 2015 ke atas

select product_id, brand, model, year, price
from car_product cp
where year >= 2015;
```

![3aea827c1ab07d73845272eb09053180](https://github.com/user-attachments/assets/f471ecac-850a-4556-9e0c-4a4cef0f7c64)


Selanjutnya kita ingin melihat iklan yang sudah ada pembeli(bid) dalam database, dan dibatasi sebanyak 10 data saja yang ditampilkan.

Query:
```
--2. Ads dengan pembeli
select ads_id, buyer_id, date_bid, bid_price
from bid_detail bd
limit 10;
```

![307450f3d0ebc1fa51b93b7f86fe079b](https://github.com/user-attachments/assets/e74cbe8a-495b-4159-9583-a8d2c428dfa0)



Pada query ketiga, kita ingin melihat semua mobil yang dijual oleh user bernama Bambang berdasarkan postingan terbaru.

```
--3. Melihat semua mobil yg dijual 1 akun dari yg paling baru
select user_id, product_id, brand, model, year, price, created_at
from ads a
join car_product cp using(product_id)
join users u using(user_id)
where u.first_name ilike '%bambang%'
ORDER BY created_at desc;
```

![89cd90086ab9b83c466ea12d62f48b3a](https://github.com/user-attachments/assets/0a13442a-358a-4a3a-ad5d-656032a5bee9)





Pada query keempat, kita ingin melihat deskripsi dan harga dari model yaris pada database.



```
--4. Mencari mobil bekas yang termurah berdasarkan keyword
SELECT product_id, brand, model, YEAR, price
FROM car_product cp
WHERE model ilike '%yaris%'
ORDER BY price;
```

![035ced69eb6ac3b8b368bdbde9855a28](https://github.com/user-attachments/assets/4ab22714-9729-4323-86c2-a7985cc4868d)






Selanjutnya kita akan masuk ke dalam analisis data berdasarkan beberapa pertanyaan. Pertanyaan pertama yaitu model mobil apa yang paling banyak di-bid berdasarkan data? Data ditampilkan berdasarkan nama model, jumlah produk di postingan, dan jumlah bid yang sudah dilakukan.

```
--1. Ranking popularitas model mobil berdasarkan jumlah bid
WITH bid_model AS (SELECT model
                  FROM ads a
                  JOIN car_product using(product_id)
                  JOIN bid_detail bd using(ads_id)
                  GROUP BY model)
SELECT model, count(product_id) AS count_product, count(bid_id) AS count_bid
FROM ads a
JOIN car_product cp using(product_id)
LEFT JOIN bid_detail bd USING(ads_id)
WHERE model IN (SELECT model FROM bid_model)
GROUP BY model
ORDER BY count_bid desc;
```

![c40d6b1562bd2a366556be417f21b83e](https://github.com/user-attachments/assets/508c4fb4-1e18-4721-b9d3-9ee829692ce5)




Dahaitsu Ayla merupakan model yang paling dibid sebanyak 6x bid diikuti oleh Suzuki Ertiga dan Honda Jazz sebanyak 5x dan 4x bid.


Selanjutnya, bagaimana harga tiap model dibandingkan dengan harga rata-rata per kota?


Query:
```
--2. Membandingkan harga mobil berdasarkan harga rata-rata per kota
SELECT c.name, brand, model, YEAR, price, ROUND(AVG(price) OVER (PARTITION BY c.name)) AS avg_price_by_city
FROM ads a
JOIN users u using(user_id)
JOIN city c using(kota_id)
JOIN car_product cp using(product_id);
```

![3d5c9ecc75a853f1c43cc5e813a5f259](https://github.com/user-attachments/assets/ce1650b9-51c5-4e6f-a69e-298fa8b1e7d2)






Dapat dilihat perbandingan harga mobil dengan harga rata-rata mobil tiap kota. Pada Kota Balikpapan yaitu Rp163.495.833 sedangkan di Kota Bandung cenderung lebih besar yaitu Rp181.157.895. Kota yang lainnya dapat dilihat dengan men-scroll lagi ke bawah.

Pada analisis ketiga, yaitu bagaimana perbandingan penawaran/bid buyer pada model Daihatsu Ayla?

```
--3. Dari penawaran suatu model mobil,
--	cari perbandingan tanggal user melakukan bid dengan bid selanjutnya beserta harga tawar yang diberikan ex: Daihatsu Ayla
WITH ranked_bids AS (
   SELECT
       model,
       buyer_id,
       date_bid,
       bid_price,
       LEAD(date_bid) OVER (PARTITION BY buyer_id ORDER BY date_bid) AS next_bid_date,
       LEAD(bid_price) OVER (PARTITION BY buyer_id ORDER BY date_bid) AS next_bid_price,
       COUNT(*) OVER (PARTITION BY buyer_id) AS bid_count -- Count the total bids for each buyer
   FROM bid_detail bd
   JOIN ads a USING(ads_id)
   JOIN car_product cp USING(product_id)
   WHERE model = 'Daihatsu Ayla'
)
SELECT model, buyer_id, date_bid AS first_bid_date, next_bid_date, bid_price AS first_bid_price, next_bid_price
FROM ranked_bids
WHERE next_bid_date IS NOT NULL
  OR bid_count = 1 -- Keep rows where there's only one bid for the buyer
ORDER BY buyer_id, first_bid_date;
```

![9942a88909abaced8c270887bbd752c5](https://github.com/user-attachments/assets/13e6bddb-7dcb-4e75-a812-4950db245b3a)


Berdasarkan output, dapat diketahui bahwa jumlah bidder model Daihatsu Ayla sebanyak 5 orang, hanya 1 orang yang melakukan bid kedua, namun nilai bid berkurang. Hal ini perlu dilihat lebih dalam apakah model yang di-bid merupakan dari ads yang sama atau bukan. Jika sama maka perlu dipertimbangkan untuk membuat constraint jika user melakukan bid selanjutnya maka nilai bid harus lebih besar dari nilai bid sebelumnya.

Pada analisis ke-4 kita ingin mengetahui perbandingan persentase rata-rata harga mobil dengan rata-rata nilai bid pada 6 bulan terakhir bid dilakukan, serta persentase diferensiasi dari harga rata-rata mobil(model) dengan harga rata-rata bid. model mobil yang ingin dilihat adalah 'Toyota Yaris', 'Daihatsu Ayla', 'Toyota Camri', dan 'Honda Civic'.

```
--4. Membandingkan persentase perbedaan rata-rata harga mobil
--berdasarkan modelnya dan rata-rata harga bid yang ditawarkan oleh customer pada 6 bulan terakhir

WITH max_bid_date AS (
   SELECT MAX(date_bid) AS most_recent_bid_date
   FROM bid_detail
),
filtered_bids AS (
   SELECT *
   FROM bid_detail
   WHERE date_bid >= (SELECT most_recent_bid_date - INTERVAL '6 MONTH' FROM max_bid_date)
),
avg_bid_prices as(
	SELECT model, ROUND(AVG(bid_price)) AS avg_bid_price
	FROM filtered_bids bd
	JOIN ads a USING(ads_id)
	JOIN car_product cp USING(product_id)
	WHERE model IN ('Toyota Yaris', 'Daihatsu Ayla', 'Toyota Camri', 'Honda Civic')
	GROUP BY model
),
avg_prices as(
	SELECT model, round(avg(price)) AS avg_price
	FROM ads a
	JOIN car_product cp USING(product_id)
	LEFT JOIN bid_detail bd using(ads_id)
	WHERE model IN ('Toyota Yaris', 'Daihatsu Ayla', 'Toyota Camri', 'Honda Civic')
	GROUP BY model
)
SELECT a.model, b.avg_price, a.avg_bid_price, (b.avg_price - a.avg_bid_price) AS difference, ((b.avg_price - a.avg_bid_price)/b.avg_price * 100) AS difference_percent
FROM avg_bid_prices a
JOIN avg_prices b ON a.model = b.model;
```

![4a72537de89dd5a41c05f26e52e7f52f](https://github.com/user-attachments/assets/c90b1ab3-f0cf-45bf-b5c3-4decbb61ee36)




Berdasarkan hasil, hanya ada dua mobil dari 4 mobil yang ingin dicari selama 6 bulan terakhir. Nilai bid cenderung lebih besar dibandingkan dengan harga mobil. Hal ini memungkinkan karena tiap model memiliki variasi harga berdasarkan body type dan tahunnya. sehingga kemungkinan mobil yang di-bid pada honda civic misalnya, yaitu mobil yang memiliki harga di atas rata-rata.

![14d49cb74dd2b17250f7f71d112821c2](https://github.com/user-attachments/assets/f9d8038f-9c7a-4a80-b819-52ba87c077fd)


Selain itu, kita ingin melihat nilai rata-rata harga bid berdasarkan 6 bulan terakhir tiap bulan pada model yang dipilih. contoh: Daihatsu Ayla.

```
WITH max_bid_date AS (
   SELECT MAX(date_bid) AS most_recent_bid_date
   FROM bid_detail
),
date_ranges AS (
   SELECT
       most_recent_bid_date - INTERVAL '6 MONTH' AS start_6m,
       most_recent_bid_date - INTERVAL '5 MONTH' AS start_5m,
       most_recent_bid_date - INTERVAL '4 MONTH' AS start_4m,
       most_recent_bid_date - INTERVAL '3 MONTH' AS start_3m,
       most_recent_bid_date - INTERVAL '2 MONTH' AS start_2m,
       most_recent_bid_date - INTERVAL '1 MONTH' AS start_1m,
       most_recent_bid_date AS end_date
   FROM max_bid_date
),
avg_prices AS (
   SELECT
       cp.brand,
       cp.model,
       ROUND(AVG(CASE
           WHEN date_bid >= dr.start_6m AND date_bid < dr.start_5m THEN price
           ELSE NULL
       END)) AS price_6m_to_5m,
       ROUND(AVG(CASE
           WHEN date_bid >= dr.start_5m AND date_bid < dr.start_4m THEN price
           ELSE NULL
       END)) AS price_5m_to_4m,
       ROUND(AVG(CASE
           WHEN date_bid >= dr.start_4m AND date_bid < dr.start_3m THEN price
           ELSE NULL
       END)) AS price_4m_to_3m,
       ROUND(AVG(CASE
           WHEN date_bid >= dr.start_3m AND date_bid < dr.start_2m THEN price
           ELSE NULL
       END)) AS price_3m_to_2m,
       ROUND(AVG(CASE
           WHEN date_bid >= dr.start_2m AND date_bid < dr.start_1m THEN price
           ELSE NULL
       END)) AS price_2m_to_1m,
       ROUND(AVG(CASE
           WHEN date_bid >= dr.start_1m AND date_bid < dr.end_date THEN price
           ELSE NULL
       END)) AS price_1m_to_now
   FROM ads a
   JOIN car_product cp USING(product_id)
   LEFT JOIN bid_detail bd USING(ads_id)
   CROSS JOIN date_ranges dr
   WHERE date_bid BETWEEN dr.start_6m AND dr.end_date
   GROUP BY cp.brand, cp.model
)
SELECT * FROM avg_prices
WHERE model = 'Daihatsu Ayla';
```

![63a724b18283d2cdfcb6a5bb3f05ed6c](https://github.com/user-attachments/assets/8a50fdcd-cfb2-4790-9e0a-c4930b569477)


Berdasarkan nilai yang diperoleh, nilai bid hanya dilakukan pada bulan ke 6-5 dan 5-4 lalu, setelah itu tidak ada bid yang dilakukan pada model Daihatsu Ayla. Nilai rata-rata bid naik dari 113 juta menjadi 115 juta.

Terakhir kita ingin mencari tau buyer terdekat berdasarkan koordinat seller, contoh: seller dengan lokasi jakarta pusat. Kita dapat menggunakan rumus haversine distance untuk menghitung jarak dari titik seller ke titik buyer (latitude, longitude)

```
select
   buyer_id,
   first_name,
   last_name,
   round(2 * 6371 * ASIN(
       SQRT(
           POWER(SIN(RADIANS(latitude - (-6.121435)) / 2), 2) +
           COS(RADIANS(-6.121435)) * COS(RADIANS(latitude)) *
           POWER(SIN(RADIANS(longitude - 106.774124) / 2), 2)
       )
   )) AS distance_km
FROM
   buyer
ORDER BY distance_km asc
limit 5;
```

![22325a91d05b3bc31ff99ff1c3b297dc](https://github.com/user-attachments/assets/fb95bc9d-cdc8-4e8e-8242-4a5f05e1bc29)


