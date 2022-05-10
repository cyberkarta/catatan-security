# Git Merge
#git

## Branch
Apabila membutuhkan branch baru
```sh
git checkout -b dev 
```

Jika tidak, maka langsung checkout ke branch nya
```sh
git branch dev
```

## Merge Branch 'Dev' ke 'Main'
```sh
git checkout main
git merge dev

# melihat perbedaan antara branch
git diff

# melihat status dari merge (-s : short, -b : branch)
git status -sb
```

## Merge Conflict Dasar
Terkadang terdapat conflict dari dua versi branch.
```sh
<<<<<<< HEAD:index.html
<div id="footer">contact : email.support@github.com</div>
=======
<div id="footer">
 please contact us at support@github.com
</div>
>>>>>>> iss53:index.html
```

Contohnya, ganti seluruh block dengan
```html
<div id="footer">
please contact us at email.support@github.com
</div>
```

Hal ini juga bisa dilakukan dengan mergetool (versi GUI)
```sh
git mergetool
```

Untuk menutup merge
```sh
git status

git commit -m "berhasil merge"
```

## Membatalkan Merge
```sh
git merge --abort
```

## Mengabaikan Whitespace
```sh
# mengabaikan whitespace seutuhnya ketika membandingkan baris
git merge -Xignore-all-space dev

# mengaba
```

