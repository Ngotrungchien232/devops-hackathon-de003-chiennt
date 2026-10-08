# DevOps Hackathon - Đề 003: Quản lý công việc task

## 1. Thông tin sinh viên

| Họ và tên       | Lớp           | Tài khoản Linux  | GitHub           | Cổng Nginx |
| --------------- | ------------- | ---------------- | ---------------- | ---------- |
| Ngô Trung Chiến | HN_KS24_CNTT2 | chiennt-k24cntt2 | Ngotrungchien232 | 8088       |

## 2. Môi trường triển khai

- Hệ điều hành: Ubuntu 22.04 LTS
- Chạy trên: Azure VM
- Nginx, Git, UFW

## 3. Cấu trúc dự án

devops-hackathon-de003-chiennt/
├── src/
│ ── index.html
├── nginx/
│ ── chiennt-k24cntt2.conf
├── screenshots/
├── .gitignore
└── README.md

## 4. Cấu hình Nginx

| Tham số trong template | Giá trị đã điền                             | Giải thích                                 |
| ---------------------- | ------------------------------------------- | ------------------------------------------ |
| <PORT>                 | 8088                                        | Cổng riêng để chạy web, không dùng cổng 80 |
| <SERVER_NAME>          | \_                                          | Địa chỉ IP máy chủ                         |
| <WEB_ROOT>             | /var/www/devops-hackathon-de003-chiennt/src | Thư mục chứa file index.html               |
| <INDEX_FILE>           | index.html                                  | File trang chủ                             |
| <TEN_TAI_KHOAN>        | chiennt-k24cntt2                            | Tên tài khoản để đặt tên file log          |
| <ALLOW_DIRECTIVE>      | allow all;                                  | Cho phép mọi người truy cập                |

File nginx:

```nginx
server {
    listen 8088;
    listen [::]:8088;

    server_name _;

    root /var/www/devops-hackathon-de003-chiennt/src;
    index index.html;

    access_log /var/log/nginx/chiennt-k24cntt2.access.log;
    error_log /var/log/nginx/chiennt-k24cntt2.error.log;

    location / {
        allow all;
        try_files $uri $uri/ =404;
    }
}
```

## 5. Tường lửa UFW

Các rule đã thêm:

- sudo ufw allow 22/tcp
- sudo ufw allow 8088/tcp
- sudo ufw enable

Kết quả chạy sudo ufw status verbose:

```text
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
8088/tcp                   ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
8088/tcp (v6)              ALLOW IN    Anywhere (v6)
```

## 6. Các bước triển khai

Tạo user và cấp quyền:

```bash
sudo useradd -m -s /bin/bash chiennt-k24cntt2
sudo passwd chiennt-k24cntt2
sudo usermod -aG sudo chiennt-k24cntt2
su - chiennt-k24cntt2
```

Cài đặt phần mềm:

```bash
sudo apt update
sudo apt install -y nginx git ufw curl
sudo systemctl start nginx
sudo systemctl enable nginx
git config --global user.name "Ngotrungchien232"
git config --global user.email "ngotrungchien232@gmail.com"
```

```bash
sudo git clone https://github.com/Ngotrungchien232/devops-hackathon-de003-chiennt.git /var/www/devops-hackathon-de003-chiennt
sudo chown -R chiennt-k24cntt2:chiennt-k24cntt2 /var/www/devops-hackathon-de003-chiennt
sudo find /var/www/devops-hackathon-de003-chiennt -type d -exec chmod 755 {} +
sudo find /var/www/devops-hackathon-de003-chiennt -type f -exec chmod 644 {} +
```

Cấu hình nginx:

```bash
sudo cp /var/www/devops-hackathon-de003-chiennt/nginx/chiennt-k24cntt2.conf /etc/nginx/sites-available/
sudo ln -s /etc/nginx/sites-available/chiennt-k24cntt2.conf /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl reload nginx
```

Bật firewall ufw:

```bash
sudo ufw allow 22/tcp
sudo ufw allow 8088/tcp
sudo ufw enable
```

## 7. Minh chứng thực hiện chạy

- Phần 1.1 tạo tài khoản người dùng đã Done-creenshots/01-user.png
- Phần 1.2 Cài đặt phần mềm đã xong em để ảnh minh chứng trong
  screenshots/Phan-1.2
- Phần 3.3 đã xong có ảnh minh chứng trong screenshots/03-ufw.png

## 8. Quy trình cập nhật website

- Bước 1: sửa file index.html ở máy cá nhân rồi push lên github:

```bash
git add src/index.html
git commit -m "cap nhat lan 2"
git push origin main
```

- Bước 2: vào server pull code mới về:

```bash
cd /var/www/devops-hackathon-de003-chiennt
git pull
```

- Bước 3: kiểm tra lại trang web:
