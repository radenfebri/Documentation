# Install Project Laravel di Server Cloud (Linux)


[![N|Solid](https://img.shields.io/badge/Laravel-FF2D20?style=for-the-badge&logo=laravel&logoColor=white)](https://laravel.com/)

[![N|Solid](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)](#)

## Video Tutorial Lengkap di Channel Yutube Raden Febri
[![N|Solid](https://img.shields.io/badge/YouTube-FF0000?style=for-the-badge&logo=youtube&logoColor=white)](https://laravel.com/)

Pada tutorial ini saya melakukan installasi laravel di Cloud dan penerapan menggunakan Docker Container sebagai Pondasinya. Berikut adalah langkah-langkah installasinya:
- Install PHP & Package PHP
- Install Composer
- Membuat Direktori
- 

## Installation PHP & Package PHP

Cara menginstall PHP dan Package yang dibutuhkan.
Kalian bisa lihat versi PHP yang terbaru melalui link berikut [php.net](https://www.php.net/releases/8.2/en.php).


```
sudo apt-get install curl php-cli php-mbstring git unzip
```

```
sudo apt install php8.1-common php8.1-mysql php8.1-xml php8.1-xmlrpc php8.1-curl php8.1-gd php8.1-imagick php8.1-cli php8.1-dev php8.1-imap php8.1-mbstring php8.1-opcache php8.1-soap php8.1-zip php8.1-redis php8.1-intl -y
```

## Install Composer
Cara menginstall Composer untuk kebutuhan intall framework Laravel.
Untuk lebih lanjut kalian bisa lihat website [getcomposer.org](https://getcomposer.org/).

```
sudo curl –sS https://getcomposer.org/installer | php
```
```
sudo mv composer.phar /usr/local/bin/composer
```
```
sudo apt update
```

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
│    └───📂 src
│         └──📄 ...
```

Berkut langkah-langkah membuat folder dan file nya:
masuk sebagau super user / root
```
sudo su
```
masuk kedalam direktori /home/radenfebri/
```
cd /home/radenfebri
```
membuat folder dengan nama laravel
```
mkdir laravel
```
masuk kedalam folder laravel yang sudah di buat
```
cd laravel
```
kemudian didalam folder laravel (/home/radenfebri/laravel/) buatlah folder seperti di bawah ini:
```
mkdir nginx
mkdir mysql
mkdir src
```
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
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./src:/home/ubuntu/laravel
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
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
      - ./src:/home/ubuntu/laravel
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
    server_name localhost;
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
## Menjalankan file docker-compose.yaml
Berikut cara menjalankan file docker-compose.yaml & pastikan sekarang sudah berada di dalam direktori (/home/radenfebri/laravel)
```
docker compose-up -d
```
## Memberiakan Hak akses kedalam folder
Tuajuannya agar file berikut ini bisa diakses oleh public
```
chmod 777 -R /home/radenfebri/laravel/src/storage
chmod 777 -R /home/radenfebri/laravel/src/public
```

> Catatan: `Apabila pada documentasi ini ada kesalahan, silahkan bantu untuk membenarkan kalian bisa langsung melakukan pull request` Saya ucapkan Terimakasih.

