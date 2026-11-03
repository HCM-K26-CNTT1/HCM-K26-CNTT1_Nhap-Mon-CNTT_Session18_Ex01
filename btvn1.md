# BÀI 1: SỰ CỐ KHÔNG TRUY CẬP ĐƯỢC HỆ THỐNG QUẢN TRỊ

# PHẦN 1. PHÂN TÍCH

## 1.1. Input và Output

DNS là hệ thống chuyển đổi tên miền thành địa chỉ IP để trình duyệt tìm đến máy chủ.

| Thành phần | Nội dung |
|---|---|
| Input | phong-dieu-phoi.greenlog.vn |
| Output | Địa chỉ IP của máy chủ |
| Quá trình | DNS Lookup |
| Kết quả lỗi | DNS_PROBE_FINISHED_NXDOMAIN |

## 1.2. Quy trình DNS Lookup

| Bước | Thành phần | Chức năng |
|---|---|---|
| 1 | Browser Cache | Kiểm tra DNS đã lưu trong trình duyệt. |
| 2 | OS Cache | Kiểm tra DNS đã lưu trong máy tính. |
| 3 | DNS Resolver | Tiếp nhận yêu cầu tìm IP. |
| 4 | Root DNS | Tìm máy chủ tên miền cấp cao. |
| 5 | TLD DNS | Tìm máy chủ quản lý tên miền .vn. |
| 6 | Authoritative DNS | Trả về địa chỉ IP. |
| 7 | Browser | Sử dụng IP để kết nối máy chủ. |

## 1.3. Sơ đồ DNS

```text
Nhập tên miền
      |
      v
Kiểm tra DNS Cache
      |
      v
  DNS Resolver
      |
      v
   Root DNS
      |
      v
    TLD DNS
      |
      v
Authoritative DNS
      |
      v
   Trả về IP
      |
      v
Kết nối máy chủ
```

## 1.4. Nguyên nhân lỗi

Nam nhập sai địa chỉ:

- Địa chỉ sai: phong-dieu-phoi.grenlog.vn
- Địa chỉ đúng: phong-dieu-phoi.greenlog.vn

Nam thiếu một chữ "e" trong tên miền.

Hậu quả: DNS không tìm thấy tên miền phù hợp nên trình duyệt báo lỗi DNS_PROBE_FINISHED_NXDOMAIN.

**Kết luận:** Lỗi do nhập sai tên miền ở phía người dùng, chưa có căn cứ kết luận máy chủ bị sập.

## 1.5. Hình ảnh lỗi DNS

file: 01-dns-error.png

Hình 1. Trình duyệt báo lỗi DNS khi nhập sai tên miền.

# PHẦN 2. TRIỂN KHAI

## 2.1. Phân biệt lỗi DNS và HTTP 500

| Tiêu chí | Lỗi DNS | HTTP 500 |
|---|---|---|
| Nguyên nhân | Không tìm thấy tên miền | Máy chủ gặp lỗi |
| Kết nối máy chủ | Chưa kết nối được đến máy chủ đích | Đã gửi yêu cầu đến máy chủ |
| Mã HTTP | Không có mã HTTP từ máy chủ đích | 500 |
| Vị trí lỗi | DNS hoặc tên miền | Server |
| Cách xử lý | Kiểm tra tên miền | Kiểm tra máy chủ |

## 2.2. Cách kiểm tra bằng F12

1. Mở Chrome và nhấn F12.
2. Chọn tab Network.
3. Tải lại trang bằng Ctrl + R.
4. Chọn một Request.
5. Chọn Headers.
6. Xem mục Status Code.

## 2.3. Hình ảnh kiểm tra Network

file: 02-network-tab.png

Hình 2. Tab Network dùng để theo dõi các yêu cầu mạng.

## 2.4. Bảng HTTP Status Code

| Mã | Ý nghĩa |
|---|---|
| 200 | Truy cập thành công. |
| 301 | Chuyển hướng vĩnh viễn. |
| 403 | Không có quyền truy cập. |
| 404 | Không tìm thấy trang. |
| 500 | Máy chủ gặp lỗi nội bộ. |
| 503 | Máy chủ tạm thời không hoạt động. |

## 2.5. Hình ảnh kiểm tra HTTP Status Code

file: images/03-http-status.png

Hình 3. Kiểm tra mã HTTP Status Code trong Headers → General.

# PHẦN 3. KIỂM THỬ

## 3.1. Tình huống

Nam sửa đúng tên miền nhưng vẫn nhận lỗi 404 Not Found.

## 3.2. Bảng kết quả kiểm thử

| Nội dung | Kết quả |
|---|---|
| Tên miền | Đã nhập đúng |
| DNS Lookup | Thành công |
| Kết nối máy chủ | Đã gửi yêu cầu HTTP |
| HTTP Status Code | 404 Not Found |
| Nguyên nhân | Không tìm thấy trang hoặc tài nguyên |
| Cách xử lý | Kiểm tra lại đường dẫn URL |

## 3.3. Kết luận

Lỗi 404 cho biết trình duyệt đã gửi yêu cầu đến máy chủ nhưng máy chủ không tìm thấy trang được yêu cầu.

Nguyên nhân có thể do nhập sai đường dẫn, trang đã bị xóa hoặc địa chỉ trang đã thay đổi.

# Tổng kết cuối bài

Qua bài tập, em hiểu được cách DNS chuyển đổi tên miền thành địa chỉ IP và cách sử dụng F12 để kiểm tra yêu cầu mạng.

Em phân biệt được:

- DNS NXDOMAIN: Không phân giải được tên miền.
- HTTP 404: Không tìm thấy trang.
- HTTP 500: Máy chủ gặp lỗi nội bộ.

Khi gặp sự cố, cần kiểm tra tên miền trước, sau đó kiểm tra Network và HTTP Status Code để xác định nguyên nhân.

