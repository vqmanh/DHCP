# CÀI ĐẶT VÀ CẤU HÌNH DHCP TRÊN CENTOS7

## Nội dung 

[A. Tổng quan về DHCP](#A)

- [A1. DHCP là gì?](#A1)

- [A2. DHCP hoạt động như thế nào?](#A2)

[B. Tiến hành LAB](#B)

- [B1. Chuẩn bị](#B1)

- [B2. Triển khai](#B2)


---------------------

<a name = "A"></a>
## A. Tổng quan về DHCP

<a name = "A1"></a>
### A1. DHCP là gì?

<a name = "A2"></a>

### A2. DHCP hoạt động như thế nào?

<a name = "B"></a>
## B. Tiến hành LAB

<a name = "B1"></a>
### B1. Chuẩn bị

- Một máy ảo Centos7 cài DHCP Server với 2 card mạng `NAT` và `Host-Only`. Sử dụng card `NAT` để cài các gói, còn card `Host-Only` để làm giải cấp DHCP. Ở đây mình để giải của card `NAT` là 66.0.0.0/24, card `Host-Only` là 192.168.1.0/24.

<img src=https://imgur.com/dPP07KF.jpg>

*Lưu ý: Tắt chế độ cấp DHCP trên card Host-Only*

- Sử dụng 2 máy client để kiểm tra DHCP. Ở đây mình dùng 1 máy ubuntu và 1 máy centos. Cả hai máy đều để card `Host-Only`

<a name = "B2"></a>
### B2. Triển khai

**Bước 1: Cài đăt dịch vụ DHCP**

`yum install -y dhcp`

***Kiểm tra DHCP đã được cài đặt hay chưa, nếu hiện ra như hình dưới đây tức là đã cài thành công***

`rpm -qa | grep dhcp`

<img src=https://imgur.com/o4II86I.jpg>


**Bước 2: Cấu hình dịch vụ**

***Copy file mẫu của DHCP***

`cp -f /usr/share/doc/dhcp-*/dhcpd.conf.example /etc/dhcp/dhcpd.conf`

*Xuất hiện dòng: overwrite ‘/etc/dhcp/dhcpd.conf’? điền y và enter*

***Chỉnh sửa file cấu hình***

`vi /etc/dhcp/dhcpd.conf`

<img src=https://imgur.com/WCqpqpp.jpg>

*Chú ý: Để dải mạng của card Host-Only*

- range: khai báo dải mạng sẽ cấp phát
- subnet: để dải của card Host-Only
- option domain-name-servers: cung cấp DNS server cho Client
- option routers: cung cấp thông tin địa chỉ IP của router gateway mà Client sẽ sử dụng khi nhận DHCP
- default-lease-time: thời gian mặc định một IP DHCP tồn tại được cấp phát cho người dùng
- max-lease-time: thời gian tối đa một IP DHCP tồn tại được cấp phát cho người dùng

**Bước 3: Cấu hình DHCP khởi động cùng hệ thống**

`systemctl start dhcpd`

`systemctl enable dhcpd`

***Cấu trúc các file/ thư mục của dịch vụ DHCP Server***

```
/etc/dhcp/dhcp.conf  #file cấu hình
/var/lib/dhcpd/dhcpd.leases #file chứa thông tin các IP động đang cấp
```
**Bước 4: Chuyển qua 2 máy Client để kiểm tra**

***Kiểm tra trên DHCP Server và đối chiếu với 2 máy client***

`cat /var/lib/dhcpd/dhcpd.leases`

<img src=https://imgur.com/HRvOms8.jpg>

***Máy DHCP client centos***

<img src=https://imgur.com/HE3t8Ut.jpg>

***Máy DHCP client ubuntu***

<img src=https://imgur.com/677wuEA.jpg>