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

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/36468ad9-72cc-4d16-b785-f2fc351e47ed)

### Switch 1
----------------------------------------------------------------
#### Nakula

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/a1697087-20cf-49ee-98d8-0ed0fb9c423f)

#### Sadewa

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/f4b114c3-c6dc-4ae0-a742-50a41c5c34db)

#### Yudhistira

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/a51e81cd-4a55-40d8-9ffa-5c13735baad2)

#### Werkudara

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/0e2d7cfb-8dd7-4ca7-8451-127a1e5b1efa)

### Switch 2
----------------------------------------------------------------
#### Arjuna

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/739c058f-fdc8-469e-98a8-e1a2b9f16e2e)

### Switch 3
----------------------------------------------------------------
#### Prabukusuma

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/214feb3b-e12b-41f3-91e3-5ee89b29572b)

#### Abimanyu

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/50611f57-56e9-439c-8c1d-6e40467a54dc)

#### Wisanggeni

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/d065ad0f-e80f-406f-8cd7-55a602421da5)

Setelah konfigurasi berhasil, kita mendeklarasikan iptables pada .bashrc

```bash
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.182.0.0/16
```

Selanjutnya, kita masukkan ```nameserver 192.168.122``` ke ```/etc/resolv.conf``` untuk server yang memerlukan internet :

```bash
echo nameserver 192.168.122.1 >> /etc/resolv.conf
```

Jika konfigurasi berhasil, maka :

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/1d260f1f-bf8d-4837-a20c-577170b464d9)

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

  ![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/c69e79f6-95f2-40a1-b52d-2b701b71176f)

- Konfigurasi /etc/bind/jarkom/abimanyu.B08.com

  ![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/de4b5796-833e-4e54-a4d0-075064659a04)

Hasil :

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/c2a3db2f-bd17-4d44-91b5-4aacd395e73f)

## Soal 4
### Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/a15f1ff6-9a11-43ef-8c3a-3cb5bf53641e)

Hasil :

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/1ea55e14-0846-49f6-af8a-e99996a1f5d8)

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

  ![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/c4d5b42e-02f4-4187-b2b4-736d9a5a0652)

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

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/7b2041fe-8361-4fda-a9e4-36504aa0254e)

## Soal 6
### Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

Untuk melakukan DNS Slave, kita melakukan konfigurasi pada DNS Master (Yudhistira) di named.conf.local

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/771cee11-1622-4d5a-9f57-84572e3d2637)

Selanjutnya, kita lakukan konfigurasi pada Server Slave (Werkudara) sebagai berikut
```bash
apt-get update
apt-get install bind9 -y
service bind9 start
```

Setelahnya, kita konfigurasi named.conf.local pada Werkudara :

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/088cf22d-1013-4aea-a00c-6cca516ac805)

Untuk melakukan test, kita mematikan service pada Yudhistira terlebih dulu, kemudian kita lakukan test pada Client server

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/5820e24e-2b4c-4b84-ba55-855f257c25ae)

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/4bbba51a-1a3a-4326-942b-6531ea9627e8)

## Soal 7
### Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

Kita menambahkan

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/4614754e-206d-4c1b-a8c1-d675b2b94d23)

```bash
ns1        IN    NS    192.182.1.5
baratayuda IN    NS    ns1
```

Ini merupakan tahapan untuk mendelegasikan baratayuda.B08.com ke IP Werkudara. Selanjutnya, tak lupa kita menambahkan /etc/bind/named.conf.options

```bash
allow-query(any;);
```

Setelah itu, kita lakukan konfigurasi pada server Werkudara dengan menambahkan named.conf.local

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/5d9f5435-114f-4821-b0e7-d30540c34439)

Kemudian, kita lakukan konfigurasi pada /etc/bind/delegasi/baratayuda.abimanyu.B08.com

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/7a70ca69-2925-4ab1-a7af-0717003ac28d)

Selanjutnya, kita lakukan service bind9 restart pada kedua server.
Untuk melakukan testing, kita lakukan ping dari server client

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/11c63c3c-2c84-40b5-9875-5162494befc7)

