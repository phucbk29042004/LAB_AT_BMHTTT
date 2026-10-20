- Họ và tên sinh viên: Huỳnh Ngọc Phúc
- Mã số sinh viên: 1150070035
- Tên bài Lab: Thiết lập mô hình tường lửa pfSense (Lab 5)

- Nội dung đã thực hiện:
Bài thực hành dùng VMware Workstation thay cho VirtualBox theo đề gốc, và tái sử dụng VM Windows Server 2022 đã có sẵn để làm Domain Controller thay vì dựng mới. Theo yêu cầu, chỉ thực hiện Tình huống 1, 2 và 3 trong phần bài tập.

Đã thực hiện xong Tình huống 1 (Block ICMP, Pass DNS/HTTP), Tình huống 2 (chỉ một host ra Internet) và Tình huống 3 (cô lập DMZ khỏi LAN).

- Kết quả thực hiện:
TH1 PASS - sau khi tạo 3 rule theo đúng thứ tự (Block ICMP, Pass DNS, Pass HTTP/HTTPS), ping từ Domain Controller tới 8.8.8.8 thất bại đúng như mong đợi, trong khi Resolve-DnsName và curl.exe tới example.com đều thành công.

TH2 PASS - sau khi tạo rule Pass 10.0.0.2 -> Any và Block LAN net -> Any, ping 8.8.8.8 từ Domain Controller (10.0.0.2) thành công, còn từ LAN-Test (10.0.0.3) thất bại đúng như mong đợi.

TH3 PASS - đã bật rule cho phép ICMP trên Windows Firewall của Domain Controller trước khi test. Baseline: với chỉ rule Pass DMZ net -> Any, ping từ DMZ-Web (172.16.0.2) tới Domain Controller (10.0.0.2) thành công. Sau khi thêm rule Block DMZ net -> LAN net phía trên rule Pass, Reset States và chạy lại lệnh ping mới, kết quả thất bại đúng như mong đợi, trong khi ping 8.8.8.8 từ DMZ-Web vẫn thành công, xác nhận DMZ vẫn ra Internet được nhưng bị chặn vào LAN.

- Các lưu ý cần thiết để giảng viên kiểm tra hoặc chạy lại bài làm:
Môi trường dùng VMware Workstation thay VirtualBox: Bridged/Host-only/Internal Network của VirtualBox tương ứng Bridged/Host-only/Custom LAN Segment trong VMware. Domain Controller tái sử dụng VM Windows Server 2022 có sẵn thay vì dựng mới.

Máy thật không có xung đột địa chỉ mạng với dải LAN 10.0.0.0/8 và DMZ 172.16.0.0/16 của bài lab, nên giữ nguyên địa chỉ gốc của đề. VMnet2 (Host-only, dải 10.0.0.0/8) được tạo mới riêng cho bài lab này, đã tắt DHCP theo đúng yêu cầu đề bài.
