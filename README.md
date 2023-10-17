# Jarkom-Modul-2-D07-2023
## Pendahuluan

Repository ini dibentuk untuk menyelesaikan tugas praktikum Jaringan Komputer.

Anggota Kelompok D07:
| Nama | NRP |
| :---: | :---: |
| Danno Denis Dhaifullah | 5025211027 |
| Fihriz Ilham Rabbany | 5025211040 |

## PEMBAHASAN

### Soal 1

Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan _Load Balancer_ yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian yang sudah ditentukan.

**Jawaban**

### Soal 2

Buatlah _website_ utama pada node arjuna dengan akses ke **arjuna.yyy.com** dengan alias **www.arjuna.yyy.com** dengan yyy merupakan kode kelompok.

**Jawaban**

### Soal 3

Dengan cara yang sama seperti soal nomor 2, buatlah _website_ utama dengan akses ke **abimanyu.yyy.com** dan alias **www.abimanyu.yyy.com**.

**Jawaban**

### Soal 4

Kemudian, karena terdapat beberapa web yang harus di-_deploy_, buatlah subdomain **parikesit.abimanyu.yyy.com** yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

**Jawaban**

### Soal 5

Buat juga _reverse_ domain untuk domain utama. **(Abimanyu saja yang direverse)**

**Jawaban**

### Soal 6

Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

**Jawaban**

### Soal 7

Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu **baratayuda.abimanyu.yyy.com** dengan alias **www.baratayuda.abimanyu.yyy.com** yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

**Jawaban**

### Soal 8

Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses **rjp.baratayuda.abimanyu.yyy.com** dengan alias **www.rjp.baratayuda.abimanyu.yyy.com** yang mengarah ke Abimanyu.

**Jawaban**

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


### Soal 11

Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server **www.abimanyu.yyy.com**. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

**Jawaban**

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
wget --no-check-certificate "https://drive.google.com/uc?id=1pPSP7yIR05JhSFG67RVzgkb-VcW9vQO6" -O download.zip
unzip download.zip
mv rjp.baratayuda.abimanyu.yyy.com /var/www/rjp.baratayuda.abimanyu.d07

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