Kita berhasil melakukan ping pada baratayuda.abimanyu.B08.com dan www.baratayuda.abimanyu.B08.com


## Soal 8
### Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

Untuk menambahkan subdomain rjp, kita hanya perlu menambahkannya di /etc/bind/delegasi/baratayuda.abimanyu.B08.com

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/26d79f18-31ac-4306-aa48-26ea02cd8b2a)

Hasil :

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/4490db90-a982-4855-971a-059e70cc9e1f)

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

  ![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/4104e605-a2d9-49a4-b8a5-9051cd08cc2b)

- Abimanyu

  ![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/5f38a69d-d0c0-41a9-9c69-a7f5d5d82ec8)

- Wisanggeni

  ![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/886ec062-7448-4a6b-b577-6f63ebb98ce4)

## Soal 10
### Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003.

Kita konfigurasi nginx menggunakan algoritma round robin. Pertama, kita akses server arjuna dan konfigurasi di /etc/nginx/sites-available/lb-jarkom dengan syntax

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/9e510fb9-f50c-4c2e-930b-95a0156587ee)

Selanjutnya, kita buat symlink yang mengkoneksikan antara sites-available dan sites-enabled

```bash
ln -s /etc/nginx/sites-available/arjuna.B08.com /etc/nginx/sites-enabled
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

  ![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/c7f0be0a-b56b-4520-b4e1-2c16bcd3021d)

- Abimanyu

  ![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/0cf31910-d7cf-4da3-8a03-d616245cc866)

- Wisanggeni

  ![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/15387c25-bae1-475f-acf2-6db1d49c20b2)


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

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/7dad5d2a-8858-40cb-8dba-f917f34a42ae)

## Soal 12
### Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.

Untuk membuat alias baru, maka kita harus merubah config apache2 dari abimanyu, dan menambahkan line dibawah

```bash
 <Directory /var/www/abimanyu.B08>
     Options +Indexes
 </Directory>

 Alias "/home" "/var/www/abimanyu.B08/index.php/home"
```

<img width="693" alt="Screenshot 2023-10-15 at 13 24 36" src="https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/86884506/a4eff642-7312-4f21-a529-649cdec21ef1">

hasil :

<img width="650" alt="Screenshot 2023-10-15 at 13 27 23" src="https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/86884506/a6cba9ba-f092-4c00-b2a9-a9f349ff7c86">


## Soal 13
### Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy

untuk ini kita harus membuat config untuk parikesit.abimanyu.B08.com

```bash
<VirtualHost *:80 >
    	ServerAdmin webmaster@localhost
      ServerName parikesit.abimanyu.B08.com
    	DocumentRoot /var/www/pariketsit.abimanyu.B08
    	ServerAlias www.parikesit.abimanyu.B08.com

      <Directory /var/www/parikesit.abimanyu.B08.com>
       Options +Indexes
       </Directory>

     Alias "/home" "/var/www/parikesit.abimanyu.B08/index.php/home"

    	ErrorLog ${APACHE_LOG_DIR}/rjp_baratayuda_error.log
    	CustomLog ${APACHE_LOG_DIR}/rjp_baratayuda_access.log combined
</VirtualHost>
```

lalu lakukan service restart apache2 dan a2ensite apabila diperlukan.
dan hasilnya adalah :

<img width="710" alt="Screenshot 2023-10-15 at 13 13 18" src="https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/86884506/ddb60825-3b79-4e7c-99f2-877d950e93aa">



## Soal 14
### Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).

maka kita perlu menambahkan pada config parikesit, dimana kita perlu menambahkan +indexes dan -Indexes untuk secret nya agar tidak terlihat

```bash
<VirtualHost *:80 >
    	ServerAdmin webmaster@localhost
      ServerName parikesit.abimanyu.B08.com
    	DocumentRoot /var/www/pariketsit.abimanyu.B08
    	ServerAlias www.parikesit.abimanyu.B08.com

      <Directory /var/www/parikesit.abimanyu.B08.com>
       Options +Indexes
       </Directory>
      <Directory /var/www/parikesit.abimanyu.B08/public>
       Options +Indexes
       </Directory>
      <Directory /var/www/pariketsit.abimanyu.B08/secret>
       Options -Indexes
       </Directory>

     Alias "/home" "/var/www/parikesit.abimanyu.B08/index.php/home"

    	ErrorLog ${APACHE_LOG_DIR}/rjp_baratayuda_error.log
    	CustomLog ${APACHE_LOG_DIR}/rjp_baratayuda_access.log combined
