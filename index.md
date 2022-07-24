

Setting IP static

Cek nmcli ada tidak

```
[centos@centos tmp]$ sudo rpm -qa | grep NetworkManager
```

kalau tidak ada install

```
[centos@centos tmp]$ sudo yum install NetworkManager
```

kemudian ditest

```
[centos@centos tmp]$ nmcli -h
[centos@centos tmp]$ nmcli -p dev
```

kalau muncul Error: NetworkManager is not running. dirun dulu

```
[centos@centos tmp]$ systemctl start NetworkManager.service
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to manage system services or units.
Authenticating as: Cloud User (centos)
Password:
==== AUTHENTICATION COMPLETE ===
```

coba run lagi harusnya tampil

```
[centos@centos tmp]$ nmcli -p dev
=====================
  Status of devices
=====================
DEVICE  TYPE      STATE                                  CONNECTION
--------------------------------------------------------------------------------------------------
eth0    ethernet  connected                              System eth0
eth1    ethernet  connecting (getting IP configuration)  Wired connection 1
lo      loopback  unmanaged
```

konfigurasi ip

```
[centos@centos tmp]$ sudo vi /etc/sysconfig/network-scripts/ifcfg-eth0
```
Simpan dan restart service
```
[centos@centos ~]$ systemctl restart network
```
setelah itu diverifikasi

verifikasi setting IP baru
```
[centos@centos ~]$ ip a s eth0
```
verifikasi routing
```
[centos@centos ~]$ ip r
```
verifikasi DNS server
```
[centos@centos ~]$ cat /etc/resolv.conf
```
verifikasi koneksi internet
```
[centos@centos ~]$ ping -c 4 google.com
```
Membuat SSH keygen dan memasukkan autorized_key

Buka cmd dan ketik perintah dan enter semua sampai selesai
```
C:\Windows\System32>ssh-keygen -t rsa
```
cek di /home/centos/ apakah sudah ada folder .ssh kalau belum ada dibuat dulu kalau sudah ada di dalamnya buat file authorized_keys. File diset permission 600 dan folder .ssh diset 700. Isi file dengan public key (isi dari id_rsa.pub) bila sudah dicek dengan perintah
```
[centos@centos .ssh]$ cat authorized_keys
```
Test lagi login ke ssh dengan perintah ssh -p 2255 centos@192.168.1.89 seharusnya sudah tidak meminta password lagi

Instal Nginx di CentOS

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

Setting Mod Rewrite

Buka file default.conf. Karena file tidak ada maka copy dari example.conf kemudian diedit

```
[centos@centos /]$ sudo vi /etc/nginx/conf.d/default.conf
```

Isi sebelumnya 

```

    location / {
      try_files $uri $uri/ /index.php;
    }
```
diganti menjadi

```
 location / {
      root /usr/share/nginx/html;
        index index.php index.html index.htm;
    }
```

Install PHP-FPM

Membutuhkan EPEL repository tapi sudah diinstall tadi di atas jadi langsung saja jalankan perintah

```
[centos@centos /]$ sudo yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
```

Setelah selesai kemudian install paket yum-utils. Jalankan perintah

```
[centos@centos /]$ sudo yum install yum-utils
```

Karena akan menggunakan php 7.3 7.4 dan 8.0 maka jalankan perintah

```
[centos@centos /]$ sudo yum-config-manager --enable remi-php73
```
```
[centos@centos /]$ sudo yum-config-manager --enable remi-php74
```
```
[centos@centos /]$ sudo yum-config-manager --enable remi-php80
```

Setelah selesai lanjutkan installasi ekstensi yang perlu
```
[centos@centos /]$ sudo yum -y install php php-fpm php-mysqlnd php-zip php-devel php-gd php-mcrypt php-mbstring php-curl php-xml php-pear php-bcmath php-json php-pdo php-pecl-apcu php-pecl-apcu-devel
```

Kemudian konfigurasi php-fpm buka file /etc/php.ini. Pada baris 
```
cgi.fix_pathinfo=1
```
dijadikan
```
cgi.fix_pathinfo=0
```

Ubah file www.conf dengan perintah
```
[centos@centos /]$ sudo vi /etc/php-fpm.d/www.conf
```
ubah pada bagian ini dari apache menjadi centos
```
user = centos
group = centos
listen.owner = centos
listen.group = centos
```
Kemudian jalankan perintah berikut berurutan
```
[centos@centos /]$ sudo systemctl start php-fpm
[centos@centos /]$ sudo systemctl enable php-fpm
```

Kalau sudah jalankan perintah ini untuk cek status
```
[centos@centos /]$ sudo systemctl status php-fpm
```

Setting Interface Kedua

Install nmtui bila belum ada 
```
[centos@centos /]$ sudo yum  install NetworkManager-tui
```
Setelah selesai ketik perintah berikut untuk membuka
```
[centos@centos /]$ sudo nmtui
```
tampilanya seperti berikut

![image](https://user-images.githubusercontent.com/109766098/180657527-11100aa4-b555-4074-84d0-fd6cb9de201e.png)



