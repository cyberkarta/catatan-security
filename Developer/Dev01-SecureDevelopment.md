# Secure Development
#development #encryption

## Membuat Sertifikat HTTPS (Self-Signed)
Self-signed certificate hanya disarankan untuk testing environment.

Oneliner
```sh
openssl req -nodes -new -x509 -keyout key.pem -out cert.pem
```

Atau...  
Generate private key baru.
```sh
openssl genrsa -out key.pem
```

Membuat CSR (Certificate Signing Request) menggunakan privatekey tersebut.
```sh
openssl req -new -key key.pem -out csr.pem
```

Generate SSL certificate dari CSR.
```sh
openssl x509 -req -days 365 -in csr.pem -signkey key.pem -out cert.pem
```

### NodeJS
https://www.youtube.com/watch?v=USrMdBF0zcg&t=531s
```js
// app.js

const express = require('express')
const https = require('https')
const path = require('path')
const fs = require('fs')

const app = express()

app.use('/', (req, res, next) => {
	res.send('Hello from SSL server')
})

const sslServer = https.createServer(
	{
		key: fs.readFileSync(path.join(__dirname, 'cert', 'key.pem')),
		cert: fs.readFileSync(path.join(__dirname, 'cert', 'cert.pem')),
	}, app)

sslServer.listen(3445, () => console.log('Secure Server on Port 3443'))
```
> Gunakan readFileSync hanya untuk membuka SSL certificate, selain itu gunakan readFile() supaya thread menjadi asinkron.
