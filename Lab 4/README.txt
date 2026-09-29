- Họ và tên sinh viên: Huỳnh Ngọc Phúc
- Mã số sinh viên: 1150070035
- Tên bài Lab: Khảo sát và đánh giá bề mặt mạng bằng Nmap (Lab 4)

- Nội dung đã thực hiện:
Bài thực hành dùng VMware Workstation (Host-only network) thay cho VirtualBox theo đề gốc, và dùng Windows Server 2022 làm máy đích thay cho Metasploitable 2, do điều kiện máy ảo sẵn có. Máy Kali Linux đóng vai trò máy quét. Đã thực hiện: cài Nmap trên Kali, thiết lập mạng Host-only trong VMware, lấy IP thật của từng máy (Kali 192.168.28.128, Windows Server 192.168.28.129), kiểm tra kết nối bằng ping. Đã thực hiện host discovery toàn dải /24, so sánh các kỹ thuật quét TCP (-sT, -sS, FIN, Xmas, NULL, ACK), quét UDP có kiểm soát 20 cổng phổ biến, nhận diện dịch vụ và hệ điều hành (-sV, -O, -A), chạy NSE script kiểm tra SMB (smb-os-discovery, smb-vuln-ms17-010), xuất kết quả ra các định dạng text/XML/grepable/HTML, và thực hiện tình huống trước/sau hardening bằng cách tắt dịch vụ SMB (Stop-Service LanmanServer) rồi so sánh kết quả quét.

- Kết quả thực hiện:
Host discovery phát hiện 4 host đang hoạt động trong dải 192.168.28.0/24, gồm gateway, Windows Server, một địa chỉ khác và chính máy Kali.

Với các kỹ thuật quét TCP, TCP Connect scan và SYN scan cho kết quả giống nhau: duy nhất cổng 5985/tcp (wsman - WinRM) ở trạng thái open, 999 cổng còn lại filtered không phản hồi. FIN, Xmas và NULL scan đều trả về toàn bộ 1000 cổng ở trạng thái open|filtered, không phân biệt được cổng mở hay bị lọc. ACK scan trả về toàn bộ cổng ở trạng thái filtered.

Quét UDP 20 cổng phổ biến cho kết quả toàn bộ ở trạng thái open|filtered, đúng như đặc điểm chung của UDP scan do không có cơ chế bắt tay xác nhận như TCP.

Version detection xác định được dịch vụ Microsoft HTTPAPI httpd 2.0 trên cổng 5985, cùng thông tin OS là Windows. OS detection đưa ra phỏng đoán Microsoft Windows Server 2022 với độ tin cậy 96%, kèm các phỏng đoán khác có độ tin cậy thấp hơn (Windows 11 24H2, Windows Server 2016, Windows 11 21H2), không có kết quả khớp chính xác tuyệt đối do điều kiện quét không lý tưởng. Aggressive scan gộp thêm thông tin traceroute và HTTP header so với các lệnh riêng lẻ.

Hai NSE script kiểm tra SMB (smb-os-discovery và smb-vuln-ms17-010) đều không thu thập được thông tin, vì cổng 445 ở trạng thái filtered thay vì open, nên script không thể kết nối để kiểm tra.

Xuất kết quả thành công ra các định dạng .txt, .xml, .html và file grepable smb.txt, xác nhận qua lệnh liệt kê thư mục.

Với tình huống trước/sau hardening, sau khi tắt dịch vụ SMB trên Server, kết quả so sánh hai file before/after bằng lệnh diff chỉ cho thấy khác biệt về timestamp và thời gian quét, không có khác biệt nào về port state hay service - do lệnh quét before/after sử dụng -sV vốn không nhắm vào cổng SMB (445), nên việc tắt SMB không thể hiện qua kết quả quét này.

- Các lưu ý cần thiết để giảng viên kiểm tra hoặc chạy lại bài làm:
Môi trường dùng VMware Workstation với Host-only network (dải 192.168.28.0/24) thay cho VirtualBox; Windows Server 2022 dùng làm máy đích thay cho Metasploitable 2. Do đó một số kết quả khác với kỳ vọng của đề gốc (ít cổng mở hơn, MS17-010 không kiểm tra được do cổng SMB bị lọc) - đây là đặc điểm của một hệ thống Windows đã được hardening/firewall mặc định chặt, không phải lỗi thực hành.

Trong quá trình thực hành, gặp lỗi ping từ Kali sang Windows Server bị 100% packet loss dù hai máy đã cùng dải mạng, do Windows Firewall mặc định chặn ICMP. Đã khắc phục bằng lệnh netsh advfirewall firewall add rule name="Allow ICMPv4-In" protocol=icmpv4:8,any dir=in action=allow chạy trên Windows Server.
