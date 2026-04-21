# Peer Review Response

## Thông tin nhóm
- Thành viên 1: Đoàn Quốc Bảo - MSSV: 1871020071
- Thành viên 2: Nguyễn Đăng Quang - MSSV: 1871020481

## Thành viên 1 góp ý cho thành viên 2
(Bảo góp ý cho Quang): Việc sử dụng lệnh `conn.recv()` thông thường trong file `receiver.py` ban đầu chưa an toàn. Dữ liệu TCP có thể bị phân mảnh, dẫn đến việc giải mã bị lỗi do nhận thiếu byte Ciphertext. Đề xuất viết vòng lặp `recv_exact` để đảm bảo chắc chắn nhận đủ dữ liệu.

## Thành viên 2 góp ý cho thành viên 1
(Quang góp ý cho Bảo): Khi dùng `struct.pack` để ép kiểu độ dài (length) vào header, nên sử dụng format `!I` (Big-Endian) thay vì `I`. Điều này giúp hệ thống hoạt động ổn định kể cả khi Sender gửi từ máy Windows và Receiver chạy trên môi trường Linux.

## Nhóm đã sửa gì sau góp ý
Nhóm đã đồng thuận tạo riêng một file `des_socket_utils.py` để chứa hàm `recv_exact` dùng chung và chuẩn hóa toàn bộ cấu trúc Header theo Network Byte Order (`!I`). Sau khi tinh chỉnh, các integration test chạy local đều không còn hiện tượng rớt packet. Nhóm cũng viết thêm file `test_padding_and_header.py` để test riêng chức năng này.