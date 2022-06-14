# Initialization Vector
#crypto #encryption 


## Definisi
Initialization Vector (IV) adalah input dari proses kriptografi yang dipakai untuk inisiasi proses enkripsi. Untuk alasan keamanan, IV tidak boleh terprediksi dan harus unik (tidak boleh dipakai berulang kali).

## Masalah pada IV
Masalah yang terjadi dari penggunaan IV berulang adalah pada Electronic Codebook atau ECB, dengan IV yang sama dan plain text yang sama akan menghasilkan cipher text yang sama. 

Selain itu pada stream cipher, sifat unik sangat penting apabila plaintext tidak ingin diambil secara mudah.

## Sumber
- https://en.wikipedia.org/wiki/Initialization_vector
