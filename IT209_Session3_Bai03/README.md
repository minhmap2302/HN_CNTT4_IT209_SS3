# Session 03 - Bài 3: Bảo mật tài nguyên bằng HTTP Basic Authentication

## 1. Mục tiêu
- Bảo vệ thư mục nhạy cảm `/admin` trên Nginx bằng HTTP Basic Authentication.
- Sử dụng công cụ `htpasswd` để mã hóa mật khẩu an toàn và lưu tại `/etc/nginx/.htpasswd`.

## 2. Các bước thực hiện

### Bước 1: Cài đặt tiện ích `apache2-utils`
```bash
sudo apt update
sudo apt install apache2-utils -y
```

### Bước 2: Tạo tệp chứa tài khoản và mật khẩu băm
```bash
sudo htpasswd -c /etc/nginx/.htpasswd admin_user
```
(Nhập mật khẩu an toàn khi hệ thống yêu cầu).

### Bước 3: Cấu hình Nginx Location Block `/admin`
Thêm vào file cấu hình `/etc/nginx/sites-available/admin-auth.conf`:
```nginx
location /admin {
    auth_basic "Restricted Admin Area";
    auth_basic_user_file /etc/nginx/.htpasswd;
    try_files $uri $uri/ =404;
}
```

### Bước 4: Kiểm tra và Reload Nginx
```bash
sudo nginx -t
sudo systemctl reload nginx
```

## 3. Kết quả kiểm tra

### Lệnh 1: Truy cập không có Auth
```bash
curl -I http://128.199.200.100/admin
```
**Kết quả:**
```text
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Basic realm="Restricted Admin Area"
```

### Lệnh 2: Truy cập với tài khoản hợp lệ
```bash
curl -u admin_user:secret_password http://128.199.200.100/admin
```
**Kết quả:**
```text
HTTP/1.1 200 OK (hoặc 404 nếu chưa tạo file index trong /admin, không còn bị 401 Unauthorized)
```
