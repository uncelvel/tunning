# Kernel / Network / TCP / UDP Tuning

## Lấy toàn bộ giá trị mặc định của kernel tại thời điểm hiện tại 

List toàn bộ thông số cấu hình kernel
```sh 
sysctl -a
```

List các thông số đã được chỉnh sửa 
```sh 
sysctl -p 
```

## Các giá trị tunning của Ceph được các tài liệu tunning áp dụng trong sysctl và diễn dải.

Khối file

- `kernel.pid_max=4194303`
    ```sh 
    Giá trị mặc định 65356
    Tăng số lượng process ID tối đa mà Kernel có thể tạo và quản lý
    ```

- `fs.file-max=26234859`
    ```sh 
    Giá trị mặc định 6516457
    Tăng số lượng File Descriptors(FD) được thực thi ở kernel
    ```

Khối Memory

- `vm.swappiness=10`
    ```sh 
    Giá trị mặc định là 60
    Là thiết lập được cung cấp bởi Linux Kernel để kiểm soát giữa việc sử dụng Swap và ram. Thiết lập giá trị = 10 mục đích giữ các application chạy trên RAM nhiều nhất có thể trước khi chuyển qua Swap
    ```

- `vm.vfs_cache_pressure=1`
    ```sh 
    Giá trị mặc định ban đầu 100
    Thiết lập cho phép Kernel chủ động hơn việc giải phóng ram từ disk và swap cache
    ```

- `net.core.rmem_max=56623104`
    ```sh 
    Giá trị mặc định ban đầu 212992
    Tăng kích thước bộ đệm tối đa cho tất cả các loại kết nối đên
    ```

- `net.core.wmem_max=56623104`
    ```sh 
    Giá trị mặc định ban đầu 212992
    Tăng kích thước bộ đệm tối đa cho tất cả các loại kết nối đi ra ngoài 
    ```

- `net.core.rmem_default=56623104`
    ```sh 
    Giá trị mặc định ban đầu 212992
    Tăng kích thước giá trị mặc định bộ đệm của tất cả các kết nối đến
    ```

- `net.core.wmem_default=56623104`
    ```sh 
    Giá trị mặc định ban đầu 212992
    Giá trị mặc định bộ đệm của tất cả các kết nối đi ra ngoài
    ```

- `net.core.optmem_max=40960`
    ```sh 
    Giá trị mặc định ban đầu 20480
    Tăng giá trị tối đa cho việc sử dụng bộ nhớ đệm tùy chọn
    ```

- `net.ipv4.tcp_wmem=4096 65536 56623104`
    ```sh 
    Giá trị mặc định ban đầu 4096	16384	4194304
    TCP Autotuning tcp_mem định nghĩa cách sắp xếp TCP hoạt động như thế nào khi sử dụng bộ nhớ. 
    Giá trị đầu tiên cho kernel biết ngưỡng thấp, TCP stack sẽ không quản lý và áp đặt mức sử dụng bộ nhớ. 
    Giá trị thứ 2 cho biết thời điểm kernel bắt đầu áp mức sử dụng bộ nhớ xuống. 
    GIá trị thứ 3 cho biết tối đa có thể sử dụng bao nhiêu pages memory, các luồng TCP và packets giảm xuống cho đến khi nó đạt trạng thái giá trị 2, hoặc hơn nữa là 1. 
    Cấu hình này được áp xuống tất cả các kết nối socket TCP đến đang sử dụng. 
    ```

- `net.ipv4.tcp_rmem=4096 87380 56623104`
    ```sh 
    Giá trị mặc định ban đầu 4096	87380	6291456
    TCP Autotuning tcp_mem định nghĩa cách sắp xếp TCP hoạt động như thế nào khi sử dụng bộ nhớ. 
    Giá trị đầu tiên cho kernel biết ngưỡng thấp, TCP stack sẽ không quản lý và áp đặt mức sử dụng bộ nhớ. 
    Giá trị thứ 2 cho biết thời điểm kernel bắt đầu áp mức sử dụng bộ nhớ xuống. 
    GIá trị thứ 3 cho biết tối đa có thể sử dụng bao nhiêu pages memory, các luồng TCP và packets giảm xuống cho đến khi nó đạt trạng thái giá trị 2, hoặc hơn nữa là 1. 
    Cấu hình này được áp xuống tất cả các kết nối socket TCP đến đang sử dụng. 
    ```

