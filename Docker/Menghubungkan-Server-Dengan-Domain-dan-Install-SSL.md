# Menghubungkan Server dengan Domain dan Install SSL pada Docker

## Video Tutorial Lengkap di Channel Yutube Raden Febri
[![N|Solid](https://img.shields.io/badge/YouTube-FF0000?style=for-the-badge&logo=youtube&logoColor=white)](https://youtube.com/@RadenFebri)

Pada tutorial ini saya memberikan tutorial cara menghubungkan Cloud Server dengan Domain yang kalian miliki dan juga saya memberikan tutorial cara untuk install SSL dengan mudah, penerapan dengan Docker COntainer. Berikut adalah langkah-langkah installasinya:


## Konfigurasi Server dan DNS Record

Sebelumnya kalian harus memiliki Cloud Server dan sekaligus alamat IP Pulic untuk dihubungkan dengan Domain yang sudah dan jangan lupa beli Domain terlebih dahulu ya. Berikut preview IP Public dan Domain yang sudah saya miliki.

Contoh IP Public / Server:
![image](https://user-images.githubusercontent.com/56567941/214229148-8974f737-fe0c-4f9e-a1b2-7c672df271a7.png)

Contoh Domain:
![image](https://user-images.githubusercontent.com/56567941/214229435-aa5bf96b-5d72-44da-a9f8-baf0da154983.png)


Langkah Pertama salin terlebih dahulu ip public dan kemudian buka pada bagian domain Records kemudian kalian bisa tambahahkan seperti gambar di bawah ini:
![image](https://user-images.githubusercontent.com/56567941/214229893-c2248c87-4506-4f0d-a123-c9f86caa7358.png)

Jadi Penjelasannya begini kenapa kita menambahkan Records seperti diatas:
* Type = A yang berarti Menghubungkan Domain ke alamat IP.
* Name = @ yang berarti Menunjukkan bahwa IP atau Hostname Server.
* Value = IP Server yang berarti Domain akan dihubungkan dengan Server yang sudah ada tersebut melalui IP Public.
* TTL = 3600 TTL yang berarti (Time to Live) nilai yang ditentukan dalam sebuah record DNS yang menunjukkan berapa lama record tersebut dipertahankan dalam cache oleh resolver DNS. Nilai TTL ditentukan dalam hitungan detik. Jika sebuah record memiliki TTL 3600, itu berarti bahwa resolver DNS yang menyimpan record tersebut harus menyimpannya selama 3600 detik (atau 1 jam) sebelum mengambil data baru dari server DNS utama.

Langkah Kedua kalian bisa cek menggunakan website [dnschecker.org](https://dnschecker.org/), kalian bisa cek dengan memilih opsi A Record.
![image](https://user-images.githubusercontent.com/56567941/214231750-d77ddef7-a5fa-4acd-a476-0dd982600b4e.png)

Gambar diatas menggambarkan bahwasannya Domain dengan Server kita sudah tehubung, dan proses propagasi ini membutuhkan kurang lebih 1x24 jam.




## Membuat Folder Seperti Sekema Berikut
Untuk sekema folder berikut ini yakni bersifat opsional, kalian bisa taruh di bagian mana saja.

```
🏠 /home/radenfebri/
├───📂 laravel
│    ├──📄 docker-compose.yaml
│    └──📄 Dockerfile
│    ├── 📂 nginx
│    │    └──📄 default.conf
│    ├───📂 mysql
│    ├───📂 src
│    │    └──📄 ...
│    └───📂 cert
│         └──📄 ...
```

Berkut langkah-langkah membuat folder cert:
masuk sebagau super user / root
```
sudo su
```
masuk kedalam direktori /home/radenfebri/
```
cd /home/radenfebri
```
membuat folder dengan nama cert
```
mkdir cert
```
masuk kedalam folder laravel yang sudah di buat
```
cd cert
```
kemudian didalam folder cert (/home/radenfebri/laravel/cert) kita buat certificate dengan perintah seperti di bawah ini:
```
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout localhost.key -out localhost.crt
```
untuk penamaan certificate .key dan .crt tidak harus menggunakan localhost, dan kalian bisa sesuaikan sendiri


## Membuat File dalam Folder laravel (docker-compose.yaml)
Perintah di bawah ini yakni membuat file sekaligus edit file yang baru dibuat
```
nano docker-compose.yaml
```

Kemudian isikan kode berikut pada bagian dalam file docker-compose.yaml
```
version: '3.9'

networks:
  laravel:

services:
  nginx:
    container_name: nginx
    image: nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./src:/home/radenfebri/laravel
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
      - ./cert/:/etc/nginx/conf.d
    networks:
      - laravel

  php:
    container_name: php
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "9000:9000"
    volumes:
      - ./src:/home/radenfebri/laravel
    networks:
      - laravel

  mysql:
    container_name: mysql
    image: mysql:5.7
    ports:
      - "3306:3306"
    volumes:
      - ./mysql:/var/lib/mysql
    environment:
      MYSQL_DATABASE: laravel
      MYSQL_PASSWORD: password
      MYSQL_ROOT_PASSWORD: password
    networks:
      - laravel

  phpmyadmin:
    container_name: phpmyadmin
    image: phpmyadmin
    restart: always
    ports:
      - 8080:80
    environment:
      - PMA_ARBITRARY=1

volumes:
  mysql:
```
Apabila sudah selesai kemudian lakukan save pada file tersebut

## Membuat File dalam Folder laravel (Dockerfile)
Dockerfile ini berfungsi untuk menentukan bagaimana sebuah image Docker harus dibangun. Ia menyimpan instruksi seperti apa yang harus diinstal, bagaimana konfigurasi sistem harus diatur, dan bagaimana aplikasi harus dijalankan ketika container dijalankan.
```
nano Dockerfile
```
Kemudian isikan baris kode berikut ini dalam file Dockerfile ini
```
FROM php:8.1-fpm-alpine
RUN docker-php-ext-install pdo pdo_mysql
```
Apabila sudah selesai kemudian lakukan save pada file tersebut

## Membuat File dalam Folder nginx (default.conf)
Pertama masuk kedalam direktori nginx terlebih dahulu
```
cd /home/radenfebri/laravel/nginx
```
membuat folder default.conf
```
nano default.conf
```
Kemudian isikan kode berikut ini dalam file default.conf
```
server {
    listen 80;
    index index.php index.html;
    server_name youtube-radenfebri.my.id;
    root /home/radenfebri/laravel/public;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}


server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    index index.php index.html;
    server_name youtube-radenfebri.my.id;
    root /home/radenfebri/laravel/public;

    ssl_certificate /etc/nginx/conf.d/rfcrt.crt;
    ssl_certificate_key /etc/nginx/conf.d/rfkey.key;

    ssl_protocols TLSV1.2 TLSv1.1 TLSV1;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```
Apabila sudah selesai kemudian lakukan save pada file tersebut

## Membuat Website dari Framework Laravel
Masuk terlebih dahulu dalam folder laravel
```
cd /home/radenfebri/laravel
```
Kemudian jalankan perintah berikut ini untuk menginstall dependensi dan package yang dibutuhkan dalam pembuatan website dari framework laravel ini. --prefer-dist disini yang berarti, nantinya laravel akan di install di dalam folder src
```
composer create-project --prefer-dist laravel/laravel src
```
## Menjalankan File docker-compose.yaml
Berikut cara menjalankan file docker-compose.yaml & pastikan sekarang sudah berada di dalam direktori (/home/radenfebri/laravel)
```
docker-compose up -d
```
## Memberiakan Hak akses kedalam DIrektori
Tuajuannya agar file berikut ini bisa diakses oleh public
```
chmod 777 -R /home/radenfebri/laravel/src/storage
chmod 777 -R /home/radenfebri/laravel/src/public
```
## Mencoba Migrate data ke Database

Lakukan perintah migrate untuk mencoba mengirimkan data ke dalam database yang sudah kita buat sebelumnya dnegan menggunakan Docker Container. masuk ke direktori /home/radenfebri/laravel/
```
docker-compose exec php php /home/radenfebri/laravel/artisan migrate
```
> Catatan: `Apabila pada documentasi ini ada kesalahan, silahkan bantu untuk membenarkan kalian bisa langsung melakukan pull request` Saya ucapkan Terimakasih.

