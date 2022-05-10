# File Organization
#python 
Automate The Boring Stuff with Python, chapter 10

## Manajemen File / Folder
Init
```py
# shutil = shell utilities
import shutil, os
from pathlib import Path
```

Listing directory
```py
path = '10-Organizing-Files/folder/'
print(os.listdir(path))

# output
# ['eggs.txt', 'spam.txt']
```

Backing up the content of the folder.
```py
s = str(Path.cwd()) + '/10-Organizing-Files/folder'
d = str(Path.cwd()) + '/10-Organizing-Files/backup'

shutil.copytree(s , d, dirs_exist_ok=True)
```

Rename and move (recursively)  
Jika ada folder, maka bacon akan dipindah ke dalam folder eggs.
Namun jika tidak ada folder eggs, maka nama file bacon.txt akan berubah menjadi eggs.
```py
shutil.move('C:\\bacon.txt', 'C:\\eggs')

# atau untuk rename bisa menggunakan os.rename()
```

Delete  
- `os.unlink` untuk delete file.
- `os.rmdir(path)` untuk folder kosong.
- `shutil.rmtree(path)` untuk file & folder secara rekursif.
- `send2trash.send2trash(path)` safe delete (masuk ke recycle bin). -> **recommended**

Change directory
```py
cd = os.walk('10-Organizing-Files')
print(list(cd))

# output
# [('10-Organizing-Files', ['folder', 'backup'], ['shutil.py']), ('10-Organizing-Files/folder', [], ['eggs.txt', 'spam.txt']), ('10-Organizing-Files/backup', [], ['eggs.txt', 'spam.txt'])]
```

Iterate through `os.walk`
```py
cd = os.walk('10-Organizing-Files')

    for folderName, subfolders, filenames in cd:
        print ('The current folder is ' + folderName)

        for subfolder in subfolders:
            print('Subfolder of ' + folderName + ': ' + subfolder)
        for filename in filenames:
            print('File inside ' + folderName + ': ' + filename)
        
        print('')
```

## ZIP
Init
```py
import os, zipfile
from pathlib import Path
from posixpath import basename
```

Membuat zip baru pada file dari satu folder.
```py
p = '10-Organizing-Files/folder/'
out_zip = p + 'exampleZip.zip'

with zipfile.ZipFile(out_zip, 'w') as archieve:
    for filename in os.listdir(p):
        archieve.write(out_zip)
```

Membuat zip baru pada file .txt secara rekursif dengan `os.walk`
```py
p = str(Path.cwd())
out_zip = p + '/10-Organizing-Files/folder/zipping2.zip'
filt = '.txt'

with zipfile.ZipFile(out_zip, 'w') as archieve:
	for foldernames, subfolders, filenames in os.walk(p):
		for filename in filenames:
			if filt in filename:
				filePath = os.path.join(foldernames, filename)
				archieve.write(filePath, basename(filePath))
```

Useful command:
```py
# list of content in out_zip
out_zip.namelist()
# out = ['spam.txt', 'cats/', 'cats/catnames.txt', 'cats/zophie.jpg']

# get info
info = out_zip.getinfo('eggs.txt')
info.file_size
info.compress_size

# extract, jika folder tidak ada, maka folder tersebut akan dibuat
out_zip.extractall('extracted_zip/')

# don't forget to close the zip file
out_zip.close()
```

Menambahkan isi dari zip. Gunakan a (append) untuk menambahkan isi dari zip.
```py
with zipfile.ZipFile(new_zip, 'a') as archieve:
    archieve.write('new_file.txt', compress_type=zipfile.ZIP_DEFLATED)
```










