# huong-dan-cai-dat-mysql-va-phpmyadmin-tren-docker
Hướng dẫn cài đặt MySQL và phpMyAdmin trên Docker

MySQL Community Edition là phiên bản có thể tải xuống miễn phí của cơ sở dữ liệu nguồn mở phổ biến nhất thế giới. Dựa theo giấy phép GPL và được hỗ trợ bởi một cộng đồng các nhà phát triển mã nguồn mở.

PhpMyAdmin là một công cụ phần mềm miễn phí viết bằng ngôn ngữ PHP nhằm cung cấp một giao diện để quản lý cơ sở dữ liệu MySQL hoặc MariaDB.

Docker là một nền tảng phần mềm được thiết kế để giúp tạo, triển khai và chạy các ứng dụng dễ dàng hơn.

Đầu tiên cúng ta sẽ tạo một Docker Network với tên sql nhằm mục đích khởi chạy Docker Container MySQL và phpMyAdmin trong network này.

docker network create sql

Thực hiện lần lượt các lệnh sau đây sẽ khởi tạo MySQL Container trên Docker. Trước tiên, hãy tạo một thư mục dùng để lưu dữ liệu rồi sau đó chạy lệnh khởi tạo MySQL Container:

Khởi tạo thư mục chứa dữ liệu mkdir -p /opt/docker/mysql
Khởi tạo thư mục chứa file config 
```mkdir -p /opt/docker/mysql/conf.d/```
vim my-custom.cnf
[mysqld]
max_connections=250

<article class="markdown-body">
Khởi tạo MySQL container docker run -it -d --name mysql --network sql -e MYSQL_ROOT_PASSWORD="12345" -v /opt/docker/mysql:/var/lib/mysql -v volume=/opt/docker/mysql/conf.d:/etc/mysql/conf.d -p 3306:3306 mysql:8.0.21
</article>

# Khởi tạo MySQL container
`docker run -it -d --name mysql --network sql -e MYSQL_ROOT_PASSWORD="12345" -v /opt/docker/mysql:/var/lib/mysql -v volume=/opt/docker/mysql/conf.d:/etc/mysql/conf.d -p 3306:3306 mysql:8.0.21`

Hướng dẫn các tham số:
– "docker run": câu lệnh để chạy 1 image và bắt đầu 1 container. Container là một process sử dụng 1 image là nội dung bên trong.
– "-it": tham số để cho phép thao tác về sau như can thiệp vào container để thực thi các câu lệnh hệ thống.
– "-d": tham số để nói sau khi thực hiện câu lệnh trên thì cho nó chạy background và con trỏ vẫn ở host hiện tại.
– "–name mysql": đặt tên cho cái container này. Bởi vì có thể tạo nhiều container từ chung 1 image (đây chính là điểm ưu điểm của Docker), mỗi container cần có cái tên để dễ làm việc, không thì docker sẽ tự tạo 1 cái tên ngẫu nhiên. Và lưu ý, không được phép có 2 container cùng 1 tên.
– "-v /opt/mysql:/var/lib/mysql": cho phép mount volume (link thư mục) từ bên trong container ra một thư mục bên ngoài máy đang chạy. Thử tưởng tượng, nếu không sử dụng volume để mapping thư mục, khi container này stop và bị xóa thì toàn bộ dữ liệu trong container cũng bị mất. Do đó, phải mapping ra thư mục bên ngoài để khi container bị xóa thì dữ liệu vẫn còn. Và khi start 1 container mới thì sẽ có dữ liệu trước đó. tham số “-v” là một tham số rất quan trọng Trong ví dụ này thì thư mục “/opt/mysql” là thư mục trên máy và “/var/lib/mysql” là chuỗi cố định được cài đặt sẵn trong image.
– "-e MYSQL_ROOT_PASSWORD=12345": Tham số “-e” sẽ set một biến môi trường. Ở đây, set biến môi trường làm password root cho mysql server. Thường mỗi image đều có ghi chú về các biến môi trường và công dụng của nó khi chạy một image.
– "mysql:latest": tên image và tag phiên bản của image sẽ chạy.

Truy cập vào MySQL container
# docker exec -it mysql01 mysql -uroot -p

Kiểm tra cấu hình max_connections
mysql -uroot -pmypassword -h127.0.0.1 -P6603 -e 'show global variables like "max_connections"';

Import file .sql vào MySQL container
Trong phần lớn trường hợp, sau khi đã có MySQL Server, bạn thường muốn import từ file .sql. Bạn có thể chạy câu lệnh sau để import file sql.
docker exec -i mysql01 mysql -uroot -p123456 --default-character-set=utf8 DATABASENAME < /data/database.sql

Thực hiện lần lượt các lệnh sau đây sẽ khởi tạo phpMyAdmin Container trên Docker. Để phpMyAdmin  có thể hoạt động chúng ta cần phải liên kết đến vùng chứa MySQL để phpMyAdmin có thể kết nối và truy cập cơ sở dữ liệu.

# Khởi tạo phpMyAdmin container docker run -d --name phpmyadmin --network sql -e PMA_HOST=sql -p 8080:80 phpmyadmin/phpmyadmin

Sau khi hoàn thành cài đặt MySQL và phpMyAdmin trên Docker, chúng ta truy cập địa chỉ dạng http://my-ip:8080/

Start, Stop and restart MySQL Container
docker start [container_name]
docker stop [container_name]
docker restart [container_name]

Delete MySQL Container
docker rm [container_name]

Thảm khảo:
https://phoenixnap.com/kb/mysql-docker-container
