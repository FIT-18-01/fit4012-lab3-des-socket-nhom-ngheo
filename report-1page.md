# Report 1 page - Lab 3

## Thông tin nhóm
- Thành viên 1: Đoàn Quốc Bảo - MSSV: 1871020071
- Thành viên 2: Nguyễn Đăng Quang - MSSV: 1871020481

## Mục tiêu
Thực hành xây dựng hệ thống mạng gửi nhận qua socket dùng TCP. Áp dụng thuật toán mã hóa DES-CBC với đệm PKCS#7 để bảo mật nội dung gói tin. Thông qua việc tự thiết kế giao thức (Header chứa Key, IV và Length), sinh viên nhận thức được lỗ hổng bảo mật chí mạng khi truyền khóa công khai trên đường truyền mạng.

## Phân công thực hiện
- **Đoàn Quốc Bảo (Thành viên 1):** Phụ trách xây dựng `sender.py`, các hàm mã hóa DES, tạo gói tin (build_packet) và padding.
- **Nguyễn Đăng Quang (Thành viên 2):** Phụ trách `receiver.py`, xử lý TCP socket an toàn (dùng `recv_exact`), bóc tách gói tin và giải mã.
- **Làm chung:** Cùng xây dựng thư viện `des_socket_utils.py`, viết kiểm thử (tests), chạy lấy log và thảo luận viết báo cáo Threat Model.

## Cách làm
Sử dụng thư viện `pycryptodome` cho DES-CBC và `socket` cho kết nối TCP. 
- **Sender:** Sinh Key và IV (8 bytes) ngẫu nhiên, mã hóa chuỗi đầu vào kèm padding PKCS#7. Đóng gói Header dài 20 bytes (8 bytes Key + 8 bytes IV + 4 bytes Length chuẩn Big-Endian) nối với Ciphertext rồi gửi qua TCP.
- **Receiver:** Lắng nghe kết nối, dùng vòng lặp `recv_exact` để đảm bảo đọc đúng 20 bytes Header. Bóc tách lấy Key, IV và độ dài bản mã, sau đó đọc tiếp đủ số byte bản mã để giải mã ngược lại ra plaintext.

## Kết quả
Hệ thống chạy ổn định trên môi trường local. 
- Script tích hợp báo nhận/gửi chuẩn xác không thất thoát byte nào. 
- Hệ thống CI pass 100% (bao gồm 6 file test).
- Các ca kiểm thử tiêu cực (Negative tests) như `wrong_key` và `tamper` đều ném ra lỗi `ValueError` hợp lý (do sai padding PKCS#7), giúp chương trình không bị crash ngầm.

## Kết luận
Qua bài lab, nhóm đã thành thạo kỹ năng thao tác với byte, socket, byte-order (Big-Endian) và thuật toán mã hóa. Bài học bảo mật lớn nhất rút ra là: Một thuật toán mã hóa mạnh đến đâu cũng sẽ vô nghĩa nếu giao thức phân phối khóa (Key Exchange) bị thiết kế sai lầm (như việc gửi kèm Key trong Header ở bài lab này).