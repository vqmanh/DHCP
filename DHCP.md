# CÀI ĐẶT VÀ CẤU HÌNH DHCP TRÊN CENTOS7

## Nội dung 

[A. Tổng quan về DHCP](#A)

- [A1. DHCP là gì?](#A1)

- [A2. DHCP hoạt động như thế nào?](#A2)

- [A3. Tìm hiểu cơ bản về tcpdump](#A3)


[B. Tiến hành LAB trên môi trường VMware](#B)

- [B1. Chuẩn bị](#B1)

- [B2. Triển khai](#B2)


---------------------

<a name = "A"></a>
## A. Tổng quan về DHCP

<a name = "A1"></a>
### A1. DHCP là gì?

- DHCP là từ viết tắt của Dynamic Host Configuration Protocol (Giao thức Cấu hình Host Động). Nó là giao thức cấp phát địa chỉ IP cho các thiết bị trên một mạng. Mọi thiết bị kết nối vào mạng đều cần một địa chỉ IP và địa chỉ IP đó thường được cấp phát bởi máy chủ DHCP (DHCP server) tích hợp trên router. Trên các hệ thống mạng lớn, một mình router không thể quản lý tất cả các thiết bị kết nối vào nó và do đó một máy chủ chuyên dụng sẽ chịu trách nhiệm cấp địa chỉ IP.

- DHCP không chỉ cấp địa chỉ IP, nó còn cấp các thông số cần thiết cho hoạt động của mạng như subnet mask (mặt nạ mạng), default gateway (gateway mặc định), và dịch vụ DNS.

<a name = "A2"></a>
### A2. DHCP hoạt động như thế nào?

#### Phân tích bản tin DHCP bằng Wireshark

- Sử dụng máy Window đã cài đặt Wireshark.

- Release địa chỉ IP đang sử dụng bằng cách gõ các lệnh sau trong cmd:
    
    `ipconfig /release`

    `ipconfig /renew `

*Hoặc đơn giản là tắt card mạng đi bật lại.*

Lọc các bản tin theo giao thức BOOTP

<img src=https://imgur.com/MvWntl8.jpg>

**Cách thức xin và cấp DHCP Gồm 4 bước:**

- Bước 1: Client gửi gói Discover (hoạt động theo gói tin Broadcast). Gói Discover dùng để hỏi ai là DHCP.

<img src=https://imgur.com/2MXTNZF.jpg>

    - Dùng Wireshark để bắt và phần tích gói tin chúng ta thấy rằng Dest MAC của gói tin Broadcast luôn là ff:ff:ff:ff:ff:ff

    - Source IP: 0.0.0.0 vì lúc này client chưa có IP

    - DestIP: 255.255.255.255 vì gói tin sẽ được gửi đi toàn mạng


- Bước 2: Khi DHCP server nhận được gói tin Discover từ Client, nó sẽ kiểm tra cơ sở dữ liệu IP đã cấp cho Client khác, xem còn IP nào rồi DHCP trả lời bằng gói DHCPOffer. Trong gói offer có thông tin về IP sẽ được cấp như ở đây là 192.168.0.101. Nếu trong thời gian mà Client ko có phản hồi lại thì DHCP sẽ thực hiện thu hồi lại IP này.

<img src=https://imgur.com/ahxpFdW.jpg>

    - Source IP: IP của DHCP là 192.168.0.1 
    - Dest IP: Ip của client sẽ được cấp là 192.168.0.101
    - Source MAC: của DHCP server
- Bước 3: Client gửi gói tin Request để xác nhận muốn sử dụng IP được cấp. Requested IP: 192.168.0.101

<img src=https://imgur.com/42A1kdS.jpg>

- Bước 4: DHCP gửi về gói ACK. Xác nhận đồng ý cho Client sử dụng IP đó. Thời gian lease time ở đây là 7200s tức 2 giờ.

<img src=https://imgur.com/qCVrx6y.jpg>



<a name = "A"></a>
### A3. Tìm hiểu cơ bản về tcpdump

- Tcpdump là phần mềm bắt tin trong mạng làm việc trên hầu hết các phiên bản hệ điều hành unix/linux. Tcpdump cho phép bắt và lưu lại những gói tin bắt được, từ đó ta có thể sử dụng để phân tích.
`yum install tcpdump`

**1. Hiển thị các giao diện mạng**

*Với option -D sẽ hiển thị ra danh sách các giao diện mạng có sẵn và ta có thể bắt các gói tin trên các giao diện này.*

`tcpdump -D`

<img src=https://imgur.com/tjBa2IW.jpg>

**2. Bắt gói tin từ một giao diện ethernet**

*Khi thực hiện lệnh tcpdump mà không có tùy chọn cụ thể nó sẽ bắt tất cả các gói tin đi qua tất cả các card mạng. Option -i cho phép lọc ra một interface etherne*

`tcpdump  -i ens37`

**3. Chỉ bắt đúng N gói tin**

* Để bắt đúng N gói tin ta dùng option -c, VD: chỉ bắt đúng 5 gói tin*

`tcpdump  -i ens37 -c 5`

**4. Bắt gói tin và ghi vào một file**

*tcpdump cho phép ta ghi kết quả bắt được vào một file và khi cần ta có thể sử dụng nó cho các mục đích phân tích khác. Các file này thường có đuôi .pcap và ta có thể dùng wireshark để đọc nó. Để ghi vào file ta dùng option -w*

`tcpdump  -XX -i ens33 -c 3 -w tcpdump1.pcap`

**5. Bắt các gói tin với địa chỉ IP**

*Để bắt các gói tin và hiển thị địa chỉ IP ta dùng option -n*

`tcpdump  -i ens37 -n`

**6. Bắt gói tin với dấu thời gian**

*Option -tttt hiển thị các gói tin có thêm trường ngày*

`tcpdump  -i ens33 -tttt`

**7. Đọc các gói tin lớn hơn N bytes**

 `tcpdump greater số_bytes`

**8. Đọc các gói tin nhỏ hơn N byte** 

`tcpdump less số_byte`

**9. Chỉ nhận gói tin với một kiểu giao thức cụ thể**

*Ta có thể lọc các gói tin dựa vào kiểu giao thức: TCP, UDP, ARP*

`tcpdump -i ens37 tcp`

**10. Bắt các gói tin qua một port cụ thể**

`tcpdump -i ens37 port 22`

**11. Bắt các gói tin trên địa chỉ nguồn hoặc đích**

- Bắt theo địa chỉ nguồn: `tcpdump src IP`

- Bắt theo địa chỉ đích: `tcpdump dst IP`

**12. Bộ lọc các gói**

*Ví dụ ta muốn bắt tất cả các gói tin ngoại trừ các gói arp và rarp ta có thể sử dụng điều kiện and, or hoặc not để lọc các gói tin:* 

VD `tcpdump -i ens33 not arp and not rarp`

<a name = "B"></a>
## B. Tiến hành LAB trên môi trường VMware

<a name = "B1"></a>
### B1. Chuẩn bị

**Mô hình**

<img src=https://imgur.com/Ne2WFjp.jpg>

- Một máy ảo Centos7 cài DHCP Server với 2 card mạng `NAT` và `Host-Only`. Sử dụng card `NAT` để cài các gói, còn card `Host-Only` để làm giải cấp DHCP. Ở đây mình để giải của card `NAT` là 66.0.0.0/24, card `Host-Only` là 192.168.1.0/24.

<img src=https://imgur.com/dPP07KF.jpg>

*Lưu ý: Tắt chế độ cấp DHCP trên card Host-Only*

- Sử dụng 2 máy client để kiểm tra DHCP. Ở đây mình dùng 1 máy ubuntu và 1 máy centos. Cả hai máy đều để card `Host-Only`

- IP Planning:

| Tên máy ảo| Hệ điều hành |IP address | Subnet mask |Default gateway|
|------|------|-------|-----|-------|
DHCP Server|CentOS7|192.168.1.111|255.255.255.0|192.168.1.1
DHCP Client 1|CentOS7|192.168.1.11|255.255.255.0|192.168.1.1
DHCP Client 2|Ubuntu 19.04|192.168.1.12|255.255.255.0|192.168.1.1

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

#### Cấp IP cho một host cụ thể

Bạn cần có địa chỉ MAC của máy cần cấp

VD:
```
    host admin {
        hardware  ethernet 00:0c:29:6c:77:e6;
        fixed-address 192.168.1.15;
    }
```
<img src=https://imgur.com/2cZsDID.jpg>

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

