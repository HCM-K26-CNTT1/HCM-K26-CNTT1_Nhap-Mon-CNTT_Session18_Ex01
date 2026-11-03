# Bài 1: Sự cố không truy cập được hệ thống quản trị

## Phần 1. Phân tích

1\. Input và Output

- Input: Tên miền `phong-dieu-phoi.grenlog.vn`.
- Output: Địa chỉ IP của máy chủ tương ứng.

DNS (Domain Name System) được ví như cuốn danh bạ Internet, giúp chuyển đổi tên miền thành địa chỉ IP để trình duyệt tìm đến máy chủ.

2\. Quy trình DNS Lookup

Khi Nam nhập tên miền vào trình duyệt, quá trình DNS Lookup diễn ra như sau:

1. Trình duyệt kiểm tra bộ nhớ DNS Cache.
2. Nếu chưa có kết quả, máy tính kiểm tra DNS Cache của hệ điều hành.
3. Nếu vẫn chưa có, máy tính gửi yêu cầu đến DNS Resolver.
4. DNS Resolver tìm kiếm thông tin qua các máy chủ DNS: Root Server, TLD Server và Authoritative DNS Server.
5. DNS trả về địa chỉ IP nếu tìm thấy tên miền.
6. Trình duyệt sử dụng địa chỉ IP để kết nối với máy chủ.

Sơ đồ luồng dữ liệu:

Nam nhập tên miền → Trình duyệt → DNS Resolver → Root Server → TLD Server → Authoritative DNS Server → Trả về IP → Kết nối máy chủ.

3\. Nguyên nhân lỗi

Nam nhập sai `grenlog.vn` thay vì `greenlog.vn` (thiếu chữ e).

Do tên miền sai nên DNS không tìm thấy địa chỉ IP tương ứng, dẫn đến lỗi `DNS_PROBE_FINISHED_NXDOMAIN`.

Đây là lỗi phân giải tên miền, không có nghĩa máy chủ công ty bị sập.

## Phần 2. Triển khai

Để kiểm tra yêu cầu đã đến máy chủ hay chưa, Nam thực hiện:

1. Sửa lại tên miền cho chính xác.
2. Truy cập lại trang quản trị.
3. Mở Developer Tools bằng phím F12.
4. Chọn tab Network và tải lại trang.
5. Kiểm tra mã trạng thái HTTP (Status Code).

Một số mã trạng thái thường gặp:

- 200 OK: Máy chủ xử lý yêu cầu thành công.
- 404 Not Found: Không tìm thấy tài nguyên được yêu cầu.
- 500 Internal Server Error: Máy chủ gặp lỗi khi xử lý yêu cầu.

Phân biệt lỗi DNS và lỗi 500:

- Lỗi DNS: Không tìm thấy địa chỉ IP nên trình duyệt chưa thể kết nối đến máy chủ.
- Lỗi 500: Yêu cầu đã đến máy chủ nhưng máy chủ gặp lỗi khi xử lý.

## Phần 3. Kiểm thử

Giả sử Nam sửa đúng tên miền nhưng vẫn nhận mã `404 Not Found`.

Điều này có nghĩa là DNS đã phân giải tên miền thành công và trình duyệt đã kết nối được với máy chủ.

Tuy nhiên, máy chủ không tìm thấy trang hoặc tài nguyên mà Nam yêu cầu.

Nguyên nhân có thể:

- Đường dẫn trang quản trị bị sai.
- Trang quản trị đã bị xóa hoặc di chuyển.
- Tài nguyên không tồn tại trên máy chủ.

Kết luận: Lỗi 404 thuộc về tài nguyên hoặc đường dẫn được yêu cầu, không phải lỗi phân giải DNS.