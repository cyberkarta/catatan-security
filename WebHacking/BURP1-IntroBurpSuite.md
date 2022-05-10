# Burp Suite
#tools

## Setting Burp Scope
https://www.youtube.com/watch?v=0mTg2BsYVmg&t=241s

Tujuannya
1. Memberitahukan Burp tentang cakupan domain yang sedang dites.
2. Meningkatkan efisiensi dalam melakukan testing dengan memilih domain yang akan di-intercept. 
3. Burp bisa mengeluarkan domain di luar scope agar tidak terdaftar pada history, sehingga data terlihat bersih dan mengurangi penggunaan memori.

### Cara Setting Scope
**Manual**
Add manual di bagian target > scope, lalu tambahkan domain yang sedang dites.
![](attachments/Pasted%20image%2020211031190834.png)

Kamu juga bisa menambahkan dibagian exclude scope.
![](attachments/Pasted%20image%2020211031190935.png)

Tujuan melakukan exclude scope:
1. Lokasi konten yang tidak penting untuk dites
2. Lokasi yang memang tidak masuk dalam cakupan tes.
3. Lokasi yang dapat mengganggu fungsionalitas dari web.
4. Lokasi yang dapat mengganggu aktivitas testingmu, contohnya fungsi logout atau deletet akun yang dapat mengganggu proses testing apabila mesin crawlingmu sedang mengujinya.

Kamu bisa menggunakan fungsi advanced scope control untuk menggunakan regex, range IP, ataupun port.
![](attachments/Pasted%20image%2020211031191639.png)

**Cara yang Mudah**
Pada sitemap, pilih URL yang ingin dimasukkan ke dalam scope atau dikeluarkan dari scope.
![](attachments/Pasted%20image%2020211031191837.png)

### In-Scope Item Only
Pada tab sitemap dan proxy > history, di bagian scope, kamu dapat memilh agar Burp menampilkan in-scope item saja.
![](attachments/Pasted%20image%2020211031192136.png)
![](attachments/Pasted%20image%2020211031192237.png)

Intercept rule pun dapat diubah untuk menggunakan in-scope item saja. Lokasinya di Proxy > Options
![](attachments/Pasted%20image%2020211031192347.png)

### In-Scope Item untuk Burp Scan (Brup Professional)
Secara otomatis, Burp Scan akan menggunakan in-scope item saja
![](attachments/Pasted%20image%2020211031192506.png)

Crawler pun dapat disetting untuk menggunakan in-scope item.
![](attachments/Pasted%20image%2020211031192541.png)
![](attachments/Pasted%20image%2020211031192642.png)

Setting live task berdasarkan suite scope
![](attachments/Pasted%20image%2020211031192744.png)

### Fungsi Lainnya yang dapat Menggunakan In-Scope
Burp search di dalam area in-scope item

Save project

Burp Intruder follow redirect
![](attachments/Pasted%20image%2020211031193054.png)

Begitu pula Burp Repeater

Burp Session Handling Rules berdasarkan in-scope item
![](attachments/Pasted%20image%2020211031193443.png)

And finally you can even drop all out-of-scope requests in the Project options > Connections.
![](attachments/Pasted%20image%2020211031193622.png)

