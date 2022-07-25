
Soal : 5

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
Install setiap versi php
```
centos@centos ~]$ sudo yum install php73 php73-php php73-php-cli php73-php-mbstring php73-php-mcryp php73-php-cli php73-php-mysql php73-php-pdo php73-php-xml php73-php-common php73-php-fpm php73-php-devel
```
```
[centos@centos ~]$ sudo yum install php74 php74-php php74-php-cli php74-php-mbstring php74-php-mcryp php74-php-cli php74-php-mysql php74-php-pdo php74-php-xml php74-php-common php74-php-fpm php74-php-devel
```
```
[centos@centos ~]$ sudo yum install php80 php80-php php80-php-cli php80-php-mbstring php80-php-mcryp php80-php-cli php80-php-mysql php80-php-pdo php80-php-xml php80-php-common php80-php-fpm php80-php-devel
```
Buat file tes.php untuk mengetes. Di dalamnya diisi
```
<?php
phpinfo();
?>
```
Kemudian jalankan pada setiap versi. Bila versi yang keluar sesuai maka berhasil
![image](https://user-images.githubusercontent.com/109766098/180843036-7a415170-1f45-4b17-a22f-f557255871d3.png)

Selanjutnya coba cek versi php bila keluarnya -bash: php: command not found maka ada satu versi php yang telah di install belum dijadikan default kompiler, cara untuk merubahnya dengan tool scl sebagai contoh php73
```
[centos@centos ~]$ scl enable php73 bash
```
Kalau hasilnya seperti berikut maka php73 sudah berjalan sebagai default system
```
[centos@centos ~]$ php -v
PHP 7.3.33 (cli) (built: Jun  7 2022 08:45:21) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.3.33, Copyright (c) 1998-2018 Zend Technologies
```
Kalau ditest seperti tadi hasilnya muncul 7.3
```
[centos@centos ~]$ php tes.php| grep "PHP Version"
PHP Version => 7.3.33
PHP Version => 7.3.33
```
Kemudian ditest via web. Webserver nginx untuk dapat membaca php dibutuhkan modul php-fpm yang sudah di install di awal tadi pada tiap versi php. Untuk mencobanya langkahnya pertama membuat folder web dengan perintah
```
[centos@centos ~]$ mkdir /home/centos/web
```
Masuk ke dalam folder tersebut dan buat 3 folder test1 test2 test3
```
[centos@centos web]$ mkdir test1 test2 test3
```
Konfigurasi setiap versi php untuk dapat diakses di web
php73
```
[centos@centos web]$ sudo vi /etc/opt/remi/php73/php-fpm.d/www.conf
```
Pada bagian user dan group ganti dari apache menjadi nginx dan pada bagian listen diubah seperti berikut kemudian save dan keluar
```
 20 ; Unix user/group of processes
     21 ; Note: The user is mandatory. If the group is not set, the default user's group
     22 ;       will be used.
     23 ; RPM: apache user chosen to provide access to the same directories as httpd
     24 user = nginx
     25 ; RPM: Keep a group allowed to write in log dir.
     26 group = nginx
     27
     28 ; The address on which to accept FastCGI requests.
     29 ; Valid syntaxes are:
     30 ;   'ip.add.re.ss:port'    - to listen on a TCP socket to a specific IPv4 address on
     31 ;                            a specific port;
     32 ;   '[ip:6:addr:ess]:port' - to listen on a TCP socket to a specific IPv6 address on
     33 ;                            a specific port;
     34 ;   'port'                 - to listen on a TCP socket to all addresses
     35 ;                            (IPv6 and IPv4-mapped) on a specific port;
     36 ;   '/path/to/unix/socket' - to listen on a unix socket.
     37 ; Note: This value is mandatory.
     38 listen = 127.0.0.1:9073
```
php74
```
[centos@centos web]$ sudo vi /etc/opt/remi/php74/php-fpm.d/www.conf
```
Pada bagian user dan group ganti dari apache menjadi nginx dan pada bagian listen diubah seperti berikut kemudian save dan keluar
```
  20 ; Unix user/group of processes
     21 ; Note: The user is mandatory. If the group is not set, the default user's group
     22 ;       will be used.
     23 ; RPM: apache user chosen to provide access to the same directories as httpd
     24 user = nginx
     25 ; RPM: Keep a group allowed to write in log dir.
     26 group = nginx
     27
     28 ; The address on which to accept FastCGI requests.
     29 ; Valid syntaxes are:
     30 ;   'ip.add.re.ss:port'    - to listen on a TCP socket to a specific IPv4 address on
     31 ;                            a specific port;
     32 ;   '[ip:6:addr:ess]:port' - to listen on a TCP socket to a specific IPv6 address on
     33 ;                            a specific port;
     34 ;   'port'                 - to listen on a TCP socket to all addresses
     35 ;                            (IPv6 and IPv4-mapped) on a specific port;
     36 ;   '/path/to/unix/socket' - to listen on a unix socket.
     37 ; Note: This value is mandatory.
     38 listen = 127.0.0.1:9074
```
php80
```
[centos@centos web]$ sudo vi /etc/opt/remi/php80/php-fpm.d/www.conf
```
Pada bagian user dan group ganti dari apache menjadi nginx dan pada bagian listen diubah seperti berikut kemudian save dan keluar
```
     20 ; Unix user/group of processes
     21 ; Note: The user is mandatory. If the group is not set, the default user's group
     22 ;       will be used.
     23 ; RPM: apache user chosen to provide access to the same directories as httpd
     24 user = nginx
     25 ; RPM: Keep a group allowed to write in log dir.
     26 group = nginx
     27
     28 ; The address on which to accept FastCGI requests.
     29 ; Valid syntaxes are:
     30 ;   'ip.add.re.ss:port'    - to listen on a TCP socket to a specific IPv4 address on
     31 ;                            a specific port;
     32 ;   '[ip:6:addr:ess]:port' - to listen on a TCP socket to a specific IPv6 address on
     33 ;                            a specific port;
     34 ;   'port'                 - to listen on a TCP socket to all addresses
     35 ;                            (IPv6 and IPv4-mapped) on a specific port;
     36 ;   '/path/to/unix/socket' - to listen on a unix socket.
     37 ; Note: This value is mandatory.
     38 listen = 127.0.0.1:9080
```
Jalankan php-fpm
```
[centos@centos ~]$ sudo service php73-php-fpm start
```
```
[centos@centos ~]$ sudo service php74-php-fpm start
```
```
[centos@centos ~]$ sudo service php80-php-fpm start
```

Konfigurasi file nginx.conf
Setting nginx pada bagian loop server seperti ini
```
location / {
         }
         location /test1 {
         location ~ \.php$ {
         try_files $uri =404;
         fastcgi_split_path_info ^(.+\.php)(/.+)$;
         fastcgi_pass 127.0.0.1:9073;
         fastcgi_index index.php;
         fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
         include fastcgi_params;
         }
         }
         location /test2 {
         location ~ \.php$ {
         try_files $uri =404;
         fastcgi_split_path_info ^(.+\.php)(/.+)$;
         fastcgi_pass 127.0.0.1:9074;
         fastcgi_index index.php;
         fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
         include fastcgi_params;
         }
         }
         location /test3 {
         location ~ \.php$ {
         try_files $uri =404;
         fastcgi_split_path_info ^(.+\.php)(/.+)$;
         fastcgi_pass 127.0.0.1:9080;
         fastcgi_index index.php;
         fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
         include fastcgi_params;
         }
         }
 ```
cek apakah nginx sudah benar
 ```
 [centos@centos ~]$ sudo nginx -t
 nginx: [warn] conflicting server name "localhost" on 0.0.0.0:80, ignored
 nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
 nginx: configuration file /etc/nginx/nginx.conf test is successful
 ```
restart service nginx
```
[centos@centos ~]$ sudo service nginx restart
```
copy file tes.php ke folder test1 test2 test3 dengan nama index.php
```
[centos@centos ~]$ cp -a tes.php /home/centos/web/test1/index.php
[centos@centos ~]$ cp -a tes.php /home/centos/web/test2/index.php
[centos@centos ~]$ cp -a tes.php /home/centos/web/test3/index.php
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

setelah edit connection akan tampil seperti ini pilih add > ethernet

![image](https://user-images.githubusercontent.com/109766098/180867424-4fdd5492-06b8-4976-9407-1e69bdf3b4b2.png)

isi pakai IP 2

![image](https://user-images.githubusercontent.com/109766098/180867643-5bbf711d-7ad8-4756-aef8-ac535bfa670b.png)






