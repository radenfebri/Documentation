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










Markdown is a lightweight markup language based on the formatting conventions
that people naturally use in email.
As [John Gruber] writes on the [Markdown site][df1]

> The overriding design goal for Markdown's
> formatting syntax is to make it as readable
> as possible. The idea is that a
> Markdown-formatted document should be
> publishable as-is, as plain text, without
> looking like it's been marked up with tags
> or formatting instructions.

This text you see here is *actually- written in Markdown! To get a feel
for Markdown's syntax, type some text into the left window and
watch the results in the right.

## Tech

Dillinger uses a number of open source projects to work properly:

- [AngularJS] - HTML enhanced for web apps!
- [Ace Editor] - awesome web-based text editor
- [markdown-it] - Markdown parser done right. Fast and easy to extend.
- [Twitter Bootstrap] - great UI boilerplate for modern web apps
- [node.js] - evented I/O for the backend
- [Express] - fast node.js network app framework [@tjholowaychuk]
- [Gulp] - the streaming build system
- [Breakdance](https://breakdance.github.io/breakdance/) - HTML
to Markdown converter
- [jQuery] - duh

And of course Dillinger itself is open source with a [public repository][dill]
 on GitHub.

## Installation

Dillinger requires [Node.js](https://nodejs.org/) v10+ to run.

Install the dependencies and devDependencies and start the server.

```sh
cd dillinger
npm i
node app
```

For production environments...

```sh
npm install --production
NODE_ENV=production node app
```

## Plugins

Dillinger is currently extended with the following plugins.
Instructions on how to use them in your own application are linked below.

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Development

Want to contribute? Great!

Dillinger uses Gulp + Webpack for fast developing.
Make a change in your file and instantaneously see your updates!

Open your favorite Terminal and run these commands.

First Tab:

```sh
node app
```

Second Tab:

```sh
gulp watch
```

(optional) Third:

```sh
karma test
```

#### Building for source

For production release:

```sh
gulp build --prod
```

Generating pre-built zip archives for distribution:

```sh
gulp build dist --prod
```

## Docker

Dillinger is very easy to install and deploy in a Docker container.

By default, the Docker will expose port 8080, so change this within the
Dockerfile if necessary. When ready, simply use the Dockerfile to
build the image.

```sh
cd dillinger
docker build -t <youruser>/dillinger:${package.json.version} .
```

This will create the dillinger image and pull in the necessary dependencies.
Be sure to swap out `${package.json.version}` with the actual
version of Dillinger.

Once done, run the Docker image and map the port to whatever you wish on
your host. In this example, we simply map port 8000 of the host to
port 8080 of the Docker (or whatever port was exposed in the Dockerfile):

```sh
docker run -d -p 8000:8080 --restart=always --cap-add=SYS_ADMIN --name=dillinger <youruser>/dillinger:${package.json.version}
```

> Note: `--capt-add=SYS-ADMIN` is required for PDF rendering.

Verify the deployment by navigating to your server address in
your preferred browser.

```sh
127.0.0.1:8000
```

## License

MIT

**Free Software, Hell Yeah!**

[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen. Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)

   [dill]: <https://github.com/joemccann/dillinger>
   [git-repo-url]: <https://github.com/joemccann/dillinger.git>
   [john gruber]: <http://daringfireball.net>
   [df1]: <http://daringfireball.net/projects/markdown/>
   [markdown-it]: <https://github.com/markdown-it/markdown-it>
   [Ace Editor]: <http://ace.ajax.org>
   [node.js]: <http://nodejs.org>
   [Twitter Bootstrap]: <http://twitter.github.com/bootstrap/>
   [jQuery]: <http://jquery.com>
   [@tjholowaychuk]: <http://twitter.com/tjholowaychuk>
   [express]: <http://expressjs.com>
   [AngularJS]: <http://angularjs.org>
   [Gulp]: <http://gulpjs.com>

   [PlDb]: <https://github.com/joemccann/dillinger/tree/master/plugins/dropbox/README.md>
   [PlGh]: <https://github.com/joemccann/dillinger/tree/master/plugins/github/README.md>
   [PlGd]: <https://github.com/joemccann/dillinger/tree/master/plugins/googledrive/README.md>
   [PlOd]: <https://github.com/joemccann/dillinger/tree/master/plugins/onedrive/README.md>
   [PlMe]: <https://github.com/joemccann/dillinger/tree/master/plugins/medium/README.md>
   [PlGa]: <https://github.com/RahulHP/dillinger/blob/master/plugins/googleanalytics/README.md>
