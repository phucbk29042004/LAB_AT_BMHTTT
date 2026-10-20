- Họ và tên sinh viên: Huỳnh Ngọc Phúc
- Mã số sinh viên: 1150070035
- Tên bài Lab: Bắt gói tin Telnet - SSH
- Nội dung đã thực hiện:
Bài thực hành mô phỏng một mô hình mạng gồm hai máy ảo: máy Ubuntu Server 26.04.1 LTS đóng vai trò máy chủ (IP 192.168.116.130), và máy Kali Linux 2026.2 đóng vai trò vừa là Client vừa là Attacker. Trên máy Server, tài khoản chính đang dùng là huynhngocphuc, và một tài khoản riêng để thử nghiệm đăng nhập từ xa đã được tạo với tên uitlab, ban đầu dùng mật khẩu là mã số sinh viên theo yêu cầu đề bài, sau đó được đổi sang một mật khẩu phức tạp hơn (trên 10 ký tự, gồm chữ hoa, chữ thường, số và ký tự đặc biệt) ở bước kiểm tra Telnet lần hai.

- Kết quả thực hiện :
Với phiên Telnet, kết quả Follow TCP Stream cho thấy toàn bộ nội dung trao đổi — bao gồm username, password (cả ở lần mật khẩu đơn giản và lần mật khẩu đã đổi phức tạp hơn), cùng các lệnh đã gõ và kết quả trả về từ Server — đều hiển thị dưới dạng văn bản đọc được trực tiếp, không qua bất kỳ lớp mã hóa nào. Điều này chứng minh rằng độ phức tạp của mật khẩu không cải thiện được tính bảo mật của giao thức Telnet, vì lỗ hổng nằm ở cách truyền tải dữ liệu dưới dạng plaintext, không nằm ở độ mạnh của mật khẩu.

Với phiên SSH, kết quả Follow TCP Stream chỉ hiển thị được dòng banner phiên bản (dạng SSH-2.0-OpenSSH_x.x) ở phần đầu, còn lại là dữ liệu đã được mã hóa và không thể đọc được nội dung, bao gồm cả username, password và các lệnh đã thực hiện. Tuy nhiên, một số metadata vẫn quan sát được dù nội dung đã mã hóa, gồm địa chỉ IP nguồn/đích, cổng kết nối, thời điểm và kích thước từng gói tin, cũng như quá trình bắt tay trao đổi khóa.

Với phần xác thực bằng public key, sau khi hoàn tất việc tạo khóa và sao chép public key sang Server, lần đăng nhập SSH tiếp theo diễn ra thành công mà không yêu cầu nhập mật khẩu, xác nhận đúng nguyên lý hoạt động của phương thức xác thực này.

Tổng thể, kết quả thực hành khẳng định rõ sự khác biệt về bảo mật giữa hai giao thức: Telnet không đảm bảo được tính bí mật do truyền dữ liệu dạng plaintext, trong khi SSH đảm bảo được tính bí mật và toàn vẹn của nội dung phiên làm việc nhờ cơ chế mã hóa, dù vẫn còn để lộ một số thông tin metadata có thể bị khai thác cho mục đích phân tích lưu lượng.

- Các lưu ý cần thiết để giảng viên kiểm tra hoặc chạy lại bài làm:
Mô hình mạng gồm 2 máy ảo VMware: Ubuntu Server 26.04.1 LTS (IP 192.168.116.130) và Kali Linux 2026.2, cả hai được cấu hình cùng chế độ Network Adapter (NAT) để đảm bảo thông mạng với nhau.

Tài khoản thử nghiệm trên Server: username uitlab. Mật khẩu ban đầu là mã số sinh viên; mật khẩu sau khi đổi là mật khẩu phức tạp hơn 10 ký tự (được dùng cho lần bắt gói Telnet thứ hai và toàn bộ phần SSH).

Trong quá trình cài đặt Telnet, cổng 23 từng không mở dù đã cài đúng gói inetutils-telnetd, do dòng cấu hình dịch vụ trong file /etc/inetd.conf bị đánh dấu tắt bằng ký hiệu #<off>. Đã khắc phục bằng cách xóa dòng cấu hình cũ và thêm lại dòng cấu hình mới, sau đó khởi động lại dịch vụ inetutils-inetd. Nếu giảng viên chạy lại và gặp tình trạng cổng 23 không LISTEN, có thể kiểm tra lại nội dung file này.

Toàn bộ quá trình bắt gói tin được thực hiện bằng Wireshark chạy trực tiếp trên máy Kali (đóng vai trò cả Client và Attacker), do switch ảo của VMware không đảm bảo máy Attacker độc lập nhìn thấy được lưu lượng unicast giữa hai máy khác nếu tách riêng 3 vai trò.