</VirtualHost>
```

maka hasilnya ketika di lynx adalah :

/public

<img width="637" alt="Screenshot 2023-10-15 at 14 00 05" src="https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/86884506/5666e3af-a515-486e-a240-d1749314fa34">


/secret

<img width="511" alt="Screenshot 2023-10-15 at 13 58 13" src="https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/86884506/a2b9466c-3824-4969-b03a-9c9a0c75db5e">

## Soal 15
### Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.

Untuk melakukan kustomisasi error, kita harus menambahkan pada /etc/apache2/sites-available/parikesit.abimanyu.B08.com

```bash
ErrorDocument 404 /error/404.html
ErrorDocument 403 /error/403.html

```

hingga confignya akan menjadi

```bash
<VirtualHost *:80 >
    	ServerAdmin webmaster@localhost
      ServerName parikesit.abimanyu.B08.com
    	DocumentRoot /var/www/pariketsit.abimanyu.B08
    	ServerAlias www.parikesit.abimanyu.B08.com

      <Directory /var/www/parikesit.abimanyu.B08.com>
       Options +Indexes
       </Directory>
      <Directory /var/www/parikesit.abimanyu.B08/public>
       Options +Indexes
       </Directory>
      <Directory /var/www/pariketsit.abimanyu.B08/secret>
       Options -Indexes
       </Directory>

      Alias "/home" "/var/www/parikesit.abimanyu.B08/index.php/home"
      ErrorDocument 404 /error/404.html
      ErrorDocument 403 /error/403.html

    	ErrorLog ${APACHE_LOG_DIR}/rjp_baratayuda_error.log
    	CustomLog ${APACHE_LOG_DIR}/rjp_baratayuda_access.log combined
</VirtualHost>
```

hasilnya :

<img width="648" alt="Screenshot 2023-10-15 at 14 02 57" src="https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/86884506/8221ed86-d828-43c7-8131-66f6a51e533c">

<img width="642" alt="Screenshot 2023-10-15 at 14 03 08" src="https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/86884506/288caa4d-c882-406c-8e7e-f0edc9898d9b">


## Soal 16
### Buatlah suatu konfigurasi virtual host agar file asset www.parikesit.abimanyu.yyy.com/public/js menjadi www.parikesit.abimanyu.yyy.com/js

Untuk membuat nama alias, kita dapat menambahkan script :

```bash
Alias "/js" "/var/www/parikesit.abimanyu.B08/public/js"
```

Hasil :

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/f0e43118-7714-437a-ae04-b32d0bc98f96)


## Soal 17
### Agar aman, buatlah konfigurasi agar www.rjp.baratayuda.abimanyu.yyy.com hanya dapat diakses melalui port 14000 dan 14400.

Pertama, kita pindahkan source dari soal ke /var/www/rjp.baratayuda.abimanyu.B08

```bash
<VirtualHost *:14000 *:14400 >
    	ServerName rjp.baratayuda.abimanyu.B08.com

    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/rjp.baratayuda.abimanyu.B08
    	ServerAlias www.rjp.baratayuda.abimanyu.B08.com

    	ErrorLog ${APACHE_LOG_DIR}/rjp_baratayuda_error.log
    	CustomLog ${APACHE_LOG_DIR}/rjp_baratayuda_access.log combined
