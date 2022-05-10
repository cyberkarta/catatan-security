# Nginx
#development

https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-20-04

## Lokasi Penting
`/etc/nginx` : file konfigurasi
`/etc/nginx/nginx.conf` : file konfigurasi global

`/var/log/nginx/access.log` : record semua request yang datang ke web server.
`/var/log/nginx/error.log`: record error pada Nginx.

## Mengamankan Nginx dengan HTTPS
https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-20-04  
  
Certbot digunakan untuk konfigurasi SSL secara otomatis
```sh
sudo apt install certbot python3-certbot-nginx
```

Pastikan konfigurasi `server_name` sudah tersedia
```sh
sudo nano /etc/nginx/sites-available/example.com

...
server_name example.com www.example.com;
...
```

Jika belum ada, update isi dari file tersebut dan verifikasi syntax.
```sh
sudo nginx -t

sudo systemctl reload nginx
```

## Mendapatkan Sertifikat SSL Secara Otomatis
```sh
sudo certbot --nginx -d example.com -d www.example.com
```
`-d` : adalah nama domain.

## Certbot Auto-Renewal
Sertifikat Let's Encrypt hanya valid selama 90 hari, jadi lebih mudah untuk meng-automasi proses pembaruan sertifikat. 
```sh
sudo systemctl status certbot.timer
```

Test prosesnya dengan dry run
```sh
sudo certbot renew --dry-run
```

[More About Certbot](https://eff-certbot.readthedocs.io/en/stable/)

