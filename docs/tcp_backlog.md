# Hoạt động của TCP backlog

Khi một ứng dụng đặt một socket vào trạng thái LISTEN bằng cách sử dụng `syscall listen`, nó cần phải xác định một backlog cho socket đó. Các backlog thường được mô tả như là giới hạn cho hàng đợi của các kết nối đến.

![](../images/tcp_backlog.png)

Do tính chất bắt tay 3 bước được sử dụng bởi TCP, kết nối đến thông qua trạng thái trung gian `SYN`, `RECEIVED` trước khi chuyển về `ESTABLISHED` và có thể được trả về được chấp nhận bởi syscall cho ứng dụng 

# Taì liệu tham khảo 

http://veithen.github.io/2014/01/01/how-tcp-backlog-works-in-linux.html