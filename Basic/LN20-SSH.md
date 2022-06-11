# SSH

## Keygen
https://www.ssh.com/academy/ssh/keygen

```sh
# old but gold, min 2048 bit supaya aman
ssh-keygen -t rsa -b 4096 

# old DSA dengan key 1024 bit, tidak dianjurkan untuk dipakai lagi
ssh-keygen -t dsa 

# algoritma bagus saat ini, 3 key yang disupport: 256, 384, dan 521 bit
ssh-keygen -t ecdsa -b 521 

# tidak dianjurkan di server general purpose, karena belum universal
ssh-keygen -t ed25519 


# spesifikasi nama
ssh-keygen -f ~/keyname
```

ssh-keygen --help
```sh
-t type (algorithm)
-b bit
-p change passphrase of private key
-N provide a new passphrase for the key
-I print the fingerprint of the specific public key
```

Untuk menambahkan key, gunakan public key dan isikan pada baris baru pada `~/.ssh/authorized_key`. Known hosts located in `~/.ssh/known_hosts`.

Pada perusahaan skala besar, butuh cara tertentu untuk manajemen ssh key. https://www.ssh.com/academy/iam/ssh-key-management

Saran keamanan:
- Buat password untuk menggunakan private key.
- Masukkan perintah `chmod 400 private_key` agar hanya dapat dibaca oleh pemilik key.

## Import Public Key ke Server Lain
**Kondisi 1**, ketika kamu mengetahui username dan password dari server.
```sh
ssh-copy-id -i ~/.ssh/id_rsa.pub YOUR_USER_NAME@IP_ADDRESS_OF_THE_SERVER
```

**Kondisi 2**, memasukkan  secara manual
```sh
vim ~/.ssh/authorized_keys

# masukkan entry dari public key, contohnya
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQ3GIJzTX7J6zsCrywcjAM/7Kq3O9ZIvDw2OFOSXAFVqilSFNkHlefm1iMtPeqsIBp2t9cbGUf55xNDULz/bD/4BCV43yZ5lh0cUYuXALg9NI29ui7PEGReXjSpNwUD6ceN/78YOK41KAcecq+SS0bJ4b4amKZIJG3JWm49NWvoo0hdM71sblF956IXY3cRLcTjPlQ84mChKL1X7+D645c7O4Z1N3KtL7l5nVKSG81ejkeZsGFzJFNqvr5DuHdDL5FAudW23me3BDmrM9ifUmt1a00mWci/1qUlaVFft085yvVq7KZbF2OP2NQACUkwfwh+iSTP username@hostname
```

