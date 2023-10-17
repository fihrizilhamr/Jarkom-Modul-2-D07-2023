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

**Jawaban**

### Soal 10

Kemudian gunakan algoritma **Round Robin** untuk Load Balancer pada **Arjuna**. Gunakan _server_name_ pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan _worker_ yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
- _Prabakusuma:8001_
- _Abimanyu:8002_
- _Wisanggeni:8003_

**Jawaban**

### Soal 11

Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server **www.abimanyu.yyy.com**. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

**Jawaban**

### Soal 12

Setelah itu ubahlah agar url **www.abimanyu.yyy.com/index.php/home** menjadi **www.abimanyu.yyy.com/home**.

**Jawaban**

### Soal 13

Selain itu, pada subdomain **www.parikesit.abimanyu.yyy.com**, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy.

**Jawaban**

### Soal 14

Pada subdomain tersebut folder **/public** hanya dapat melakukan _directory listing_ sedangkan pada folder /secret tidak dapat diakses (_403 Forbidden_).

**Jawaban**

### Soal 15

Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah **_404 Not Found_** dan **_403 Forbidden_**.

**Jawaban**

### Soal 16

Buatlah suatu konfigurasi virtual host agar file asset **www.parikesit.abimanyu.yyy.com/public/js** menjadi 
**www.parikesit.abimanyu.yyy.com/js**.

**Jawaban**

### Soal 17

Agar aman, buatlah konfigurasi agar **www.rjp.baratayuda.abimanyu.yyy.com** hanya dapat diakses melalui port **14000** dan **14400**.

**Jawaban**

### Soal 18

Untuk mengaksesnya buatlah autentikasi username berupa **“Wayang”** dan password **“baratayudayyy”** dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada **/var/www/rjp.baratayuda.abimanyu.yyy**.

**Jawaban**

### Soal 19

Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke **www.abimanyu.yyy.com** (alias)

**Jawaban**

### Soal 20

Karena website **www.parikesit.abimanyu.yyy.com** semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.

**Jawaban**

