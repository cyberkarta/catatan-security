# Deploy Web App

## Reverse Proxy pada NodeJS App
https://www.digitalocean.com/community/tutorials/how-to-set-up-a-node-js-application-for-production-on-ubuntu-16-04

```sh
curl -sL https://deb.nodesource.com/setup_16.x -o nodesource_setup.sh
sudo bash nodesource_setup.sh

sudo apt install nodejs build-essential pm2
```

Bare minimal NodeJS server `hello.js`
```js
const http = require('http');

const hostname = 'localhost';
const port = 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World!\n');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

```sh
pm2 startup systemd

pm2 start hello.js

systemctl status pm2
```

**Setup Nginx**
https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-20-04  

Aplikasi saat ini mendengarkan pada localhost, kamu perlu memberikan jalan kepada user untuk mengaksesnya.
```sh
sudo apt install nginx

sudo nano /etc/nginx/sites-available/example.com

# cari block server
server {
...
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
...
}
```
Konfigurasi di atas akan mengirimkan request pada hello.js yang berjalan pada port 3000 pada localhost.  

Apabila terdapat aplikasi lain yang berjalan pada port 3001, kamu dapat memberikan blok lain kepada server block.
```sh
server {
...
    location /app2 {
        proxy_pass http://localhost:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
...
}
```

Notes:
```sh
# syntax testing
sudo nginx -t

# restart nginx
sudo systemctl reload nginx
```