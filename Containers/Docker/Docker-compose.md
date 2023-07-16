# ***Docker Compose***
## ***Cách tạo và dùng docker compose file***

Docker compose sử dụng tệp YAML để định cấu hình các dịch vụ của ứng dụng. Sau đó, với một lệnh duy nhấtcó thể tạo và khởi động tất cả các dịch vụ từ cấu hình của mình. Để sử dụng Compose thông thường có ba bước sau:
1. Tạo Dockerfile cho mỗi môi trường container của từng service mình muốn. Dockerfile là bắt buộc để khởi tạo container.
2. Tạo file docker-compose.yml để định nghĩa mối liên kết giữa các containers với nhau.
3. Chạy lệnh docker-compose up để khởi động Compose và chạy toàn bộ ứng dụng.

Để có thể dùng được docker compose, cần tạo một compose file như *-**compose.yml** để thiết lập các container cần cho ứng dụng. Trông nó sẽ như thế này:
```
version: "3.9"  
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
      - logvolume01:/var/log
    links:
      - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```
Và sau đó để build, run và stop các container,sử dụng các command sau:
```
$ docker-compose build
$ docker-compose up 
$ docker-compose down
```
Trong đó 
- `docker-compose build` dùng để build tất cả container được định nghĩa trong compose file. Tuy nhiên, lệnh này thường để thực hiện build lại service vừa được thay đổi bằng lệnh sau `docker-compose build <servicename>`

- `docker-compose up` thực hiện tạo và khởi chạy các container. Các bạn có thể xem ở đây để thêm các options tương ứng với lệnh up. Về cơ bản thì bạn chỉ cần 2 option là -d và --force-recreate

  - Với -d thì các containers sẽ được chạy dưới dạng background.Detached mode chắc hẳn là không thể thiếu khi khởi chạy bất cứ service nào.
  - Với --force-recreate thì bạn sẽ tái tạo lại các containers.
Còn nếu chỉ muốn up một số services thì các bạn cứ đặt các service muốn chạy đằng sau lệnh up là được. Ví dụ như docker-compose up -d redis sqlserver
- `docker-compose down` dùng để dừng các container và xóa hết những gì được tạo từ lệnh up. Về cơ bản thì nó sẽ xóa bỏ những container và network được định nghĩa trong compose file.
 
## Ví dụ về docker compose file
Về cơ bản các lệnh trong docker compose cũng giống như những gì docker thực hiện.
### 1. Tạo 1 Dockerfile
Giống như khi chạy 1 docker,cần kởi tạo 1 Dockerfile để chỉ định cách tạo image và container cho chương trình.
```
FROM php:5.6-apache
RUN apt-get update && \
        curl \
        vim \
        libmemcached-dev \
        libz-dev \
        libpq-dev \
        libjpeg-dev \
        
# Customize apache config
COPY ./danht.conf  /etc/apache2/sites-available/000-default.conf

WORKDIR /var/www/html

RUN usermod -u 1000 www-data
RUN chown -R www-data:www-data /var/www/html

```
2. Tạo 1 docker-compose.yml

Để xác định ứng dụng gồm nhiều containe, nên sử dụng file docker-compose.yml trong thư mục gốc của dự án.

```
version: '3'

services:
   mariadb:
     image: mariadb:5.5
     port:
       - "3306:3306"
     volumes:
       - database_data:/var/lib/mysql
     environment:
       MYSQL_ROOT_PASSWORD: root
       MYSQL_DATABASE: database_test
       MYSQL_USER: test
       MYSQL_PASSWORD: test@123
     network:
       - nets
   web:
     depends_on:
       - mariadb
      build:
       context: .
     ports:
       - "8000:80"
     networks:
       - nets
volumes:
    database_data:
 networks:
     nets:

```
Lưu file docker-compose.yml. Sau đó chạy lệnh tại đường chỉ dẫn chứa file .yml: `docker-compose up -d`.

### **Giải thích cú pháp của file docker-compose.yml**

File docker-compose.yml được tổ chức gồm 4 phần:

|        | Ý nghĩa | 
|--------|-------|
| Version | Version của file **docker-compose ( version của docker engine)** |
| services | chứa các `container`. Với mỗi service là ***tên*** của một container | 
| Volumes | Gắn đường dẫn trên `host machine` được sử dụng trên container | 
| Networks | Sử dụng để cấu hình network cho ứng dụng | 

Với services có một số thành phần sau:

|  |Ý nghĩa
|---|---|
|image	|Chỉ ra image sẽ được dùng để build container.Tên image được chỉ định khi build một image trên máy host hoặc download từ Docker Hub|
|port |	Kết nối port của máy host đến port của container|
|volumes|	Gắn đường dẫn trên host machine được sử dụng trên container|
|enviroment|	Định nghĩa các biến môi trường được truyền vào Docker
|depends_on|	Chọn các service được dùng là dependency cho container được xác định trong service hiện tại.|
|build	|Chỉ ra vị trị đường dẫn đặt Dockerfile|


## Một vài chú ý:
- dockerfile dùng để build các image.
- docker-compose dùng để build và run các container.
- docker-compose viết theo cú pháp **YAML**, các lệnh khai báo trong docker-compose gần tương tự với thao tác chạy container `docker run`.
- docker-compose cung chấp chức năng `Horizontally scaled`, cho phép ta tạo ra nhiều container giống nhau một cách nhanh chóng. Bằng cách sử dụng lệnh
```sh
docker-compose scale name_service=5
```

Trong đó, **name_service** là tên services cần tạo container. **5** là số container sẽ được tạo ra.