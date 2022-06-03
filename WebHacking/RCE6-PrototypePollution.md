# Prototype Pollution
#web #rce #nodejs #objectinjection


## Definisi
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

## Dampak
Dampaknya: Denial of Service, SQL Injection, XSS, RCE.  
Teknik ini juga dapat bypass HTML sanitizer, contoh `html-sanitize` dan `DOMPurify`.

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

## Sumber
- https://portswigger.net/daily-swig/prototype-pollution-the-dangerous-and-underrated-vulnerability-impacting-javascript-applications
- https://portswigger.net/daily-swig/node-js-applications-open-to-prototype-pollution-attacks-via-legacy-function-in-popular-encryption-library
- https://www.tenable.com/blog/identifying-prototype-pollution-vulnerabilities-using-tenable-io-web-application-scanning