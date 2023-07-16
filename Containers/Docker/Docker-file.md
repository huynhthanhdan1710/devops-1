# ***DOCKER FILE***
## Giới thiệu
Dockerfile là file chứa các đặc tả về môi trường thực thi phần mềm. Dễ hiểu nó chỉ là file text thôi, và có các câu lệnh cài đặt quen thuộc. Dĩ nhiên cũng sẽ có 1 số câu lệnh hỗ trợ bởi docker.

<img src=../../Image/docker-file.png>
Cú pháp chung của một Dockerfile có dạng:
```INSTRUCTION arguments```
- INSTRUCTION là tên các chỉ thị có trong Dockerfile, mỗi chỉ thị thực hiện một nhiệm vụ nhất định, được Docker quy định. Khi khai báo các chỉ thị này phải được viết bằng chữ IN HOA.
- Một Dockerfile bắt buộc phải bắt đầu bằng chỉ thị FROM để khai báo đâu là image sẽ được sử dụng làm nền để xây dựng nên image của bạn.
- aguments là phần nội dung của các chỉ thị, quyết định chỉ thị sẽ làm gì.
Ví dụ :

```
FROM python:alpine3.7

RUN apk add git \
            wget \
            nano

COPY ./main.py /home

CMD ["python" "/home/main.py"]
```
- FROM: môi trường của mình sẽ được dựng trên môi trường đặc tả sau lệnh FROM, ở đây mình dùng môi trường python 3.7, hệ điều hành alpine 3.7 (link docker image ở đây). Có rất nhiều môi trường có thể bắt đầu như ubuntu, centos, …
- python:alpine3.7: tên của 1 docker image có cấu trúc như sau: tên_image:version. Cho cùng 1 image python, ta có thể có nhiều phiên bản khác nhau.
- Các câu lệnh tiếp theo trong file lần lượt mô tả các package sẽ được cài đặt vào môi trường (hệ điều hành alpine, ta phải dùng lệnh "apk" thay vì "apt" như trên Ubuntu), copy source code file main.py từ máy thật vào trong image có đường dẫn /home. 
- Cuối cùng là câu lệnh thực thi khi container được dựng lệnh / khởi động.
## Các chỉ thị chính trong Dockerfile
### **FROM**
Chỉ định rằng image nào sẽ được dùng làm image cơ sở để quá trình build image thực thiện các câu lệnh tiếp theo. Các image base này sẽ được tải về từ Public Repository hoặc Private Repository riêng của mỗi người tùy theo setup.
```
FROM <image> [AS <name>]
FROM <image>[:<tag>] [AS <name>]
FROM <image>[@<digest>] [AS <name>]

```

Chỉ thị FROM là bắt buộc và phải được để lên phía trên cùng của Dockerfile.

Ví Dụ 
```
FROM ubuntu
hoặc
FROM ubuntu:latest

```
### **LABEL**
Chỉ thị LABEL được dùng để thêm các thông tin meta vào Docker Image khi chúng được build. Chúng tồn tại dưới dạng các cặp key - value, được lưu trữ dưới dạng chuỗi. Có thể chỉ định nhiều label cho một Docker Image, và tất nhiên mỗi cặp key - value phải là duy nhất. Nếu cùng một key mà được khai báo nhiều giá trị (value) thì giá trị được khai báo gần đây nhất sẽ ghi đè lên giá trị trước đó.

Cú pháp:
```
LABEL <key>=<value> <key>=<value> <key>=<value> ... <key>=<value> 
```

Có thể khai báo metadata cho Image theo từng dòng chỉ thị hoặc có thể tách ra khai báo thành từng dòng riêng biệt.

Ví dụ :
```
LABEL com.example.some-label="lorem"
LABEL version="2.0" description="Lorem ipsum dolor sit amet, consectetur adipiscing elit."

```

Để xem thông tin meta của một Docker Image, ta sử dụng dòng lệnh: ``` docker inspect <image id> ```
### **MAINTAINER**
Chỉ thị MAINTAINER dùng để khai báo thông tin tác giả người viết ra file Dockerfile.

Cú pháp: 
```
MAINTAINER <name> [<email>]
```

Ví dụ : 
```
MAINTAINER Danht <htdan2k@gmail.com>
```

Hiện nay, theo tài liệu chính thức từ bên phía Docker thì việc khai báo MAINTAINER đang dần được thay thế bằng LABEL maintainer bới tính linh hoạt của nó khi ngoài thông tin về tên, email của tác giả thì ta có thể thêm nhiều thông tin tùy chọn khác qua các thẻ metadata và có thể lấy thông tin dễ dàng với câu lệnh docker inspect ....

VD : 
```
LABEL maintainer="htdan2k@gmail.com"
```
### **RUN**

Chỉ thị RUN dùng để chạy một lệnh nào đó trong quá trình build image và thường là các câu lệnh Linux. Tùy vào image gốc được khai báo trong phần FROM thì sẽ có các câu lệnh tương ứng. Ví dụ, để chạy câu lệnh update đối với Ubuntu sẽ là `RUN apt-get update -y` còn đối với CentOS thì sẽ là `Run yum update -y`. Kết quả của câu lệnh sẽ được commit lại, kết quả commit đó sẽ được sử dụng trong bước tiếp theo của Dockerfile.

Cú Pháp : 
```
RUN <command>
RUN ["executable", "param1", "param2"]
```
Ví dụ :
```
RUN /bin/bash -c 'source $HOME/.bashrc; echo $HOME'
-------- hoặc --------
RUN ["/bin/bash", "-c", "echo hello"]
```

