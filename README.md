# Jarkom-Modul-2-D07-2023
## Pendahuluan

Repository ini dibentuk untuk menyelesaikan tugas praktikum Jaringan Komputer.

Anggota Kelompok D07:
| Nama | NRP |
| :---: | :---: |
| Danno Denis Dhaifullah | 5025211027 |
| Fihriz Ilham Rabbany | 5025211040 |

## Pembahasan

### Soal 1

Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan _Load Balancer_ yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian yang sudah ditentukan.

**Jawaban**
Pandudewanata sebagai Router:
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.25.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.25.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.25.3.1
	netmask 255.255.255.0
```

Nakula sebagai Client:

```
auto eth0
iface eth0 inet static
	address 10.25.1.2
	netmask 255.255.255.0
	gateway 10.25.1.1
```

Sadewa sebagai Client:
```
auto eth0
iface eth0 inet static
	address 10.25.1.3
	netmask 255.255.255.0
	gateway 10.25.1.1
```


Yudhistira sebagai DNS Master:
```
auto eth0
iface eth0 inet static
	address 10.25.1.4
	netmask 255.255.255.0
	gateway 10.25.1.1
```

Werkudara sebagai DNS Slave:
```
auto eth0
iface eth0 inet static
	address 10.25.1.5
	netmask 255.255.255.0
	gateway 10.25.1.1
```

Arjuna merupakan Load Balancer: 
```
auto eth0
iface eth0 inet static
	address 10.25.2.2
	netmask 255.255.255.0
	gateway 10.25.2.1
```
Web Server Prabakusuma
```
auto eth0
iface eth0 inet static
	address 10.25.3.2
	netmask 255.255.255.0
	gateway 10.25.3.1
```

Web Server Abimanyu
```
auto eth0
iface eth0 inet static
	address 10.25.3.3
	netmask 255.255.255.0
	gateway 10.25.3.1
```

Web Server Wisanggeni
```
auto eth0
iface eth0 inet static
	address 10.25.3.4
	netmask 255.255.255.0
	gateway 10.25.3.1
