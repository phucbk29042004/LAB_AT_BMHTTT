- Họ và tên sinh viên: Huỳnh Ngọc Phúc
- Mã số sinh viên: 1150070035
- Tên bài Lab: Xác thực Active Directory và chính sách truy cập trên pfSense (Lab 6)

- Nội dung đã thực hiện:
Bài thực hành dùng VMware Workstation thay cho VirtualBox theo đề gốc. Mô hình gồm pfSense-LAB6 với 4 interface (WAN, MGMT, LAN, DMZ), Domain Controller DC01 chạy Windows Server 2022 (domain lab6.test), Client-A là máy Kali Linux, Client-B là bản clone của Kali, và DMZ-WEB là Ubuntu Server chạy Nginx với 4 địa chỉ IP (172.16.6.10, .20, .30, .40) để mô phỏng các nhóm website.

Sau đó thực hiện 5 kịch bản chính sách: Kịch bản 1 chặn Client-A tới JOB_SITE; Kịch bản 2 cấm RESTRICTED_SITE cho cả hai client; Kịch bản 3 cho Client-B vào SOCIAL_SITE chỉ trong giờ nghỉ (kiểm thử bằng TEST_NOW); Kịch bản 4 chỉ cho HTTP/HTTPS trong giờ hành chính; Kịch bản 5 cô lập DMZ khỏi LAN.

- Kết quả thực hiện:
Kịch bản 1 PASS - Client-A truy cập được 172.16.6.10 nhưng bị chặn khi truy cập 172.16.6.20; Firewall Log ghi nhận gói từ 10.6.0.21 tới 172.16.6.20 bị Block.
Kịch bản 2 PASS - cả Client-A và Client-B đều bị chặn tại 172.16.6.40 nhưng vẫn truy cập được 172.16.6.10.
Kịch bản 3 PASS - khi TEST_NOW Active, Client-B truy cập được 172.16.6.30; sau khi dời TEST_NOW ra ngoài thời điểm hiện tại và tạo phiên mới, truy cập bị chặn bởi rule Block nằm phía dưới; cuối cùng rule được gắn lại LUNCH_WINDOW.
Kịch bản 4 PASS - trong WORK_HOURS, truy cập HTTP/HTTPS hoạt động còn ping bị Block; khi chỉnh WORK_HOURS ra ngoài thời điểm hiện tại, truy cập HTTPS bị chặn.
Kịch bản 5 PASS - baseline: DMZ-WEB kết nối được cổng TCP 389 của DC01 khi chỉ có rule Pass; sau khi thêm rule Block DMZ net -> LAN net phía trên rule Pass và Reset States, kết nối thất bại; Client-A vẫn truy cập được 172.16.6.10 theo rule trên tab LAN.

- Các lưu ý cần thiết để giảng viên kiểm tra hoặc chạy lại bài làm:
Môi trường dùng VMware Workstation: NAT Network của VirtualBox tương ứng VMnet8 (NAT) với dải 192.168.116.0/24 thay cho 192.168.250.0/24 của đề; Internal Network tương ứng LAN Segment; Host-Only tương ứng VMnet3. Vì WAN nằm trong dải địa chỉ private nên đã tắt tùy chọn Block private networks và Block bogon networks trên interface WAN.

Client-A dùng máy Kali Linux và Client-B dùng bản clone của Kali thay cho Windows 11 do điều kiện máy ảo sẵn có; policy khớp theo IP tĩnh (10.6.0.21 và 10.6.0.22) nên không phụ thuộc hệ điều hành. DC01 và DMZ-WEB tái sử dụng VM Windows Server 2022 và Ubuntu Server có sẵn.
