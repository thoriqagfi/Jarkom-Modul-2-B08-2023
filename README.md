# Jarkom-Modul-1-B08-2023

|Nama|NRP|Kelas|
|:--:|:-:|:---:|
|Adrian Karuna Soetikno|5025211019|Jarkom B|
|Thariq Agfi Hermawan|5025211215|Jarkom B|
------------------------------------------


## Soal 1
### Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut

Pertama, kita lakukan konfigurasi IP dari setiap server
#### Pandudewanata
Image1

### Switch 1
----------------------------------------------------------------
#### Nakula
Image

#### Sadewa
Image

#### Yudhistira

#### Werkudara



### Switch 2
----------------------------------------------------------------
#### Prabukusuma

#### Abimanyu

#### Wisanggeni



Setelah konfigurasi berhasil, kita mendeklarasikan iptables pada .bashrc

```bash
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.182.0.0/16
```

Selanjutnya, kita masukkan ```nameserver 192.168.122``` ke ```/etc/resolv.conf``` untuk server yang memerlukan internet :

```bash
echo nameserver 192.168.122.1 >> /etc/resolv.conf
```

## Soal 2
### Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.

DNS master 	  : Yudhistira
DNS Slave		  : Werkudara
Load Balancer : Arjuna
Web Server 		: Prabakusuma, Abimanyu, dan Wisanggeni

Pada Yudhistira (DNS Master) kita akan lakukan instalasi bind9 dan konfigurasi sebagai berikut :

- Instalasi bind9

```bash
apt-get update
apt-get install bind9 -y
service bind9 start
```

- Konfigurasi /etc/bind/named.conf.local
```conf
zone "arjuna.B08.com" {
type master;
file "/etc/bind/jarkom/arjuna.B08.com";
};

zone "abimanyu.B08.com" {
type master;
file "/etc/bind/jarkom/abimanyu.B08.com";
};
```

## Soal 3
### Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.

- Make directory jarkom pada /etc/bind
- Konfigurasi /etc/bind.jarkom/arjuna.B08.com
- Konfigurasi /etc/bind/jarkom/abimanyu.B08.com

Hasil :

## Soal 4
### Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

Hasil :


## Soal 5
### Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)

Untuk melakukan reverse domain, kita lakukan konfigurasi pada domain reverse address => /etc/bind/[1 atau 2].182.192.in-addr.arpa dan named.conf.local

Sebelum melakukan konfigurasi, kita lakukan copy file db.local lebih dulu :

```bash
cp /etc/bind/db.local /etc/bind/jarkom/1.182.192.in-addr.arpa
cp /etc/bind/db.local /etc/bind/jarkom/2.182.192.in-addr.arpa
```

Selanjutnya
- Edit named.conf.local
- Edit /etc/bind/[1 atau 2].182.192.in-addr.arpa
  - Arjuna
    ```conf
    zone "1.182.192.in-addr.arpa" {
      type master;
      file "/etc/bind/jarkom/1.182.192.in-addr.arpa";
    };
    ```

  - Abimanyu
  ```conf
    zone "2.182.192.in-addr.arpa" {
      type master;
      file "/etc/bind/jarkom/2.182.192.in-addr.arpa";
    };
    ```
Jangan lupa untuk melakukan service bind9 restart. Kita akan
melakukan test di client (Sadewa). Hasilnya adalah

## Soal 6
### Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

Untuk melakukan DNS Slave, kita melakukan konfigurasi pada DNS Master (Yudhistira) di named.conf.local

Selanjutnya, kita lakukan konfigurasi pada Server Slave (Werkudara) sebagai berikut
```bash
apt-get update
apt-get install bind9 -y
service bind9 start
```

Setelahnya, kita konfigurasi named.conf.local pada Werkudara :

Untuk melakukan test, kita mematikan service pada Yudhistira terlebih dulu, kemudian kita lakukan test pada Client server


## Soal 7
### Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

Kita menambahkan

```bash
ns1        IN    NS    192.182.1.5
baratayuda IN    NS    ns1
```

Ini merupakan tahapan untuk mendelegasikan baratayuda.B08.com ke IP Werkudara. Selanjutnya, tak lupa kita menambahkan /etc/bind/named.conf.options

```bash
allow-query(any;);
```

Setelah itu, kita lakukan konfigurasi pada server Werkudara dengan menambahkan named.conf.local

Kemudian, kita lakukan konfigurasi pada /etc/bind/delegasi/baratayuda.abimanyu.B08.com

Selanjutnya, kita lakukan service bind9 restart pada kedua server.
Untuk melakukan testing, kita lakukan ping dari server client

Kita berhasil melakukan ping pada baratayuda.abimanyu.B08.com dan www.baratayuda.abimanyu.B08.com


## Soal 8
### Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

Untuk menambahkan subdomain rjp, kita hanya perlu menambahkannya di /etc/bind/delegasi/baratayuda.abimanyu.B08.com

Hasil :

## Soal 9
### Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai web server) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

Pertama, kita melakukan instalasi nginx dan lynx pada masing-masing worker dan load balancer

```bash
apt-get update
apt-get install nginx -y
service nginx start
```

Kemudian, kita juga coba mengakses masing-masing localhost dengan lynx

- Prabukusuma

- Abimanyu

- Wisanggeni

## Soal 10
### Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003.

Kita konfigurasi nginx menggunakan algoritma round robin. Pertama, kita akses server arjuna dan konfigurasi di /etc/nginx/sites-available/lb-jarkom dengan syntax

Selanjutnya, kita buat symlink yang mengkoneksikan antara sites-available dan sites-enabled

```bash
ln -s /etc/nginx/sites-available/arjuna.b13.com /etc/nginx/sites-enabled
```

Setelah itu, kita melakukan konfigurasi pada 3 worker server, yaitu Prabukusuma, Abimanyu, Wisanggeni. Sebelum melakukan konfigurasi, kita insatll tools php dan php-fpm

```bash
apt-get install php php-fpm -y
service php7.0-fpm start
```

Selanjutnya, untuk konfigurasi nginx pada 3 server worker

```conf
server {
  Listen 8001; (Tergantung port yang sudah di config antara 8001, 8002 dan 8003)

  root /var/www/jarkom;

  index index.php index.html index.htm
  server_name _;
  location / {
      try_files $uri /index.php?$query_string;
  }
  location ~ \.php$ {
      include snippets/fastcgi-php.conf;
      fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
  }
  location ~ /\.ht {
      deny all;
  }
  error_log /var/log/nginx/jarkom_error.log;
  access_log /var/log/nginx/jarkom_access.log;
}
```

Selanjutnya, kita cek di client apakah sudah berhasil atau belum dengan lynx localhost masing-masing server worker

- Prabukusuma

- Abimanyu

- Wisanggeni


## Soal 11
### Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

Pertama, kita install apache2 pada worker Abimanyu

```bash
apt-get installapache2
```

Selanjutnya kita download unzip untuk download resources dari yang disediakan dan buat folder di /var/www/abimanyu.B08 dan pindah hasil download ke folder tersebut.

Setelah selesai kita perlu setting DocumentRoot pada webserver ini untuk mengarah ke /var/www/abimanyu.B08 pertama-tama masuk ke directory cd /etc/apache2/sites-available dan edit file 000-default.conf dan tambahkan script berikut

```bash
ServerName www.abimanyu.B08.com
DocumentRoot /var/www/html
```

Setelah selesai kita coba restart apache2 nya dan kita coba testing dengan cara lynx arjuna.B08.com/home.html di server abimanyu tersebut

