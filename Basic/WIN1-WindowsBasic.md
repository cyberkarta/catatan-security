# Windows Basic
#windows #basic

## Variabel dan PATH
```sh
Control Panel\System and Security\System\ > Advanced system settings > Tab advanced > Environment Variables
%windir% = Windows Directory (C:\Windows)

```

## User Manager
Manajemen user bisa melalui run: `lusrmgr.msc`
- Disable account
- Lock account


## System Configuration
Bisa melalui run: `msconfig`. Terdapat konfigurasi:
- boot (safe boot, no GUI, boot log)
- startup selection (normal, diagnostic, or selective)
- running and stopped services
- berbagai tools (change UAC setting, about, troubleshooting, dll)

## User Account Control
User tidak memerlukan privilege tinggi untuk menjalankan task dasar, justru ini akan membahayakan sistem. Ketika seorang user dengan tipe administrator log in ke sistem, user tersebut tidak akan mendapatkan permission yang tinggi. Apabila membutuhkan barulah terdapat Window yang memverifikasi, apakah operasi tersebut dijalankan dengan level administrator.

UAC tidak ada pada built-in administrator account.

Mengganti setting pada: search:  `user account control settings`

## Computer Management
Buka di search: `compmgmt`
![](attachments/Pasted%20image%2020211227155635.png)

Task scheduler dapat digunakan untuk automasi aplikasi, script, dll.

Event viewer dapat digunakan untuk melihat log. Ada beberapa tipe event: 
- Error
- Warning
- Information
- Success Audit
- Failure Audit
https://docs.microsoft.com/en-us/windows/win32/eventlog/event-types

Standard log dapat dilihat pada Windows Logs dan terdapat beberapa tipe:
- Application
- Security
- System
- Customlog
https://docs.microsoft.com/en-us/windows/win32/eventlog/eventlog-key

Seluruh shared folder dapat dilihat pada Shared Folders.

Setting user manager dapat dilihat pada Local Users and Groups.

Performa dari komputer dapat dilihat pada Performance.

Konfigurasi hardware dan driver dapat dilihat pada Device Manager

Konfigurasi backup dan manajemen disk dapat di temukan pada Storage.

Enable/disable background service pada Services and Applications. DI tab ini juga terdapat WMI control yang dapat digunakan untuk manajemen Windows dengan menggunakan script (VB atau PowerShell). WMI deprecated pada Windows 10 version 21H1 dan digantikan dengan PowerShell.

## System Information
`msinfo32`

Terdiri dari 3 bagian:
- Hardware resources: technical specification
- Components: informasi tentang hardware yang terpasang
- Software environment: software di dalam operating system dan yang sudah terinstall, terdapat informasi mengenai environment variables dan network connections

## Resource Monitor
`resmon`

Terdiri dari tab:
- CPU
- Disk
- Netwokr
- Memory

## Command Prompt
https://ss64.com/nt/

```sh
# perintah dasar
whoami
hostname

# mencari ip address
ipconfig
ipconfig /all
ipconfig /?

# running port
netstat

# manajemen network resource
net help
net help user
```

## Registry Editor
`regedit`

Registry adalah central hierarchical database yang digunakan untuk menyimpan informasi tentang konfigurasi sistem untuk user, aplikasi dan hardware.

Di dalam registry terdapat referensi dari dokumen lain yaitu:
- Profile dari tiap user
- Aplikasi yang terinstall pada komputer, dan tipe dokumen yang dapat dibuatnya
- Properti pada sheet setting pada folder beserta icon aplikasi
- Hardware yang terdapat pada sistem
- Port yang digunakan

## Alternate Data Streams (ADS)
- NTFS menyimpan file dalam bentuk atribut (nama file, timestamp, dll). Data di dalam file tersebut disimpan dalam atribut \$Data yang disebut juga dengan data stream.
- Dengan menggunakan NTFS, ADS dapat dikoneksikan kepada file.
- Namun threat actor bisa saja memanfaatkan ADS dengan menyimpan code berbahaya di dalam ADS yang nantinya dapat dipanggil dari file yang berbeda.
- Di dalam NTFS, file ADS diidentifikasi setelah filename dan titik dua, contohnya `Testfile.txt:ADS`. Artinya, file ini mengasosiasikan ADS dengan `Testfile.txt`.

## Proses Booting pada Windows
![](Pasted%20image%2020220316144609.png)







