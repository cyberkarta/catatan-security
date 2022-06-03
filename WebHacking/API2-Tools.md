# Tools
#webpentest #api #tools 

# Postman
- Inspect element dan masuk ke tab network. Jika belum terisi, refresh halaman web. Kemudian cari resource yang menghasilkan sebuah format API, kemungkinan berupa JSON. Urutkanlah pencarian filetype dan cari format JSON atau XHR.
![](attachments/Pasted%20image%2020220510111139.png)

- Setelah ini kamu perlu mencari potongan code yang menarik, dengan cara:
	- Membaca source code
	- Try and error

- Copy all as curl dan import
![](attachments/Pasted%20image%2020220510111431.png)

- Mungkin butuh beberapa detik untuk prettify print. Di sini kamu bisa mengubah-ubah parameter authorization, header, dan body.
![](attachments/Pasted%20image%2020220510111800.png)

- Kamu juga bisa mengimport code untuk request dan automasi.
![](attachments/Pasted%20image%2020220510112022.png)

## Burp

PUT request, DELETE Request
`Cache-Control: max-age=0`
`Content-Type: application/json`

## KiteRunner
Bruteforce route and endpoints for MVC and modern Web.

- Kite bisa download wordlist secara sementara (cache), kita tidak perlu menyediakan wordlist secara offline. 

- **Penggunaan**
```sh
# list of wordlists
kite wordlist list

# scan
kite scan https://target.com/ -A wordlist 
```

- **Cheatsheet**
```sh
# perintah
brute : directory busting
scan : melakukan scanning ke satu atau beberapa host
wordlist : interaksi dengan local wordlist atau remote wordlist

# helpful flag
-h : help
-A : assetnote wordlist

# limitasi scan
--blacklist-domain strings : blacklist redirection ke url
--delay duration : delay request ke host
-H : menentukan header, secara default [X-forwarded-for: 127.0.0.1]
-x : koneksi maksimal ke host (default: 3)
-j : 



```

## Sumber
- [Youtube-Hak5](https://www.youtube.com/watch?v=mbrX1_CVG-0)