```

<img width="584" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/557015d0-76b4-494c-88e1-516e7f83722e">

### Soal 2

Buatlah _website_ utama pada node arjuna dengan akses ke **arjuna.yyy.com** dengan alias **www.arjuna.yyy.com** dengan yyy merupakan kode kelompok.

**Jawaban**

- Lakukan update dan isntall bind9 pada node yudhistira. Setelah itu kita harus mengubah named.conf.local yang ada pada direktori bind dengan command ``nano /etc/bind/named.conf.local``.

```
zone "arjuna.d07.com" {  
        type master;  
        file "/etc/bind/wayang/arjuna.d07.com";
};
```

- Kemudian buat 1 folder bernama jarkom dengan command ``mkdir /etc/bind/jarkom``.
- Selanjutnya salin file db.local pada path ``/etc/bind`` ke dalam folder jarkom yang baru saja dibuat dengan ``cp /etc/bind/db.local /etc/bind/jarkom/arjuna.d07.com``.
- Kemudian kami buka file arjuna.d07.com dan mengubah beberapa bagian.

```
$TTL 604800 
@       IN      SOA     arjuna.d07.com. root.arjuna.d07.com. (
2		;serial
604800	;refresh
86400		;retry
2419200	;expire
604800)	;negative cache ttl
;
@               IN      NS      arjuna.d07.com.
@               IN      A       10.25.2.2 ; IP Arjuna
www             IN      CNAME   arjuna.d07.com.
```

- Kemudian kita melakukan restart bin dengan _command_ ``service bind9 restart`` dan merubah settingan nameserver yang ada pada nakula dan sadewa dengan mengganti name server pada ``nano /etc/resolv.conf``.

```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
echo "nameserver 10.25.1.4" >> /etc/resolv.conf
echo "nameserver 10.25.1.5" >> /etc/resolv.conf
```

- Terakhir kami melakukan testing dengan perintah ping ``arjuna.d07.com`` -c 5 dan ping ``www.arjuna.d07.com``.

![WhatsApp Image 2023-10-17 at 20 23 20_e64ab189](https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/105486369/bea79264-be8f-49f2-894d-3a9dc2b01498)


### Soal 3

Dengan cara yang sama seperti soal nomor 2, buatlah _website_ utama dengan akses ke **abimanyu.yyy.com** dan alias **www.abimanyu.yyy.com**.

**Jawaban**

- Pertama kita perlu mengubah named.conf.local dengan _command_ ``nano /etc/bind/named.conf.local``.

```
zone "abimanyu.d07.com" {  
        type master;  
        file "/etc/bind/wayang/abimanyu.d07.com";
};
```

- Kemudian buat 1 folder bernama abimanyu dengan : ``mkdir /etc/bind/abimanyu``. Lalu, sama seperti sebelumnya dengan copy file db.local dengan ``cp /etc/bind/db.local /etc/bind/abimanyu/abimanyu.d07.com`` dan buka file abimanyu yang telah di copy, kemudian sesuaikan isinya.

```
$TTL    604800  
@       IN      SOA     abimanyu.d07.com. root.abimanyu.d07.com. (
2		;serial
604800	;refresh
86400		;retry
2419200	;expire
604800)	;negative cache ttl
;
@               IN      NS      abimanyu.d07.com.
@               IN      A       10.25.3.3 ; IP Abimanyu
www             IN      CNAME   abimanyu.d07.com.
```

- Selanjutnya lakukan restart bin dengan perintah ``service bind9 restart`` dan mengubah settingan nameserver pada nakula dan sadewa (client) dengan mengganti name server pada nano ``/etc/resolv.conf`` yang diisi dengan ip yudhistira. Terakhir, sama seperti sebelumnya kami melakukan testing dengan perintah ``ping abimanyu.d07.com -c 5`` dan ``ping www.abimanyu.d07.com``.

![WhatsApp Image 2023-10-17 at 20 23 59_040fa0a1](https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/105486369/b2ff95b8-973f-471d-afed-6486c3a873e7)


### Soal 4

Kemudian, karena terdapat beberapa web yang harus di-_deploy_, buatlah subdomain **parikesit.abimanyu.yyy.com** yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

**Jawaban**

- Pada yudhistira, buka ``nano /etc/bind/abimanyu/abimanyu.d07.com`` dan tambahkan konfigurasi untuk menambahkan subdomain.

```
$TTL    604800  
@       IN      SOA     abimanyu.d07.com. root.abimanyu.d07.com. (
                        2      ; Serial
                        604800          ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
@               IN      NS      abimanyu.d07.com.
@               IN      A       10.25.3.3 ; IP Abimanyu
www             IN      CNAME   abimanyu.d07.com.
parikesit       IN      A       10.25.3.3 ; IP Abimanyu
www.parikesit   IN      CNAME   parikesit.abimanyu.d07.com.
```

- Dengan konfigurasi diatas, maka akan menambahkan domain dengan nama parikesit yang juga akan mengarah ke ip abimanyu. Kemudian lakukan ``restart bind service bind9 restart`` dan lakukan pengetesan untuk melakukan ping pada subdomain.

![WhatsApp Image 2023-10-17 at 20 24 48_6e6f3212](https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/105486369/1195e727-93de-4b28-8ee3-5590b4ad36a9)

### Soal 5

Buat juga _reverse_ domain untuk domain utama. **(Abimanyu saja yang direverse)**

**Jawaban**



- Pertama tama, edit ``named.conf.local`` dengan membuka ``nano /etc/bind/named.conf.local`` kemudian tambahkan konfigurasi untuk melakukan reverse domain utama yaitu abimanyu.

```
zone "1.25.10.in-addr.arpa" {  
        type master;  
        file "/etc/bind/wayang/1.25.10.in-addr.arpa
```
- Sama seperti sebelumnya dengan copy file db.local, kami melakukan perubahan pada file 1.25.10.in-addr.arpa menjadi seperti gambar di bawah ini.

```
$TTL    604800  
@       IN      SOA     abimanyu.d07.com. root.abimanyu.d07.com. (
                        2      ; Serial
                        604800          ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
1.25.10.in-addr.arpa.  IN      NS      abimanyu.d07.com.
4                       	IN      PTR     abimanyu.d07.com.
```

- Kemudian kami harus melakukan restart service bind9 restart untuk melakukan pengecekan konfigurasi maka pada _client_ (nakula/sadewa) bisa dilakukan download dengan nameserver awal dulu (192.168.122.1) agar terhubung ke internet untuk melakukan ``apt-get update`` dan juga ``apt-get install dnsutils``. Setelah proses download selesai maka ubah nameserver ke arah yudhistira untuk melakukan cek konfigurasi dan apabila berhasil maka akan muncul output seperti ini.

![WhatsApp Image 2023-10-17 at 20 25 28_d65e032d](https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/105486369/bdfb9db7-01d1-4ae1-bdac-d00b4c2215c2)

### Soal 6

Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

**Jawaban**

- Edit pada ``/etc/bind/named.conf.local`` di yudhistira dan sesuaikan.

![WhatsApp Image 2023-10-17 at 20 23 20_8cc7789f](https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/105486369/fc4af330-976d-4412-bf7a-4585af0dfe55)

![WhatsApp Image 2023-10-17 at 20 23 59_2f48eca6](https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/105486369/ad2c5319-83fd-451d-bbfc-9db7984e1c35)

- Mengubah pada kedua dns, kemudian lakukan ``service bind9 restart``.
- Lakukan setting pada dns slave yaitu werkudara dengan mengunduh bind9 dengan cara ``apt-get update`` kemudian ``apt-get install bind9 -y``.
- Tambahkan pada /etc/bind/named.conf.local pada werkudara dengan kedua dns.

```
zone "arjuna.d07.com" {  
        type master;
	notify yes;
        also-notify {10.25.1.5;};  //Masukan IP Werkudara
        allow-transfer {10.25.1.5;};  //Masukan IP Werkudara
        file "/etc/bind/wayang/arjuna.d07.com";
};

zone "abimanyu.d07.com" {  
        type master;  
	notify yes;
        also-notify {10.25.1.5;};  //Masukan IP Werkudara
        allow-transfer {10.25.1.5;};  //Masukan IP Werkudara
        file "/etc/bind/wayang/abimanyu.d07.com
```

- Kemudian lakukan ``service bind9 restart``.
- Lakukan ``service bind9 stop``
- Pada yudhistira untuk melakukan cek apakah bisa mengakses melalui dns slave yang sudah dibuat. pada client (nakula/sadewa) masukkan 2 nameserver yaitu ip dari yudhistira dan juga ip dari werkudara dengan ``nano /etc/resolv.conf``.
- Lakukan ping ke kedua dns pada client sadewa dan ping berhasil meskipun bind9 pada yudhistira di stop yang berarti konfigurasi dns slave berhasil.


### Soal 7

Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu **baratayuda.abimanyu.yyy.com** dengan alias **www.baratayuda.abimanyu.yyy.com** yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

**Jawaban**

- Lakukan konfigurasi untuk kasus ini adalah delegasi subdomain, Pada yudhistira kita melakukan edit file /etc/bind/abimanyu/abimanyu.d07.com dan mengubah menjadi seperti di bawah ini

```
$TTL    604800  
@       IN      SOA     abimanyu.d07.com. root.abimanyu.d07.com. (
                        2      ; Serial
                        604800          ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
@               IN      NS      abimanyu.d07.com.
@               IN      A       10.25.3.3 ; IP Abimanyu
www             IN      CNAME   abimanyu.d07.com.
parikesit       IN      A       10.25.3.3 ; IP Abimanyu
www.parikesit   IN      CNAME   parikesit.abimanyu.d07.com.
ns1             IN      A       10.25.1.5; IP Werkudara
baratayuda      IN      NS      ns1
```

- keterangan ns untuk delegasi subdomain yang dimana baratayuda adalah subdomain yang akan didelegasikan. Kemudian kita melakukan edit file ``/etc/bind/named.conf.options``.
- melakukan comment pada ``dnssec-validation auto;`` dan menambahkan ``allow-query{any;};`` kemudian pada ``/etc/bind/named.conf.local``.
- Tambahkan _allow-transfer_ dengan IP werkudara, namun sebelumnya sudah di-set jadi tidak dilakukan perubahan karena sudah sesuai lakukan ``service bind9 stop``.
- Pada Werkudara edit file ``/etc/bind/named.conf.options`` sama persis seperti poin nomor 3 dengan melakukan comment pada ``dnssec-validation auto;`` dan menambahkan ``allow-query{any;};``
- Lakukan edit file ``/etc/bind/named.conf.local`` pada werkudara menjadi seperti

```
zone "baratayuda.abimanyu.d07.com"{  
        type master;
        file "/etc/bind/Baratayuda/baratayuda.abimanyu.d07.com";
};
```

- sesuai ketentuan dengan folder baratayuda. buat directory sesuai folder ``mkdir /etc/bind/baratayuda``.
- Kemudian lakukan copy pada db.local dengan ``cp /etc/bind/db.local /etc/bind/baratayuda/baratayuda.abimanyu.b07.com``.
- Kemudian lakukan edit pada file ``baratayuda.abimanyu.d07.com``

```
$TTL    604800  
@       IN      SOA     baratayuda.abimanyu.d07.com. root.baratayuda.abimanyu.d07.com.  (
                        2      ; Serial
                        604800          ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
@               IN      NS      baratayuda.abimanyu.d07.com.
@               IN      A       10.25.3.3       ;IP Abimanyu
www             IN      CNAME   baratayuda.abimanyu.d07.com.
```

- Dengan menambahkan alias www pada dns tersebut dan sesuai ketentuan dengan IP mengarah ke abimanyu. lakukan ``service bind9 stop``.
- Lakukan testing pada _client_ (nakula/sadewa) pada delegasi subdomain yang telah dilakukan dengan dan tanpa alias (www).

![WhatsApp Image 2023-10-17 at 20 29 07_03cee6ef](https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/105486369/f95d0bf2-a0d6-4dec-9387-6d719253df51)


### Soal 8

Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses **rjp.baratayuda.abimanyu.yyy.com** dengan alias **www.rjp.baratayuda.abimanyu.yyy.com** yang mengarah ke Abimanyu.

**Jawaban**

- Lakukan pemberian subdomain namun pada subdomain hasil delegasi sebelumnya dengan menambahkan rjp.dan alias www. kita melakukan perubahan pada file ``baratayuda.abimanyu.d07.com`` dengan ``nano /etc/bind/baratayuda/baratayuda.abimanyu.d07.com``.

```
$TTL    604800  
@       IN      SOA     baratayuda.abimanyu.d07.com. root.baratayuda.abimanyu.d07.com.  (
                        2      ; Serial
                        604800          ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;

@               IN      NS      baratayuda.abimanyu.d07.com.
@               IN      A       10.25.3.3       ;ip Abimanyu
www             IN      CNAME   baratayuda.abimanyu.d07.com.
rjp         IN      A       10.25.3.3       ;IP Abimanyu
www.rjp     IN      CNAME   baratayuda.abimanyu.d07.com
```

- Tambahkan subdomain sesuai ketentuan yaitu subdomain rjp dan memberikan alias www.rjp untuk CNAME yang di set menuju subdomain sebelumnya. Lakukan ``service bind9 stop``.
- Test ping dengan subdomain dan alias yang diberikan.

![WhatsApp Image 2023-10-17 at 20 31 04_5e51fc67](https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/105486369/80c2c8ee-0125-47f3-a2aa-bf88f85543de)


### Soal 9

Arjuna merupakan suatu _Load Balancer_ Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan _deployment_ pada masing-masing worker.

### Soal 10

Kemudian gunakan algoritma **Round Robin** untuk Load Balancer pada **Arjuna**. Gunakan _server_name_ pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan _worker_ yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
- _Prabakusuma:8001_
- _Abimanyu:8002_
- _Wisanggeni:8003_

**Jawaban**

**Node Arjuna**

Menambahkan server DNS (192.168.122.1) ke file `resolv.conf`. Sehingga dapat digunakan untuk mengonfigurasi server agar menggunakan internet

``
echo "nameserver 192.168.122.1" > /etc/resolv.conf
``

Menginstal BIND9 (server DNS) dan Nginx (server web) 

```
apt-get update
apt-get install bind9 nginx -y
```

Blok teks berikut ini mengonfigurasi Nginx sebagai server load balancing:

```
echo "
upstream myweb  {
	server Prabukusuma:8001; # IP Prabukusuma
	server Abimanyu:8002;   # IP Abimanyu
	server Wisanggeni:8003;  # IP Wisanggeni
}

server {
	listen 80;
	server_name arjuna.d07.com;

	location / {
		proxy_pass http://myweb;
	}
}
" > /etc/nginx/sites-available/lb-jarkom
```

Menghapus konfigurasi default Nginx untuk mengaktifkan konfigurasi yang baru.

``
rm -rf /etc/nginx/sites-enabled/default
``

Membuat symlink (shortcut) dari konfigurasi baru (lb-jarkom) ke direktori sites-enabled agar konfigurasi tersebut diaktifkan.

``
ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled
``

Blok teks di bawah ini menambahkan konfigurasi pada `/etc/hosts`. Untuk menambahkan entri host yang mengaitkan alamat IP dengan nama host.
```
echo "
10.25.3.2       Prabukusuma
10.25.3.3       Abimanyu
10.25.3.4       Wisanggeni
" >> /etc/hosts
```

Merestart layanan Nginx untuk menerapkan perubahan konfigurasi.

``
service nginx restart
``

**Node Prabukusuma**

Tentu saja, langkah berikut berlaku untuk nginx worker lainnya, yaitu Abimanyu dan Wisanggeni

Menetapkan server DNS ke alamat IP 192.168.122.1 dalam file `resolv.conf`.

``
echo "nameserver 192.168.122.1" > /etc/resolv.conf
``

Menginstal Nginx, PHP, dan PHP-FPM 

```
apt-get update
apt install nginx php php-fpm -y
```

Membuat direktori `/var/www/wayang` untuk menyimpan konten situs web. Menyimpan pesan PHP sederhana dalam file `index.php` di direktori situs web. Membuat konfigurasi Nginx untuk situs web Prabukusuma di dalam file wayang pada direktori `sites-available`. Menghapus konfigurasi default Nginx agar dapat mengaktifkan konfigurasi yang baru. Membuat symlink (shortcut) dari konfigurasi baru (wayang) ke direktori sites-enabled agar konfigurasi tersebut diaktifkan. Memulai layanan PHP-FPM. Merestart layanan Nginx untuk menerapkan perubahan konfigurasi.
``
mkdir -p /var/www/wayang
``

```
echo "<?php
echo \"Halo, Kamu berada di Prabukusuma\";
?>" > /var/www/wayang/index.php
```

```
echo " 
server {
	listen 8001;
	root /var/www/wayang;
	index index.php index.html index.htm;
	server_name Prabukusuma;
	location / {
		try_files \$uri \$uri/ /index.php?\$query_string;
	}
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
	}
	location ~ /\.ht {
		deny all;
	}
	error_log /var/log/nginx/wayang_error.log;
	access_log /var/log/nginx/wayang_access.log;
}" > /etc/nginx/sites-available/wayang
```
``
rm -rf /etc/nginx/sites-enabled/default
``

``
ln -s /etc/nginx/sites-available/wayang /etc/nginx/sites-enabled
``

```
service php7.2-fpm start
service nginx restart
```

**Testing**

lynx http://arjuna.d07.com

><img width="363" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/3bfde72b-e621-46c2-88a1-58a2e7efad20">

lynx 10.25.3.2:8001

><img width="359" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/88759e16-4e35-4cf6-9552-7f9aaf11d811">





### Soal 11

Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server **www.abimanyu.yyy.com**. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

**Jawaban**

**Node Abimanyu**
Menghentikan layanan Nginx. Menginstal Apache2 dan PHP. Memulai layanan Apache2. Menginstal modul PHP untuk Apache. Menginstal utilitas unzip untuk mengekstrak arsip ZIP.
```
service nginx stop
apt-get install apache2 php
service apache2 start
apt-get install libapache2-mod-php
apt-get install ca-certificates openssl -y
apt-get install unzip
```


Blok teks berikut ini mengonfigurasi VirtualHost untuk `abimanyu.d07.com`:
```
echo "
<VirtualHost *:80>
    ServerAdmin webmaster@localhost 
    DocumentRoot /var/www/abimanyu.d07
    ServerName abimanyu.d07.com
    ServerAlias www.abimanyu.d07.com

    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
" > /etc/apache2/sites-available/abimanyu.d07.com.conf
```

Mengunduh dan menyiapkan konten `abimanyu.d07.com`:
```
wget --no-check-certificate "https://drive.google.com/uc?export=download&id=1a4V23hwK9S7hQEDEcv9FL14UkkrHc-Zc" -O download.zip
unzip download.zip
mv abimanyu.yyy.com /var/www/abimanyu.d07
```

Mengaktifkan konfigurasi VirtualHost.

``
a2ensite abimanyu.d07.com
``

Merestart layanan Apache untuk menerapkan perubahan konfigurasi.

``
service apache2 restart
``

**Testing**

lynx abimanyu.d07.com

><img width="361" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/160fb1eb-7b65-42da-a67a-d9825fdc4ac5">


### Soal 12

Setelah itu ubahlah agar url **www.abimanyu.yyy.com/index.php/home** menjadi **www.abimanyu.yyy.com/home**.

**Jawaban**

Mengaktifkan modul rewrite pada Apache. Merestart layanan Apache untuk menerapkan perubahan setelah mengaktifkan modul rewrite.
a2enmod rewrite
service apache2 restart

Blok teks di bawah ini mengonfigurasi `.htaccess` untuk mengaktifkan mod_rewrite:
```
echo "
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule (.*) /index.php/$1 [L]
" > /var/www/abimanyu.d07/.htaccess 
```

Blok teks berikut ini memperbarui konfigurasi VirtualHost untuk `abimanyu.d07.com` dengan menambahkan konfigurasi `<Directory>`
```
echo "
<VirtualHost *:80>
    ServerAdmin webmaster@localhost 
    DocumentRoot /var/www/abimanyu.d07
    ServerName abimanyu.d07.com
    ServerAlias www.abimanyu.d07.com

    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined

    <Directory /var/www/abimanyu.d07>
        Options +FollowSymLinks -Multiviews
        AllowOverride All
    </Directory>
</VirtualHost>
" > /etc/apache2/sites-available/abimanyu.d07.com.conf
```
Mengaktifkan konfigurasi VirtualHost.
``
a2ensite abimanyu.d07.com
``

Merestart layanan Apache untuk menerapkan perubahan konfigurasi.
``
service apache2 restart
``

**Testing**

lynx www.abimanyu.d07.com/home

><img width="361" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/c8bcc99a-e72a-4c37-b773-7e8df6716eb1">


### Soal 13

Selain itu, pada subdomain **www.parikesit.abimanyu.yyy.com**, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy.

**Jawaban**

Blok teks berikut ini menetapkan konfigurasi VirtualHost untuk `parikesit.abimanyu.d07.com`:
```
echo "
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/parikesit.abimanyu.d07
    ServerName parikesit.abimanyu.d07.com
    ServerAlias www.parikesit.abimanyu.d07.com

    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined

    <Directory /var/www/parikesit.abimanyu.d07>
        Options +FollowSymLinks -Multiviews
        AllowOverride All
    </Directory>
</VirtualHost>
" > /etc/apache2/sites-available/parikesit.abimanyu.d07.com.conf
```

Mengaktifkan konfigurasi VirtualHost 
``
a2ensite parikesit.abimanyu.d07.com
``

Mengunduh dan menyiapkan konten untuk `parikesit.abimanyu.d07.com`:
```
wget --no-check-certificate "https://drive.google.com/uc?export=download&id=1LdbYntiYVF_NVNgJis1GLCLPEGyIOreS" -O download.zip
unzip download.zip
mv parikesit.abimanyu.yyy.com /var/www/parikesit.abimanyu.d07
```

Menyusun konten PHP sederhana dalam file index.php di direktori situs web.
``
echo "<?php echo \"Halo ini Parikesit\"; ?>" > /var/www/parikesit.abimanyu.d07/index.php
``

Merestart layanan Apache untuk menerapkan perubahan konfigurasi.
``
service apache2 restart
``

**Testing**

lynx parikesit.abimanyu.d07.com

><img width="364" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/f5bed3ea-5eda-48e7-a7b3-3c77b3751462">




### Soal 14

Pada subdomain tersebut folder **/public** hanya dapat melakukan _directory listing_ sedangkan pada folder /secret tidak dapat diakses (_403 Forbidden_).

**Jawaban**

Blok teks berikut ini menetapkan konfigurasi VirtualHost untuk `parikesit.abimanyu.d07.com` dengan menambahkan konfigurasi `<location "/secret">`:
```
echo "
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/parikesit.abimanyu.d07
    ServerName parikesit.abimanyu.d07.com
    ServerAlias www.parikesit.abimanyu.d07.com

    <Directory /var/www/parikesit.abimanyu.d07/public>
        Options +Indexes
    </Directory>

    <Location \"/secret\">
        Deny from all
    </Location>

    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined

    <Directory /var/www/parikesit.abimanyu.d07>
        Options +FollowSymLinks -Multiviews
        AllowOverride All
    </Directory>
</VirtualHost>
" > /etc/apache2/sites-available/parikesit.abimanyu.d07.com.conf
```

Merestart layanan Apache untuk menerapkan perubahan konfigurasi.
``
service apache2 restart
``

**Testing**

lynx parikesit.abimanyu.d07.com/public

><img width="361" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/a4710e80-ac99-401b-81e7-d5320eb66ee7">

lynx parikesit.abimanyu.d07.com/secret

><img width="361" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/eebfc0ee-c657-4dd3-8e60-d06bc1605790">



### Soal 15

Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah **_404 Not Found_** dan **_403 Forbidden_**.

**Jawaban**

Blok teks berikut ini menetapkan konfigurasi VirtualHost untuk `parikesit.abimanyu.d07.com` dengan menambahkan konfigurasi `ErrorDocument` untuk mengganti halaman error yang diinginkan:
```
echo "
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/parikesit.abimanyu.d07
    ServerName parikesit.abimanyu.d07.com
    ServerAlias www.parikesit.abimanyu.d07.com

    <Directory /var/www/parikesit.abimanyu.d07/public>
        Options +Indexes
    </Directory>

    <Location \"/secret\">
        Deny from all
    </Location>

    ErrorDocument 404 /error/404.html
    ErrorDocument 403 /error/403.html

    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined

    <Directory /var/www/abimanyu.d07>
        Options +FollowSymLinks -Multiviews
        AllowOverride All
    </Directory>
</VirtualHost>
" > /etc/apache2/sites-available/parikesit.abimanyu.d07.com.conf
```

Merestart layanan Apache untuk menerapkan perubahan konfigurasi.

``
service apache2 restart
``

**Testing**

lynx parikesit.abimanyu.d07.com/secret

><img width="358" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/c2251b7e-44fa-42f3-99e5-27b0f2a9f82a">

lynx parikesit.abimanyu.d07.com/tesssss

><img width="363" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/aaf58f6f-7fe8-446d-ac75-8ca99bccb1c5">




### Soal 16

Buatlah suatu konfigurasi virtual host agar file asset **www.parikesit.abimanyu.yyy.com/public/js** menjadi 
**www.parikesit.abimanyu.yyy.com/js**.

**Jawaban**

Blok teks berikut ini menetapkan konfigurasi VirtualHost untuk `parikesit.abimanyu.d07.com` dengan menambahkan konfigurasi `Alias "/js" "/var/www/parikesit.abimanyu.d07/public/js"`untuk mengonfigurasi rute `/js` yang akan diarahkan ke direktori `/var/www/parikesit.abimanyu.d07/public/js`:
```
echo "
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/parikesit.abimanyu.d07
    ServerName parikesit.abimanyu.d07.com
    ServerAlias www.parikesit.abimanyu.d07.com

    <Directory /var/www/parikesit.abimanyu.d07/public>
        Options +Indexes
    </Directory>

    <Location \"/secret\">
        Deny from all
    </Location>

    Alias \"/js\" \"/var/www/parikesit.abimanyu.d07/public/js\"

    ErrorDocument 404 /error/404.html
    ErrorDocument 403 /error/403.html

    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined

    <Directory /var/www/abimanyu.d07>
        Options +FollowSymLinks -Multiviews
        AllowOverride All
    </Directory>
</VirtualHost>
" > /etc/apache2/sites-available/parikesit.abimanyu.d07.com.conf
```

Merestart layanan Apache untuk menerapkan perubahan konfigurasi.

``
service apache2 restart
``

**Testing**

lynx parikesit.abimanyu.d07.com/js

><img width="359" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/dde7c7e9-d92f-43b5-9a36-faba31ec3715">



### Soal 17

Agar aman, buatlah konfigurasi agar **www.rjp.baratayuda.abimanyu.yyy.com** hanya dapat diakses melalui port **14000** dan **14400**.

**Jawaban**

Dua blok konfigurasi untuk VirtualHost pada dua port (14000 dan 14400) untuk situs `rjp.baratayuda.abimanyu.d07.com`:

```
echo "
<VirtualHost *:14000>
    ServerAdmin webmaster@localhost
    ServerName rjp.baratayuda.abimanyu.d07.com
    ServerAlias www.rjp.baratayuda.abimanyu.d07.com
    DocumentRoot /var/www/rjp.baratayuda.abimanyu.d07

    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

<VirtualHost *:14400>
    ServerAdmin webmaster@localhost
    ServerName rjp.baratayuda.abimanyu.d07.com
    ServerAlias www.rjp.baratayuda.abimanyu.d07.com
    DocumentRoot /var/www/rjp.baratayuda.abimanyu.d07

    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
" > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.d07.com.conf
```


Mengunduh dan menyiapkan konten untuk `rjp.baratayuda.abimanyu.d07.com`:
```
wget --no-check-certificate "https://drive.google.com/uc?id=1pPSP7yIR05JhSFG67RVzgkb-VcW9vQO6" -O download.zip
unzip download.zip
mv rjp.baratayuda.abimanyu.yyy.com /var/www/rjp.baratayuda.abimanyu.d07
```
Membuat file index.php di direktori DocumentRoot

```
echo "<?php echo 'Halo, Kamu berada di Rjp'; ?>" > /var/www/rjp.baratayuda.abimanyu.d07/index.php
```

Menambahkan konfigurasi Listen ke ports.conf
```
echo "
Listen 14000
Listen 14400" >> /etc/apache2/ports.conf
```

Mengaktifkan konfigurasi VirtualHost

``
a2ensite rjp.baratayuda.abimanyu.d07.com
``

Merestart layanan Apache untuk menerapkan perubahan konfigurasi.

``
service apache2 restart
``

**Testing**

lynx rjp.baratayuda.abimanyu.d07.com:80

><img width="441" alt="Screenshot 2023-10-11 213744" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/ffd57abd-bae8-47f4-a0e5-fd2145452b2f">

lynx rjp.baratayuda.abimanyu.d07.com:14000

lynx rjp.baratayuda.abimanyu.d07.com:14400

><img width="431" alt="Screenshot 2023-10-11 213709" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/78165f77-5ee2-40a1-9b66-83a7b8c58b2d">

lynx rjp.baratayuda.abimanyu.d07.com:8000

><img width="362" alt="Screenshot 2023-10-11 213310" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/2f3702c5-8dc0-4439-af49-0ee269d63c48">



### Soal 18

Untuk mengaksesnya buatlah autentikasi username berupa **“Wayang”** dan password **“baratayudayyy”** dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada **/var/www/rjp.baratayuda.abimanyu.yyy**.

**Jawaban**

Membuat file htpasswd di lokasi `/etc/apache2/.htpasswd` dengan menambahkan pengguna baru "Wayang" dengan kata sandi "baratayudad07".

``
htpasswd -c -b /etc/apache2/.htpasswd Wayang baratayudad07
``

Konfigurasi VirtualHost. Menggunakan autentikasi dasar dengan file `.htpasswd`. Sehingga hanya pengguna yang valid dapat mengakses konten terbatas yang didefinisikan oleh `<Directory>`.
```
echo "<VirtualHost *:14000>
    ServerAdmin webmaster@localhost
    ServerName rjp.baratayuda.abimanyu.d07.com
    ServerAlias www.rjp.baratayuda.abimanyu.d07.com
    DocumentRoot /var/www/rjp.baratayuda.abimanyu.d07

    <Directory \"/var/www/rjp.baratayuda.abimanyu.d07\">
        AuthType Basic
        AuthName \"Restricted Content\"
        AuthUserFile /etc/apache2/.htpasswd
        Require valid-user
    </Directory>

    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

<VirtualHost *:14400>
    ServerAdmin webmaster@localhost
    ServerName rjp.baratayuda.abimanyu.d07.com
    ServerAlias www.rjp.baratayuda.abimanyu.d07.com
    DocumentRoot /var/www/rjp.baratayuda.abimanyu.d07

    <Directory \"/var/www/rjp.baratayuda.abimanyu.d07\">
        AuthType Basic
        AuthName \"Restricted Content\"
        AuthUserFile /etc/apache2/.htpasswd
        Require valid-user
    </Directory>

    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
" > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.d07.com.conf
```

Merestart layanan Apache untuk menerapkan perubahan konfigurasi.

``
service apache2 restart
``

**Testing**

lynx rjp.baratayuda.abimanyu.d07.com:14000

><img width="450" alt="Screenshot 2023-10-11 214928" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/169346df-fc56-4112-9969-82d904d4bdfa">
><img width="444" alt="Screenshot 2023-10-11 214920" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/815abf81-0043-4f05-acbf-3d474890887b">
><img width="447" alt="Screenshot 2023-10-11 214855" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/73d09efc-44a8-486b-871a-a66bd7632182">





### Soal 19

Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke **www.abimanyu.yyy.com** (alias)

**Jawaban**

Blok teks berikut ini mengkonfigurasi VirtualHost dengan menggunakan file konfigurasi default Apache `000-default.conf`.
```
echo "<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html

    RewriteEngine On
    RewriteCond %{HTTP_HOST} !^abimanyu\.d07\.com$ [NC]
    RewriteRule ^/(.*)$ http://www.abimanyu.d07.com/$1 [L,R=301]

    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
" > /etc/apache2/sites-available/000-default.conf
```

Merestart layanan Apache untuk menerapkan perubahan konfigurasi.

``
service apache2 restart
``

**Testing**

lynx 10.25.3.3

><img width="437" alt="Screenshot 2023-10-11 215421" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/c42c0c4c-3531-4822-9ce2-543dea46e074">


### Soal 20

Karena website **www.parikesit.abimanyu.yyy.com** semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.

**Jawaban**

Konfigurasi Rewrite di `.htaccess`
```
echo "
RewriteEngine On
RewriteCond %{REQUEST_URI} ^/public/images/(.*)(abimanyu)(\.(png|jpg|jpeg|gif|webp))$
RewriteCond %{REQUEST_URI} !/public/images/abimanyu.png
RewriteRule /.* http://parikesit.abimanyu.d07.com/public/images/abimanyu.png [L]" > /var/www/parikesit.abimanyu.d07/.htaccess
```

Konfigurasi VirtualHost:
```
echo "
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/parikesit.abimanyu.d07
    ServerName parikesit.abimanyu.d07.com
    ServerAlias www.parikesit.abimanyu.d07.com

    <Directory /var/www/parikesit.abimanyu.d07>
        Options +FollowSymLinks -Multiviews
        AllowOverride All
    </Directory>

    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
" > /etc/apache2/sites-available/parikesit.abimanyu.d07.com.conf
```

Merestart layanan Apache untuk menerapkan perubahan konfigurasi.
``
service apache2 restart
``

**Testing**

lynx parikesit.abimanyu.d07.com/public/images/not-abimanyu.png

><img width="442" alt="Screenshot 2023-10-11 220124" src="https://github.com/fihrizilhamr/Jarkom-Modul-2-D07-2023/assets/116176265/00f6f014-2f3c-4183-abfb-a249d59208fb">



## Kendala
Tidak ada kendala yang siginifikan dalam pengerjaan praktikum.
