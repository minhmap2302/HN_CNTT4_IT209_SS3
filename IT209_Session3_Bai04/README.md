# Session 03 - Bài 4: Chạy song song nhiều cổng dịch vụ (Nginx Virtual Hosts 8080 & 8090)

## 1. Mục tiêu
- Cấu hình Nginx chạy song song hai trang web tĩnh độc lập trên cùng một địa chỉ IP máy chủ ảo thông qua việc phân chia cổng dịch vụ (`8080` và `8090`).
- Thiết lập tường lửa UFW và Cloud Firewall mở cổng tương ứng.

## 2. Các bước thực hiện

### Bước 1: Tạo thư mục lưu trữ mã nguồn cho 2 trang web
```bash
sudo mkdir -p /var/www/beta-app/html
sudo mkdir -p /var/www/internal-app/html

sudo cp beta-app-index.html /var/www/beta-app/html/index.html
sudo cp internal-app-index.html /var/www/internal-app/html/index.html
```

### Bước 2: Tạo cấu hình Virtual Hosts `multi-port.conf`
Tạo tệp `/etc/nginx/sites-available/multi-port.conf`:
```nginx
server {
    listen 8080;
    server_name _;
    root /var/www/beta-app/html;
    index index.html;
    location / { try_files $uri $uri/ =404; }
}

server {
    listen 8090;
    server_name _;
    root /var/www/internal-app/html;
    index index.html;
    location / { try_files $uri $uri/ =404; }
}
```

### Bước 3: Mở cổng trên tường lửa UFW & Reload Nginx
```bash
sudo ufw allow 8080/tcp
sudo ufw allow 8090/tcp
sudo ufw reload

sudo ln -s /etc/nginx/sites-available/multi-port.conf /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

## 3. Kết quả kiểm tra

### Lệnh 1: Truy cập Beta App ở cổng 8080
```bash
curl http://128.199.200.100:8080
```
**Kết quả:** Trả về giao diện HTML của `Beta App Environment (Port 8080)`.

### Lệnh 2: Truy cập Internal App ở cổng 8090
```bash
curl http://128.199.200.100:8090
```
**Kết quả:** Trả về giao diện HTML của `Internal App Information (Port 8090)`.
