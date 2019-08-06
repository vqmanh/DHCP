# Cấu trúc gói tin

## Một gói tin TCP bao gồm 2 phần

- Header (độ dài tối thiểu 20 bytes, tối đa 60 bytes)
- Dữ liệu

<img src=https://imgur.com/zU5mXRo.jpg>

**Phần header có 11 trường trong đó 10 trường bắt buộc. Trường thứ 11 là tùy chọn (trong bảng minh họa có màu nền đỏ) có tên là: options**

### Header

**Source port:** Số hiệu của cổng tại máy tính gửi.

**Destination port:** Số hiệu của cổng tại máy tính nhận.

**Sequence number:** Trường này có 2 nhiệm vụ. Nếu cờ SYN bật thì nó là số thứ tự gói ban đầu và byte đầu tiên được gửi có số thứ tự này cộng thêm 1. Nếu không có cờ SYN thì đây là số thứ tự của byte đầu tiên.

**Acknowledgement number:**
Nếu cờ ACK bật thì giá trị của trường chính là số thứ tự gói tin tiếp theo mà bên nhận cần.

**Data offset:**
Trường có độ dài 4 bít quy định độ dài của phần header (tính theo đơn vị từ 32 bít). Phần header có độ dài tối thiểu là 5 từ (160 bit) và tối đa là 15 từ (480 bít).

**Reserved:**
Dành cho tương lai và có giá trị là 0.

**Flags (hay Control bits):**
Bao gồm 6 cờ:

- ***URG***
Cờ cho trường Urgent pointer

- ***ACK***
Cờ cho trường Acknowledgement

- ***PSH***
Hàm Push

- ***RST***
Thiết lập lại đường truyền

- ***SYN***
Đồng bộ lại số thứ tự

- ***FIN***
Không gửi thêm số liệu

***Window***
Số byte có thể nhận bắt đầu từ giá trị của trường báo nhận (ACK)

***Checksum:***
*16 bít của trường kiểm tra là bổ sung của tổng tất cả các từ 16 bít trong gói tin. Trong trường hợp số octet (khối 8 bít) của header và dữ liệu là lẻ thì octet cuối được bổ sung với các bít 0. Các bít này không được truyền. Khi tính tổng, giá trị của trường kiểm tra được thay thế bằng 0*

- Nói một cách khác, tất cả các từ 16 bít được cộng với nhau. Kết quả thu được sau khi đảo giá trị từng bít được điền vào trường kiểm tra. Về mặt thuật toán, quá trình này giống với IPv4.

- Các địa chỉ nguồn và đích là các địa chỉ IPv4. Giá trị của trường protocol là 6 (giá trị dành cho TCP, xem thêm: Danh sách số hiệu giao thức IPv4). Giá trị của trường TCP length field là độ dài của toàn bộ phần header và dữ liệu của gói TCP.

***Urgent pointer:***
Nếu cờ URG bật thì giá trị trường này chính là số từ 16 bít mà số thứ tự gói tin (sequence number) cần dịch trái.
Options
Đây là trường tùy chọn. Nếu có thì độ dài là bội số của 32 bít.

### Dữ liệu
Trường cuối cùng không thuộc về header. Giá trị của trường này là thông tin dành cho các tầng trên (trong mô hình 7 lớp OSI). Thông tin về giao thức của tầng trên không được chỉ rõ trong phần header mà phụ thuộc vào cổng được chọn.

### Thiết lập kết nối
- Để thiết lập một kết nối, TCP sử dụng một quy trình bắt tay 3 bước (3-way handshake) Trước khi client thử kết nối với một server, server phải đăng ký một cổng và mở cổng đó cho các kết nối: đây được gọi là mở bị động. Một khi mở bị động đã được thiết lập thì một client có thể bắt đầu mở chủ động. Để thiết lập một kết nối, quy trình bắt tay 3 bước xảy ra như sau:

- Client yêu cầu mở cổng dịch vụ bằng cách gửi gói tin SYN (gói tin TCP) tới server, trong gói tin này, tham số sequence number được gán cho một giá trị ngẫu nhiên X.
Server hồi đáp bằng cách gửi lại phía client bản tin SYN-ACK, trong gói tin này, tham số acknowledgment number được gán giá trị bằng X + 1, tham số sequence number được gán ngẫu nhiên một giá trị Y
- Để hoàn tất quá trình bắt tay ba bước, client tiếp tục gửi tới server bản tin ACK, trong bản tin này, tham số sequence number được gán cho giá trị bằng X + 1 còn tham số acknowledgment number được gán giá trị bằng Y + 1
Tại thời điểm này, cả client và server đều được xác nhận rằng, một kết nối đã được thiết lập.

Truyền dữ liệu
Một số đặc điểm cơ bản của TCP để phân biệt với UDP:

- Truyền dữ liệu không lỗi (do có cơ chế sửa lỗi/truyền lại)

- Truyền các gói dữ liệu theo đúng thứ tự
- Truyền lại các gói dữ liệu mất trên đường truyền
- Loại bỏ các gói dữ liệu trùng lặp
- Cơ chế hạn chế tắc nghẽn đường truyền

<img src=https://imgur.com/TaqKq7g.gif>