- `net.ipv4.udp_rmem_min=8192`
- `net.ipv4.udp_wmem_min=8192`
    ```sh 
    Giá trị mặc định là 4096
    Ở đây chúng ta cũng giới hạn kích thước bộ nhớ đệm tối thiểu cho kết nối đi và đến của các gói tin UDP
    ```

Khối TCP Listen Backlog

Đối với khối TCP thì chúng ta sẽ thực hiện điều chỉnh các thông số limit của tcp cấu hình trong Kernel cho TCP buffer 
Set giá trị tối đa là 16MB (16777216) cho card 1GE, 32MB (33554432) hoặc 54MB (56623104) cho card 10GE


- `net.core.somaxconn=1024`
    ```sh
    Giá trị mặc định ban đầu 128
    Tăng số lượng connection, giá trị có thể được nâng lên đến giá trị của request. 
    ```

- `net.core.netdev_max_backlog=50000`
    ```sh 
    Giá trị mặc định ban đầu 1000
    Tăng số lượng connection backlog đến tối đa
    ```

- `net.ipv4.tcp_max_syn_backlog=30000`
    ```sh 
    Giá trị mặc định ban đầu 128
    Tăng số lượng connection tối đa, 
    ```

```sh 
netstat -an | grep -c SYN_RECV 
```

- `net.ipv4.tcp_max_tw_buckets=2000000`
    ```sh 
    Giá trị mặc định ban đầu 8192
    Tăng kích thước các pool tcp-time-wait để có thể ngăn chặn các cuộc tấn công DOS đơn giản
    ```

- `net.ipv4.tcp_tw_reuse=1`
- `net.ipv4.tcp_tw_recycle=1`
    ```sh 
    Giá trị mặc định ban đầu 0
    Hai giá trị trên cho phéo khôi phục và sử dụng lại các socket ở trạng thái TIME_WAIT một cách nhanh chóng. 
    ```

- `net.ipv4.tcp_fin_timeout=10`
    ```sh 
    Giá trị mặc định ban đầu 30
    Giảm bớt thời gian chờ TIME_WAIT 
    ```

- `net.ipv4.tcp_slow_start_after_idle=0`
    ```sh 
    Giá trị mặc định ban đầu 1
    Đối với các kết nối TCP không hoạt động trong 1 khoảng tg dài thì hệ thống sẽ không start các TCP connection này ở kích thước mặc định
    ```

- `net.ipv4.conf.all.send_redirects=0`
- `net.ipv4.conf.all.accept_redirects=0`
    ```sh 
    Giá trị mặc định ban đầu 1
    Ở đây disable việc chấp nhận, khởi tạo các luồng chuyển hướng 
    ```

- `net.ipv4.conf.all.accept_source_route=0`
    ```sh 
    Giá trị mặc định ban đầu 0
    Disable source routing
    ```
## Tunning Block Device 

- /sys/block/$device/queue/rotational
    ```sh 
    Giá trị mặc định 1 (Ổ HDD)
    Xác định file được lưu trữ trên thiết bị loại quay cơ khí (HDD) hay không quay (SSD, NVME)
    ```

- /sys/block/$device/queue/add_random
    ```sh 
    Giá trị mặc định 1
    Linux sử dụng entropy là pool cho các SSH key, bảo mật SSL, .. Recommend không để SSD và tham gia vào pool này sẽ tiết kiệm
     được một phần I/O cho hệ thống
    ```

- /sys/block/$device/queue/rq_affinity
    ```sh 
    Giá trị mặc định 1
    Đối với 1 I/O request sẽ được hoàn thành bởi Block Device, nó sẽ gửi lại thông tin cho "group" CPU ra lệnh. 
    Điều này giúp cải thiện hiệu năng của CPU do bộ nhớ đệm đòi hỏi ít chu trình hơn. 
    Nếu giá trị =2 Block Device sẽ gửi request hoàn thành đến chính xác CPU yêu cầu chứ không phải cho nhóm CPU.
    Đối với 1 CPU có tốc độ xử lý nhanh thì việc chỉnh tham số này lên =2 sẽ tận dụng được tối đa hiệu suất của tất cả
    các CORE của CPU
    ```

