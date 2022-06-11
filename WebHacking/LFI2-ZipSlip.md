# Zip Slip
#web #rce #pathtraversal 

## Definisi
Memperbolehkan attacker untuk menuliskan file berbahaya ke dalam sistem dengan menggunakan ekstrak archive, contoh dari file tar, jar, war, cpio, apk, rar, dan 7z.

Vulnerability ini dapat dieksploitasi dengan menggunakan archive yang berisi payload directory traversal. Contoh (../../../tmp/evil.sh).

Lalu attacker bisa melakukan overwrite ke executable file dan melakukan invoke secara remote.

Vulnerability ini sangat berbahaya pada Java, yang mana tidak terdapat central library yang dipakai untuk mempreoses archive secara high level. Tidak ada library tersebut menyebabkan banyak developer secara tidak sengaja membuat vulnerability tersebut dan melakukan sharing ke komunitas developer (Github & StackOverflow).

## Eksploitasi
**Part 1**, malicious archive dan kode ekstraksi yang tidak melakukan validasi.

Isi archive.
```sh
5 Tue Jun 5 11:04:29 BST 2018 good.sh
20 Tue Jun 5 11:04:42 BST 2018 ../../../../../../../../tmp/evil.sh
```
`good.sh` akan diekstrak ke folder tersebut, namun `evil.sh` diekstrak ke `/tmp/`.  
Untuk membuat archive tersebut, archive harus dibuat secara manual, karena archive tool biasanya tidak memperbolehkan hal tersebut, meskipun pada spesifikasi zip memperkenankannya.


## Vulnerable Code
### Java
Java (punya banyak archive library dibandingkan dengan yang lain)
```java
Enumeration<ZipEntry> entries = zip.getEntries();
while (entries.hasMoreElements()) {
   ZipEntry e = entries.nextElement();
   File f = new File(destinationDir, e.getName());
   InputStream input = zip.getInputStream(e);
   IOUtils.copy(input, write(f));
}
```
Validasi.
```java
String canonicalDestinationDirPath = destinationDir.getCanonicalPath();
File destinationfile = new File(destinationDir, e.getName());
String canonicalDestinationFile = destinationfile.getCanonicalPath();

if (!canonicalDestinationFile.startsWith(canonicalDestinationDirPath + File.separator)) {
	throw new ArchiverException("Entry is outside of the target dir: " + e.getName());
}
```

### Groovy
Seperti halnya Java, Groovy juga punya vulnerable snippet pada codebase.
```groovy
final zipInput = new ZipInputStream(newFileInputStream(self))
zipInput.withStream {
	def entry
	 while(entry = zipInput.nextEntry)  {
	   final file = new File(dest, entry.name)
	   file.parentFile?.mkdirs()
	   def output = new FileOutputStream(file)
	   output.withStream {
	     output << zipInput
	   }
	   unzippedFiles << file
	 }
}
```

Validasi
```groovy
final canonicalDestinationDirPath = destinationDir.getCanonicalPath()
final destinationfile = new File(destinationDir, e.name)
final canonicalDestinationFile = destinationfile.getCanonicalPath()

if (!canonicalDestinationFile.startsWith(canonicalDestinationDirPath + File.separator)) {
    throw new ArchiverException("Entry is outside of the target dir: ${e.name}")
}
```

### JavaScript
Jika dibandingkan dengan Java dan Groovy, JavaScript memiliki central libraries, sehingga ketika central libraries ini ditutup celahnya, maka code yang dishare di komunitas tetap aman dari serangan ini.
```js
self.on('entry', function(entry) {
	entry.pipe(Writer({
	    path: path.join(opts.path,entry.path)
}))
```

Validasi
```js
var filePath = path.join(targetFolder, entry.path);
if (filePath.indexOf(path.join(targetFolder, path.sep)) != 0) {
  return;
}
```

### .NET
```c#
public static void WriteToDirectory(IArchiveEntry entry,
                                    string destDirectory,
                                    ExtractionOptions options){
   string file = Path.GetFileName(entry.Key);
   string destFileName = Path.Combine(destDirectory, file);
   entry.WriteToFile(destFileName, options);
}
```

Validasi
```c#
destFileName = Path.GetFullPath(Path.Combine(destDirectory, entry.Key));
string fullDestDirPath = Path.GetFullPath(destDirectory + Path.DirectorySeparatorChar);
if (!destFileName.StartsWith(fullDestDirPath)) {
   throw new ExtractionException("Entry is outside of the target dir: " + destFileName);
}
```

### Go
```go
func (rarFormat) Read(input io.Reader, dest string) {
  rr := rardecode.NewReader(input, "")
  for {
    header := rr.Next()
    writeNewFile(filepath.Join(dest, header.Name), rr, header.Mode())
  }
}
```

Validasi
```go
func sanitizeExtractPath(filePath string, destination string) error {
 destpath := filepath.Join(destination, filePath)
 if !strings.HasPrefix(destpath, filepath.Clean(destination) + string(os.PathSeparator)) {
   return fmt.Errorf("%s: illegal file path", filePath)
 }
  return nil
}
```

## Sumber
- https://snyk.io/research/zip-slip-vulnerability
- https://res.cloudinary.com/snyk/image/upload/v1528192501/zip-slip-vulnerability/technical-whitepaper.pdf

Lab
- https://www.youtube.com/watch?v=l1MT5lr4p9o