Cách thức shell form để có thể thực hiện nhiều câu lệnh cùng một lúc với dấu \:
```
FROM ubuntu
RUN apt-get update
RUN apt-get install curl -y
```
Hoặc
```
FROM ubuntu
RUN apt-get update;
    apt-get install curl -y
```
### **ADD**
Chỉ thị ADD sẽ thực hiện sao chép các tập, thư mục từ máy đang build hoặc remote file URLs từ `src` và thêm chúng vào filesystem của image `dest`.

Cú Pháp :
```
ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

Trong đó:
- src có thể khai báo nhiều file, thư mục, ...
- dest phải là đường dẫn tuyệt đối hoặc có quan hệ chỉ thị đối với WORKDIR.

Ví dụ : 
```
ADD hom* /mydir/
ADD hom?.txt /mydir/
ADD test.txt relativeDir/
```
Cũng có thể phân quyền vào các file/thư mục mới được copy:
```
ADD --chown=55:mygroup files* /somedir/
ADD --chown=bin files* /somedir/
ADD --chown=1 files* /somedir/
ADD --chown=10:11 files* /somedir/
```
### **COPY**
Chỉ thị COPY cũng giống với ADD là copy file, thư mục từ `<src>` và thêm chúng vào `<dest>` của container. Khác với ADD, nó không hỗ trợ thêm các file remote file URLs từ các nguồn trên mạng.

Cú Pháp :
```
COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]
```
### **ENV**

Chỉ thị ENV dùng để khai báo các biến môi trường. Các biến này được khai báo dưới dạng key - value bằng các chuỗi. Giá trị của các biến này sẽ có hiện hữu cho các chỉ thị tiếp theo của Dockerfile.

Cú Pháp :
```
ENV <key>=<value> ...
```
Ví dụ :
```
ENV DOMAIN="thanhdan.name.vn"
ENV PORT=80
ENV USERNAME="danht" PASSWORD="danht"
```
Ngoài ra cũng có thể thay đổi giá trị của biến môi trường bằng câu lệnh khởi động container:
```
docker run --env <key>=<value>

```
ENV chỉ được sử dụng trong các command sau:
- ADD
- COPY
- ENV
- EXPOSE
- FROM
- LABEL
- STOPSIGNAL
- USER
- VOLUME
- WORKDIR
### **CMD**
Chỉ thị CMD định nghĩa các câu lệnh sẽ được chạy sau khi container được khởi động từ image đã build. Có thể khai báo được nhiều nhưng chỉ có duy nhất CMD cuối cùng được chạy.

Cú Pháp :
```
CMD ["executable","param1","param2"]
CMD ["param1","param2"] 
CMD command param1 param2
```

Ví dụ :
```
FROM ubuntu
CMD echo "Dan ne"
```
### **USER**
Có tác dụng set username hoặc UID để sử dụng khi chạy image và khi chạy các lệnh có trong RUN, CMD, ENTRYPOINT sau nó.
Cú pháp:
```
USER <user>[:<group>]
hoặc
USER <UID>[:<GID>]
```
Ví dụ :
```
FROM alpine:3.4
RUN useradd -ms /bin/bash danht1
USER danht1
```
## Tạo image với Dockerfile

Phần trên đã đi qua được cấu trúc cũng như các chỉ thị chính của một Dockerfile. Bây giờ sẽ thực hành build một image bằng cách viết một Dockerfile đơn giản.
Lưu ý : hãy chắc chắn rằng máy đã được cài đặt sẵn Docker.
## *Tạo Dockerfile*
Ví dụ 1

Ở bước này, đầu tiên sẽ tạo một đường dẫn mới cho Dockerfile.

`mkdir myproject && cd myproject`

Tiếp theo là tạo Dockerfile, lưu ý rằng tên của Dockerfile phải đúng là ***"Dockerfile"*** và không có phần đuôi mở rộng cho loại file này. Nếu không đặt tên đúng, khi build hệ thống sẽ báo lỗi là không tìm thấy file.

`touch Dockerfile`

Sau khi đã tạo xong Dockerfile, ta tiền hành nhập các chỉ thị:

```
FROM alpine
CMD ["echo", "Hello world!"]
```
Nội dung của Dockerfile ở trên chỉ chứa 2 chỉ thị FROM và CMD, CMD chứa câu lệnh echo và sẽ in ra màn hình dòng chữ "Hello world!" khi mà container được khởi động từ image đã build từ Dockerfile này. Tiến hành tạo image với câu lệnh:

`docker build -t <tên image> .`

Cuối cùng, chạy lệnh `docker run <imageID>` để tạo và chạy container. Kết quả thu được sẽ là dòng chữ "Hello world!" được in ra trên màn hình.

### ***Chú ý***
 - Nếu bạn đang ở thư mục /path/to/Dockerfile, lệnh `docker build -t myimage` . sẽ tìm kiếm file Dockerfile trong thư mục hiện tại /path/to/Dockerfile và build image có tên là myimage từ Dockerfile này.

 - Nếu bạn đang ở thư mục /path/to/dockerbuild/ và muốn chạy Dockerfile tại đường dẫn /path/to/dockerbuild/myapp/Dockerfile, bạn có thể sử dụng lệnh `docker build -t myimage -f myapp/Dockerfile .. `Trong trường hợp này, dấu "." chỉ định thư mục hiện tại /path/to/dockerbuild/ nhưng tùy chọn -f sẽ chỉ định Dockerfile nằm tại thư mục con /path/to/dockerbuild/myapp/.