- /sys/block/$device/queue/scheduler
    ```sh 
    Giá trị mặc định "deadline"
    "deadline" phù hơn hơn CFQ,  Việc chuyển giá trị của scheduler về "noop" cho hiệu suất cao hơn khi chúng ta sử dụng 
    PCI-E SSD 
    ```

- /sys/block/$device/queue/nr_requests
    ```sh 
    Giá trị mặc định 128
    Định nghĩa số lượng I/O có thể xếp hàng đợi tại 1 thời điểm trước khi 1 y/c đọc ghi hoăc chế độ sleep. 
    Giá trị lớn có thể tăng throuput cho các khối lượng công việc ghi nhiều tệp nhỏ. 
    Giá trị nhỏ hơn tăng throughtput cho các hoạt động I/O lớn. 
    Cần cân nhắc đối với các hệ thống cần latency thấp thì nên giảm thông số này lại, và không sử dụng "noop"
    ```

- /sys/block/$device/queue/read_ahead_kb
    ```sh 
    Giá trị mặc định 128
    Xác định số dữ liệu sẽ được đọc trước trong quá trình đọc tuần tự. 
    Nếu các yêu cầu tiếp theo tương tự yêu cầu thứ nhất dữ liệu đã có sắn trong RAM để đọc. 
    ```

Mặc định các giá trị chỉnh sửa sẽ reset sau khi Server reboot để tự động điều chỉnh các tham số add các giá trị này vào `rc.local`
```sh 
vim /etc/rc.local
##Add this above "exit 0"##

echo 0 > /sys/block/$device/queue/add_random
echo 0 > /sys/block/$device/queue/rotational
echo 2 > /sys/block/$device/queue/rq_affinity

for device in $(find /sys/block/* | grep -E '\/nvme' ) ; do
echo 2048 > /sys/block/$device/queue/read_ahead_kb
echo 512 > /sys/block/$device/queue/nr_requests
echo "noop" > /sys/block/$device/queue/scheduler
done 
exit 0
```

##  Các tham số trên Ulimit 

```sh 
[root@controller ~]# ulimit -a 
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 256803
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 256803
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```
- core file size - Kích thước của tệp lỗi khi một tiến trình, chương trình bị lỗi, giá trị = 0 nghĩa là các tệp này sẽ không được tạo ra. 

- data seg size - kích thước tối đa của phân đoạn dữ liệu quá trình, chứa các nội dung như đống

- scheduling priority - số lượng tín hiệu được phép đợi trong khi bị chặn

- file size - dung lượng file tối đa

- pending signals - số lượng tín hiệu chờ xử lý tối đa

- max locked memory - lượng bộ nhớ có thể bị khóa do đó nó không được phân trang

- max memory size - bộ nhớ tối đa trong RAM vật lý

- open files - số lượng file tối đa được xử lý

- pipe size - 

- POSIX message queues - dung lương 

- real-time priority - giá trị tối ưu tối đa cho người dùng thông thường

- stack size - bộ nhớ tối đa cho ngăn xếp

- cpu time - thời gian cpu tối đa (không phải thời gian thực) mà quá trình có thể sử dụng

- virtual memory - về cơ bản tổng bộ nhớ mà quá trình có thể sử dụng

- file locks - 

Giá trị chỉnh sửa `open files`

- `open files`
    ```sh 
   `ulimit -n 10240`
   Tăng số lượng files kernel có thể mở và xử lý
    ```


# Tunning Network trên OS linux 


## 1. SoftIRQ misses (netdev budget) 

## 2. "tuned" tuning daemon 

## 3. "numad" NUMA daemon 

## 4. CPU power states 

## 5. terrupt balancing 

## 6. Pause frames 

## 7. Interrupt Coalescence 

## 8. Adapter queue (netdev backlog) 

## 9. Adapter RX and TX buffers 

## 10. Adapter TX queue 

## 11. Module parameters 

## 12. Adapter offloading 

## 13. Jumbo Frames 

## 14. TCP and UDP protocol tuning

## 15. NUMA locality

# Tài liệu tham khảo 

- https://cromwell-intl.com/open-source/performance-tuning/disks.html
- https://www.kernel.org/doc/Documentation/block/queue-sysfs.txt
- https://wiki.mikejung.biz/Ubuntu_Performance_Tuning
- https://access.redhat.com/sites/default/files/attachments/20150325_network_performance_tuning.pdf
- https://fatmin.com/2015/08/19/ceph-tcp-performance-tuning/