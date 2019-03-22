Cũng giống như việc setup jenkins cho reactjs, việc setup jenkins cho php cũng tương tự.
Nhưng đối với php làm backend thì cần phải setup thêm 1 số cái như cho database. 
Bài viết này mình coi như các bạn đã biết dùng jenkins, bài này chỉ hướng dẫn cách setup jenkins cho php.

Việc biết docker và bash shell là yêu cầu. Nên cần tìm hiểu 2 docker và bash shell trước khi đến với bài viết này.

Việc setup Jenkins cho php nó cũng giống như việc bạn vừa clone 1 project từ github hay gitlab hay bất kỳ nơi nào về và sau đó bạn làm các bước để chạy được con php server đó.

Các bước thường thực hiện cho 1 project php vừa mới clone về thường sẽ như sau:

+ pull các thư viện về: `composer install`

+ copy file  .env.example sang .env và config file env

+ tạo key generate

+ php artisan serve (default port 8000) or php artisan —port=port/8000


Dưới đây là 1 ví dụ:

**Ở trong `docker-compose.yml` file sẽ như sau:**

```sh
version: '3.4'
services:
  web:
      build:
          context: ../..
          dockerfile: ./.cicd/docker/web/Dockerfile
      depends_on:
        - mysql
      links:
        - mysql
      environment:
        - COMPOSER_ALLOW_SUPERUSER=1
        - MYSQL_USER=root
        - MYSQL_DATABASE=insight
        - DB_CONNECTION=mysql
        - DB_HOST=mysql
        - DB_PORT=3306
        - DB_DATABASE=insight
        - DB_USERNAME=root
        - DB_PASSWORD=
        - MAIL_DRIVER=log
  mysql:
      image: 'mysql:5.7'
      environment:
      - MYSQL_ALLOW_EMPTY_PASSWORD=yes
      - MYSQL_DATABASE=insight

```

* Ở đây mình khai báo 1 service là `web`

* trong `build` mình chỉ định path dockerfile và context

* `depends_on`: Chọn các service được dùng là dependency cho container được xác định trong service hiện tại.

* `links`:	Liên kết service này với bất kỳ service nào khác trong Docker Compose file bằng các chỉ rõ tên.

* `environment`: Định nghĩa các biến môi trường truyền vào Docker khi chạy command

* `image`: Chỉ ra image được sử dụng để build container. Sử dụng directive với các `image` chỉ định trên host mechine hoặc trên Docker Hub.

**Ở `Dockerfile`:**

```sh
FROM php:7.1
RUN apt-get update -y && apt-get install -y libmcrypt-dev openssl git wget zip unzip gnupg mysql-client # install update

WORKDIR /app
COPY . /app

RUN docker-php-ext-install pdo mcrypt pdo_mysql
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN cp -rf .env.example .env
RUN composer global require hirak/prestissimo   #composer parallel install plugin
RUN composer install # pull library
RUN php artisan key:generate   #generate key

RUN ["chmod", "+x", "./.cicd/docker/web/wait-for-mysql.sh"]
CMD ["./.cicd/docker/web/wait-for-mysql.sh", "mysql", "php artisan serve --host=0.0.0.0 --port=8000"]
EXPOSE 8000
```

* Ở docker file mình lấy 1 image php đã cài sẵn php và các service của php và mình update 1 số cái lên như mysql,.. 

* Tiếp theo mình chỉ thị thư mục làm việc và copy toàn bộ file local sang image docker, và cài thêm 1 số thư viện.
( bạn cũng có thể sử dụng 1 image đã tạo sẵn `FROM xuanbinh91/docker-laravel-node` )

* `RUN cp -rf .env.example .env`: dòng này mình copy từ file `.env.example` và tạo mới 1 file `.env` 

* Tiếp mình pull các thư viện php về và generate key.

* 2 bước:
```sh
RUN ["chmod", "+x", "./.cicd/docker/web/wait-for-mysql.sh"]
CMD ["./.cicd/docker/web/wait-for-mysql.sh", "mysql", "php artisan serve --host=0.0.0.0 --port=8000"]
```
 có mục đích để chờ mysql khởi động xong và chờ khi vào được host thì mới chạy `artisan serve`


* Đến đây là hoàn thành các bước thực thi 1 file docker php.

* file `wait-for-mysql.sh`

```sh
set -e
set -x

host="$1"
shift
cmd="$@"

until mysql -h "$host" -u ${MYSQL_USER} ${MYSQL_DATABASE} -e 'use insight'; do
  >&2 echo "MySQL is unavailable - sleeping"
  sleep 1
done

>&2 echo "Mysql is up - executing command"

php artisan migrate:fresh
php artisan db:seed
php artisan serve --host=0.0.0.0 --port=8000
```

Về structure cho cicd hiện tại mình đang để như sau:

```
--.cicd
        -docker
                -web
                    -Dockerfile
                    -wait-for-mysql.sh
                -docker-compose.yml
        -jenkins
                -Jenkinsfile
```

Và bài viết trên được để theo đường dẫn như trên, các bạn có thể tham khảo.


link tham khảo: 
[Viblo](https://viblo.asia/p/tim-hieu-ve-docker-phan-3-vyDZOzxGKwj)