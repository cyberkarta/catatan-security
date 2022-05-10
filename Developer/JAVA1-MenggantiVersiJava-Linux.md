# Mengganti Versi Java (Linux)
https://aboullaite.me/switching-between-java-versions-on-ubuntu-linux/

Ambil informasi direktori dari java, jangan ubah versi java di sini
```sh
sudo update-alternatives --config java

# java compiler
sudo update-alternatives --config javac
```

Ubah versi java
```sh
sudo update-alternatives --set java <path>

# contoh
sudo update-alternatives --set java /usr/lib/jvm/java-11-openjdk-amd64/bin/java
sudo update-alternatives --set javac /usr/lib/jvm/java-11-openjdk-amd64/bin/java
```
