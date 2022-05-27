# Prototype Pollution
#web #rce #nodejs #objectinjection


## Definisi
Prototype Pollution adalah tipe dari serangan object injection.

Ketika JavaScript membuat objek baru, mereka membawa properties dan methods dari prototype "objek" yang mengandung fungsi dasar seperti `tostring, constructor, hasOwnProperty`. Contoh pada fungsi `toString` yang berubah karena terpolusi.

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



## Sumber
- https://portswigger.net/daily-swig/prototype-pollution-the-dangerous-and-underrated-vulnerability-impacting-javascript-applications
- https://www.tenable.com/blog/identifying-prototype-pollution-vulnerabilities-using-tenable-io-web-application-scanning