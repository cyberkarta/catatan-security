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