</VirtualHost>
```

Kemudian, kita tambahkan port di /etc/apache2/ports.conf

```bash
Listen 14000
Listen 14400
```

Selanjutnya, kita cek apakah port 14400 dan 14000 sudah terbuka dengan
```bash
netstat -nltp | grep apache
```

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/4c5c401a-fd2c-453e-948e-d457999b29bb)

Hasil :

![image](https://github.com/thoriqagfi/Jarkom-Modul-2-B08-2023/assets/92865110/d43e6a79-f8bc-4dd0-b511-ca66ad8c709e)


## Soal 18
### Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.

Pertama, kita install apache2 dan apache2-utils

```bash
sudo apt-get update
sudo apt-get install apache2 apache2-utils
```

Kemudian, kita buat username dan password dengan

```bash
htpasswd -nb Wayang baratayudab08 > /etc/apache2/.htpasswd
```

Kemudian masukan password baratayudaB08 dan tambahkan script di sites-available/rjp.baratayuda.abimanyu.B08 dengan

```bash
      <Directory />
            	Options FollowSymLinks
            	AllowOverride None
    	</Directory>
    	<Directory /var/www/rjp.baratayuda.abimanyu.B08.com>
            	AuthType Basic
            	AuthName "Restricted Content"
            	AuthUserFile /etc/apache2/.htpasswd
            	Require valid-user
    	</Directory>
```


## Soal 19
### Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke www.abimanyu.yyy.com (alias)

Kita ubah httaccess untuk forward ke domain tersebut, tetapi kita dulu a2enmod rewrite sudah menyalah dengan cara menjalankan

```bash
a2enmod rewrite
```

Setelah itu kita setting .htaccess menjadi sebagai berikut sebagai berikut :

```bash
RewriteEngine On
RewriteBase /
RewriteCond %{HTTP_HOST} ^192\.182\.3\.3$
RewriteRule ^(.*) http://www.abimanyu.B08.com/$1 [L,R=301]
```

Kemudian tambahakan script berikut ini untuk menjalankan .

```bash
 # Soal No 19
    	<Directory /var/www/abimanyu.B08.com>
            	Options +FollowSymLinks -Multiviews
            	AllowOverride All
    	</Directory>
```


## Soal 20
### ## Soal 19
### Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke www.abimanyu.yyy.com (alias)

Pertama, kita tambahkan .htaccess pada /var/www/parikesit.abimanyu.B08.com kemudian tambahkan syntax dibawah ini

```bash
RewriteEngine On
RewriteBase /

# Check if the request is not already /abimanyu.png
RewriteCond %{REQUEST_URI} !^/abimanyu.png$ [NC]

# Redirect requests for images containing "abimanyu" to abimanyu.png
RewriteCond %{REQUEST_URI} abimanyu [NC]
RewriteRule \.(jpg|jpeg|png|gif)$ http://www.parikesit.abimanyu.B08.com/abimanyu.png [L,R=301]
```

Kemudian tambahakn alias dengan cara ubah parikesit.abimanyu.B08.com.conf menjadi

```bash
<VirtualHost *:80>
    	ServerName parikesit.abimanyu.B08.com

    	ErrorDocument 404 /error/404.html
    	ErrorDocument 403 /error/403.html

    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/parikesit.abimanyu.B08.com
    	ServerAlias www.parikesit.abimanyu.B08.com

    	# Soal No 14
    	<Directory /var/www/abimanyu.B08.com/public>
            	Options +Indexes
    	</Directory>

    	<Directory /var/www/parikesit.abimanyu.B08.com/secret>
            	Options -Indexes
    	</Directory>

    	# SOal No 16
    	Alias "/js" "/var/www/parikesit.abimanyu.B08.com/public/js"

    	# Soal No 20
     	<Directory /var/www/parikesit.abimanyu.B08.com>
            	Options +FollowSymLinks -Multiviews
            	AllowOverride All
    	</Directory>
    	Alias "/abimanyu.png" "/var/www/parikesit.abimanyu.B08.com/public/images/abimanyu.png"

    	ErrorLog ${APACHE_LOG_DIR}/parikesit_error.log
    	CustomLog ${APACHE_LOG_DIR}/parikesit_access.log combined
</VirtualHost>
```