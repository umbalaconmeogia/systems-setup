# Installation Guide for Remote AI Worker Environment

This document provides step-by-step instructions for installing and configuring the necessary applications across all devices in the Remote AI Worker architecture.
👉 *(Lưu ý: Đây là tài liệu chứa các dòng lệnh cài đặt chi tiết để xây dựng môi trường theo kiến trúc được mô tả trong file [SetupRemoteAIWorkerEnvironment.md](SetupRemoteAIWorkerEnvironment.md)).*

---

## 1. Trên Worker Node (Ubuntu Server 26.04)

### 1.1. Cập nhật hệ thống
Luôn bắt đầu bằng việc cập nhật hệ điều hành:
```bash
sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y
```

### 1.2. Cấu hình chống ngủ đông (Prevent Sleep on Lid Close)
Vì Worker Node là một chiếc laptop Lenovo, ta cần thiết lập để hệ thống không tự động đưa vào trạng thái Sleep khi gập màn hình:
```bash
sudo nano /etc/systemd/logind.conf
# Tìm và chỉnh sửa các dòng sau (nhớ xóa dấu # ở đầu):
# HandleLidSwitch=ignore
# HandleLidSwitchExternalPower=ignore
# HandleLidSwitchDocked=ignore

# Lưu file (Ctrl+O, Enter) và Thoát (Ctrl+X)
# Khởi động lại service để áp dụng thay đổi:
sudo systemctl restart systemd-logind.service
```

### 1.3. OpenSSH Server
Cần thiết để cho phép máy Client remote vào.
```bash
sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh
```

### 1.4. Tailscale (Mạng VPN)
Cài đặt Tailscale để tạo mạng nội bộ (VPN) an toàn kết nối các thiết bị.
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```
*(Lưu ý: Sau khi chạy `sudo tailscale up`, terminal sẽ in ra một đường link. Copy đường link đó dán vào trình duyệt để xác thực).*

### 1.5. Docker & Docker Compose
Sử dụng script chính thức để cài đặt nhanh Docker:
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Cấp quyền cho user hiện tại chạy docker không cần sudo
sudo usermod -aG docker $USER
# (Quan trọng: Bạn phải logout ra và login lại SSH để quyền này có hiệu lực)
```

### 1.6. Node.js & npm (Môi trường Dev & Claude CLI)
```bash
# Cài đặt Node.js (Ví dụ bản 20.x LTS)
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### 1.7. Claude CLI / Claude Code
Cài đặt các công cụ AI CLI thông qua npm:
```bash
# Tùy thuộc vào package chính xác bạn dùng, ví dụ:
npm install -g @anthropic-ai/claude-cli
```

### 1.8. n8n (Tự động hóa workflow)
Triển khai n8n thông qua Docker Compose:
```bash
# Tạo thư mục chứa dữ liệu n8n
mkdir -p ~/n8n-docker
cd ~/n8n-docker

# Tạo file docker-compose.yml
cat <<EOF > docker-compose.yml
version: '3.8'

volumes:
  n8n_data:

services:
  n8n:
    image: docker.n8n.io/n8nio/n8n
    restart: always
    ports:
      - "5678:5678"
    volumes:
      - n8n_data:/home/node/.n8n
EOF

# Khởi chạy n8n chạy nền
docker compose up -d
```
*(Cursor Server sẽ tự động được cài khi máy Windows Client connect vào).*

---

## 2. Trên Client Node 1 (Windows 11 HP Laptop)

### 2.1. Tailscale
* Tải bản cài đặt cho Windows từ [Tailscale Download](https://tailscale.com/download/windows).
* Cài đặt và đăng nhập cùng một tài khoản Tailscale đã dùng trên Ubuntu.
* Lấy địa chỉ IP Tailscale của máy Ubuntu (ví dụ: `100.x.y.z`) để chuẩn bị cho bước SSH.

### 2.2. Cursor IDE & Remote-SSH
* Tải và cài đặt [Cursor IDE](https://cursor.sh/).
* Mở Cursor, vào phần **Extensions** (Ctrl+Shift+X) tìm và cài đặt tiện ích **Remote - SSH** (của Microsoft).
* Click vào biểu tượng `><` màu xanh ở góc dưới cùng bên trái màn hình Cursor.
* Chọn **Connect to Host...** -> **Add New SSH Host...**
* Nhập lệnh SSH: `ssh username_ubuntu@100.x.y.z` (thay IP và username tương ứng).
* Chọn hệ điều hành đích là `Linux`. Lần đầu kết nối, Cursor sẽ tự động tải thư viện Cursor Server lên máy Ubuntu. Mọi thao tác code, terminal mở trong Cursor từ giờ đều là của máy Ubuntu.

### 2.3. Trình duyệt Web (Quản lý n8n)
* Mở Chrome/Edge, truy cập `http://100.x.y.z:5678` (Thay IP Tailscale của Ubuntu) để mở Web UI của n8n và setup tài khoản admin.

---

## 3. Trên Client Node 2 (Smartphone)

### 3.1. Tailscale App
* **iOS:** Tải từ App Store.
* **Android:** Tải từ Google Play.
* Đăng nhập và bật (Connect) VPN. 

### 3.2. Termius (SSH Client khẩn cấp)
* Tải ứng dụng **Termius** từ App Store / Google Play.
* Tạo một Host mới:
  * **IP/Hostname:** IP Tailscale của Ubuntu Server (`100.x.y.z`).
  * **Username & Password:** Thông tin đăng nhập Ubuntu.
* Giờ đây bạn có thể mở terminal lên điện thoại để gõ lệnh bất kỳ lúc nào.

### 3.3. Trình duyệt Web (Kiểm tra Workflow)
* Bật Tailscale VPN trên điện thoại.
* Mở Safari/Chrome truy cập `http://100.x.y.z:5678` để kiểm tra, phê duyệt các task n8n đang chạy ngay cả khi đang đi trên đường.
