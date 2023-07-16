# ***TÌM HIỂU VỀ DOCKER***

# *MỘT SỐ LỆNH CƠ BẢN TRÊN DOCKER*
## 1. Tải về 1 image

Ví dụ:
- Tải về ubuntu version 16.04 -> `sudo docker pull ubuntu:16.04`
- Tải về phiên bản cuối cùng -> `sudo docker pull ubuntu:lastest`
- Cú pháp: `sudo docker pull [name_image]:[tag]`
## 2. Xóa đi 1 image

VD:
- Cách 1 -> `sudo docker rm ubuntu:16.04`
- Cách 2 -> `sudo docker rm [image_id]`

Cú pháp :
- Cách 1 -> `sudo docker rm [name_image]:[tag]`
- Cách 2 -> `sudo docker rm [image_id]` (không cần viết hết ID nếu như những kí tự đầu không trùng)

Lưu ý: Khi image chạy, các phiên bản thực thi của image là các container (1 image có thể tạo nhiều container)

## 3. Khởi tạo và chạy 1 image

Cú pháp: `docker run [param] IMAGE command [param_command]`

Ví dụ :
```
docker run -i -t ubuntu:16.04
```
- Tham số -i : container tạo ra nhận tương tác
- Tham số -t : kết nối với terminal
```
docker run -it --name "ABC" -h ubuntu1 ubuntu:16.04
```
- Tham số --name : đặt tên cho container
- Tham số -h : tên cho host name (ví dụ root@ubuntu1 thay vì host name do docker đặt)

## 4. Xem những container đang chạy

Cú pháp: `docker ps`

- Để xem tất cả những container
Cú pháp: `docker ps -a`
- Để tắt container :

Cú pháp:
- Trong terminal của container -> exit
- Trong cửa sổ terminal khác -> docker stop [container_ID]
- Để đóng cửa sổ terminal của container nhưng container vẫn chạy -> Ctrl + P + Q
## 5. Để khởi chạy lại những container đã tắt
Cú pháp: `docker start [container_ID]`
## 6. Để xóa container

- Cú pháp:
`docker rm [container_ID]`

- Khi container đang chạy mà vẫn muốn xóa : `docker rm -f [container_ID]`
## 7. PHỤ LỤC
- parameters -> param : tham số
- 1 IMAGE đặc trưng bởi tên + phiên bản hoặc `ID ([name]:[tag] && [ID])`. Mọi `[name]:[tag]` đều có thể thay thế bằng [iD]. Với mọi thông tin có thể dùng bởi `[name]:[tag]` thì đều có thể thay thế bởi `[ID]`.

## 8. Muốn trở lại 1 container đang chạy

Cú pháp: `docker attach [container_ID]`
## 9.Khi đứng ở ngoài container nhưng vẫn muốn thi hành một lệnh cho container đang chạy
- Cú pháp: `docker exec [container_ID] command`
- Ví dụ: `docker exec U1 ls`
- Ví dụ: `docker exec -it U1 bash <-> docker attach` -> Khi thoát bằng lệnh exit sau khi dùng lệnh bash thì container vẫn sẽ chạy. -> Muốn cài cái gì thì cài như bình thường khi dùng máy thật.
## 10. Lưu container trở lại thành image
### ***Container phải ở trạng thái dừng***

- Cú pháp: `docker commit [container_ID]`
## 11. Lưu image trở thành 1 file
- Cú pháp: `docker save --output [file_name.tar] [image_ID]` (tới đường dẫn muốn lưu image)
- Ví dụ: `docker save --output myimage.tar ff`
## 12. Khi có 1 container của image đang chạy, không thể xóa image, để ép nó phải xóa
- Cú pháp: `docker image rm [image_ID] -f`

Để import file docker lại thành 1 image
- Cú pháp: `docker load -i [file_name.tar]`

Ví dụ: `docker load -i myimage.tar`
## 13 .Đổi tên cho image
- Cú pháp : `docker tag f [name]:[tag]`

Ví dụ: `docker tag f [newimage]:[version2]`

## 14. Khi muốn chạy container đúng 1 lần, chạy xong container tự xóa sử dụng
- Cú pháp : `docker run -it --rm [image_name]`
- Tham số --rm : tự xóa container sau khi sử dụng
## 15. Để chia sẻ thư mục trên máy host cho container
- Cú pháp: `docker run -i -v <path1>:<path2> [image_ID]`

Ví dụ: docker -it -v /user/danht/dulieu:/home/dulieu ff85
- Tham số -v : chỉ định tài nguyên
- <path1>: đường dẫn tài nguyên trong máy host
- <path2>: tài nguyên trên máy host được ánh xạ sang đường dẫn path2 trong container

-> Mọi dữ liệu được chỉnh sửa trên container hay trên máy host thì đều được thay đổi trên cả 2

## 16. Tạo 1 container cùng được chia sẻ dữ liệu giống 1 container đang chạy
- Cú pháp: `docker run -it --name [container_2] --volumes-from [container_1] [image_ID]`

Ví dụ: `docker run -it --name C2 --volumes-from C1 ubuntu:16.04`

-> Mọi dữ liệu được thay đổi thì đều thay đổi trên các bên

## ***TẠO VÀ QUẢN LÝ Ổ ĐĨA BẰNG DOCKER***

## 17. Kiểm tra đang có ổ đĩa nào
- Cú pháp: `docker volume ls`
## 18. Tạo ổ đĩa
- Cú pháp: `docker volume create [name_volume]`
- Ví dụ: `docker volume create D1`
## 19. Kiểm tra thông tin ổ đĩa vừa tạo
- Cú pháp: `docker volume inspect [name_volume]`

Ví dụ: docker volume inspect D1
## 20. Xóa ổ đĩa
- Cú pháp: `docker volume rm [name_volume]`

Ví dụ: docker volume rm D1
## 21. Gán ổ đĩa vào container để container sử dụng ổ đĩa
- Cú pháp: `docker run -it --name [container_name] --mount source=[name_volume], target=<path> [image_ID]`

Ví dụ: `docker run -it --name C1 --mount source=D1, target=/home/disk2 ubuntu:16.04`
-  \<path> ổ đĩa được ánh xạ vào thư mục nào
- Có thể lưu trữ dữ liệu từ container vào ổ đĩa mà khi xóa đi không bị mất dữ liệu
- Muốn truy cập ổ đĩa, phải truy cập qua container
## 22. Tạo ra ổ đĩa mà ánh xạ tới thư mục nào đó trong máy host
- Cú pháp: `docker volume create --opt device=<path1> --opt type=none --opt o=bind [volume_name]`

Ví dụ: `docker volume create --opt device=/user/trannguyenhan --opt type=none --opt o=bind D1`
- <path1>: đường dẫn trên máy host
- Khi gán ổ đĩa này cho container không sử dụng tham số  --mount và sử dụng tham số -v

Ví dụ: docker run -it -v D1:/home/disk ubuntu:16.04 (giống chia sẻ thư mục máy host cho container)
## ***TẠO MẠNG TRONG DOCKER***
## 23. Kiểm tra trong docker có những mạng nào
- Cú pháp : `docker network ls`

