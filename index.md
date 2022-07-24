Setting IP static

Cek nmcli ada tidak

[centos@centos tmp]$ sudo rpm -qa | grep NetworkManager
kalau tidak ada install

[centos@centos tmp]$ sudo yum install NetworkManager
kemudian ditest

[centos@centos tmp]$ nmcli -h
[centos@centos tmp]$ nmcli -p dev
kalau muncul Error: NetworkManager is not running. dirun dulu

[centos@centos tmp]$ systemctl start NetworkManager.service
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to manage system services or units.
Authenticating as: Cloud User (centos)
Password:
==== AUTHENTICATION COMPLETE ===
coba run lagi harusnya tampil

[centos@centos tmp]$ nmcli -p dev
=====================
  Status of devices
=====================
DEVICE  TYPE      STATE                                  CONNECTION
--------------------------------------------------------------------------------------------------
eth0    ethernet  connected                              System eth0
eth1    ethernet  connecting (getting IP configuration)  Wired connection 1
lo      loopback  unmanaged
konfigurasi ip

[centos@centos tmp]$ sudo vi /etc/sysconfig/network-scripts/ifcfg-eth0
isinya seperti berikut C:\Users\Ratno\ratno-xx\nama-simulasi-2022\gambar\Screenshot_2.jpg

Simpan dan restart service

[centos@centos ~]$ systemctl restart network
setelah itu diverifikasi

verifikasi setting IP baru

[centos@centos ~]$ ip a s eth0
verifikasi routing

[centos@centos ~]$ ip r
verifikasi DNS server

[centos@centos ~]$ cat /etc/resolv.conf
verifikasi koneksi internet

[centos@centos ~]$ ping -c 4 google.com
contoh hasilnya Membuat SSH keygen dan memasukkan autorized_key

Buka cmd dan ketik perintah dan enter semua sampai selesai

C:\Windows\System32>ssh-keygen -t rsa
cek di /home/centos/ apakah sudah ada folder .ssh kalau belum ada dibuat dulu kalau sudah ada di dalamnya buat file authorized_keys. File diset permission 600 dan folder .ssh diset 700. Isi file dengan public key (isi dari id_rsa.pub) bila sudah dicek dengan perintah

[centos@centos .ssh]$ cat authorized_keys
Test lagi login ke ssh dengan perintah ssh -p 2255 centos@192.168.1.89 seharusnya sudah tidak meminta password lagi



**Instal Nginx di CentOS**

Paket Nginx tersedia di repositori EPEL, install epel dulu

```
sudo yum install epel-release
```

Instal Nginx dengan mengetikkan perintah yum berikut

```
sudo yum install nginx
```

Setelah instalasi selesai, aktifkan dan mulai layanan Nginx dengan 

```
sudo systemctl enable nginx
sudo systemctl start nginx
```

Periksa status layanan Nginx dengan perintah berikut:

```
sudo systemctl status nginx
```

