- Họ và tên sinh viên: Huỳnh Ngọc Phúc
- Mã số sinh viên: 1150070035
- Tên bài Lab: Xây dựng chính sách Web Protection trên mô hình pfSense + Web Proxy (Lab 7)
 
- Nội dung đã thực hiện:
Bài thực hành kế thừa hạ tầng mạng từ Lab 6 trên môi trường VMware Workstation. Mô hình gồm Firewall pfSense CE (pfSense-LAB6), Domain Controller DC01 (Windows Server 2022, đóng vai trò DNS/AD), DMZ-WEB là Ubuntu Server chạy Nginx để mô phỏng website lab7.test, hai máy trạm Client-A và Client-B, và một máy mới bắt buộc là WEB-PROXY (Ubuntu Server 24.04, IP 10.6.0.30, chạy Squid làm proxy cổng 3128 phía sau E2Guardian lọc nội dung ở cổng 8080, kèm ClamAV quét virus khi tải file). Theo đúng thiết kế của đề, không cài Squid/E2Guardian/SquidGuard trực tiếp lên pfSense vì các gói này đã bị Netgate đánh dấu deprecated; pfSense chỉ đóng vai trò firewall/gateway, toàn bộ việc lọc nội dung Web chạy trên WEB-PROXY riêng.

- Kết quả thực hiện:
WEB-PROXY hoạt động đúng luồng E2Guardian (8080) -> Squid (3128) -> pfSense; Client-A/B chỉ duyệt web được khi cấu hình đúng proxy, và bị chặn hoàn toàn khi cố tình tắt proxy để bypass, xác nhận ruleset trên pfSense chặn bypass hiệu quả.