# Active Directory
#windows #basic 

## Domain Controller
WIndows server yang terinstall Active Directory Domain Services (AD DS), aktif, dan dipromosikan sebagai domain controller di dalam forest. Tugasnya adalah:
- Menyimpan AD DS datastore
- Menangani proses autentikasi dan autorisasi
- Replikasi update dari domain controller lain pada forest
- Memperbolehkan admin untuk melakukan manajemen resource

### AD DS Data Store
AD DS Data Store menyimpan database dan proses yang dibutuhkan untuk melakukan manajemen informasi directory, seperti users, groups, dan services. Konten dan karakteristik dari AD DS Datastore:
- Terdapat NTDS.dit, sebuah database yang menyimpan informasi dari Active Directory domain controller beserta password dalam bentuk hash dari domain suers.
- Secara default tersimpan pada `%SystemRoot%\NTDS`
- Hanya dapat diakses oleh domain controller.

## Forest
Forest adalah koleksi satu atau lebih domain tree di dalam jaringan Active Directory. Bagian dari forest:
- Trees - sebuah hirarki domain pada AD DS
- Domains - Grouping dan manajemen objek
- Organizational Units - Containers untuk grup, komputer, users, printers, dan OU lainnya.
- Trusts - Memperbolehkan users untuk mengakses resources dari domain lainnya.
- Objects - Users, groups, printers, computer, shares
- Domain Services - DNS Server, LLMNR, IPV6
- Domain Schema - Aturan untuk membuat object

# User dan Group
Secara default saat membuat sebuah domain controller, terdapat 2 default user dan 2 default group: Administrator dan Guest.

Tipe user:
- Domain admins: mengontrol domain dan satu-stunya yang bisa mengakses domain controller.
- Service account: untuk maintenance service dan dibutuhkan untuk layanan seperti SQL untuk menyambungkan service dengan service accouint.
- Local administrators: user yang memegang kendali dari local machine dan bisa jadi dapat mengontrol user biasa, namun local administrator tidak dapat mengakses domain controller.
- Domain users: user biasa.

Group adalah kumpulan dari user dan biasa digunakan untuk memberikan izin autorisasi  kepada users dan object. Secara garis besar, terdapat dua jenis grup:
- Security group: spesifikasi permission untuk jumlah user yang besar.
- Distribution group: spesifikasi list distribusi email. Sebagai penyerang grup ini kurang berguna, kecuali untuk enumerasi.

Terdapat banyak default security group, beberapa garis besarnya adalah:
- Domain controllers - seluruh domain controller di dalam domain
- Domain guests - seluruh domain guests
- Domain users - seluruh domain users
- Domain computers - seluruh workstation dan server yang bergabung di dalam domain
- Domain admins
- Enterprise admins
- Schema admins
- DNS admins
- DNS update proxy - DNS client yang diperbolehkan untuk melakukan update secara dinamis untuk kepentingan client lainnya (contohnya DHCP server)

## Domain Trusts
