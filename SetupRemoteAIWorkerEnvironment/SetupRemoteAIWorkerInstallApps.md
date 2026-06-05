# Installation Guide for Remote AI Worker Environment

This document provides step-by-step instructions for installing and configuring the necessary applications across all devices in the Remote AI Worker architecture.
👉 *(Lưu ý: Đây là tài liệu chứa các dòng lệnh cài đặt chi tiết để xây dựng môi trường theo kiến trúc được mô tả trong file [README.md](README.md)).*

---

## 1. Trên Worker Node (Ubuntu Server 26.04)

### 1.1. Cập nhật hệ thống
Luôn bắt đầu bằng việc cập nhật hệ điều hành:
```bash
sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y
```

> [!TIP]
> **Xử lý lỗi dpkg lock (`unattended-upgrades`) nếu gặp phải:**
> Khi mới khởi động Ubuntu Server hoặc sau một thời gian dài, bạn có thể gặp lỗi dạng:
> `E: Could not get lock /var/lib/dpkg/lock-frontend. It is held by process XXXX (unattended-upgr)`
>
> Đây là do tính năng cập nhật bảo mật tự động của Ubuntu đang chạy ngầm. Để xử lý:
> 1. **Giải pháp an toàn (Khuyên dùng):** Chờ khoảng 3-5 phút cho tiến trình tự hoàn thành. Bạn có thể kiểm tra trạng thái của nó bằng lệnh: `systemctl status unattended-upgrades`
> 2. **Giải pháp nhanh:** Dừng tạm thời dịch vụ cập nhật tự động bằng lệnh: `sudo systemctl stop unattended-upgrades`

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
*(Lưu ý: Sau khi chạy `sudo tailscale up`, terminal sẽ in ra một đường link. Bạn cần copy đường link đó dán vào trình duyệt để xác thực. Vì Ubuntu Server không có giao diện web browser, bạn có 3 cách để thực hiện việc này:
1. **Cách 1 (Khuyên dùng - SSH qua mạng LAN):** Trong lúc setup ban đầu khi 2 máy ở cạnh nhau, hãy dùng máy HP SSH vào IP nội bộ (ví dụ `192.168.1.x`) của máy Lenovo. Chạy lệnh trên, sau đó copy đường link hiển thị trong terminal dán vào trình duyệt trên máy HP.
2. **Cách 2 (Dùng QR Code):** Nếu bạn đang gõ trực tiếp trên màn hình của Lenovo, hãy chạy lệnh `sudo tailscale up --qr`. Terminal sẽ in ra một mã QR, bạn chỉ cần dùng điện thoại quét mã này để xác thực.
3. **Cách 3 (Quét chữ qua ảnh chụp - OCR):** Dùng smartphone chụp lại màn hình hiển thị đường link, sau đó sử dụng tính năng nhận diện chữ viết (OCR) tích hợp sẵn trên điện thoại hoặc trình duyệt để sao chép đường link và mở trên thiết bị).*

* **Cách lấy IP Tailscale của máy Ubuntu:**
  Để biết IP Tailscale của máy (dạng `100.x.y.z`), bạn chạy lệnh sau trên terminal của Ubuntu:
  ```bash
  tailscale ip -4
  ```
  Hoặc bạn có thể truy cập trang quản trị [Tailscale Admin Console](https://login.tailscale.com/admin/machines) từ bất kỳ thiết bị nào đã đăng nhập cùng tài khoản để xem IP của thiết bị.

### 1.5. Docker & Docker Compose
Sử dụng script chính thức để cài đặt nhanh Docker:
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Cấp quyền cho user hiện tại chạy docker không cần sudo
sudo usermod -aG docker $USER
# (Quan trọng: Bạn phải logout ra và login lại SSH để quyền này có hiệu lực)
```

### 1.6. Node.js & npm (Môi trường Dev)
Sử dụng **fnm (Fast Node Manager)** để cài đặt và quản lý các phiên bản Node.js.
* **Lý do lựa chọn**:
  * **Tránh lỗi phân quyền (Permission Denied)**: `fnm` cài đặt Node.js trực tiếp trong thư mục `$HOME` (`~/.fnm`). Nhờ đó, khi bạn cài đặt các package global (như Claude CLI/Claude Code) qua `npm install -g`, bạn sẽ không bao giờ cần sử dụng `sudo`, tránh được các xung đột và lỗi bảo mật liên quan đến quyền root của hệ thống.
  * **Quản lý linh hoạt**: `fnm` được phát triển bằng Rust nên cực kỳ nhanh, giúp bạn dễ dàng nâng cấp hoặc chuyển đổi giữa các phiên bản Node.js khác nhau cho từng dự án.

```bash
# Cài đặt fnm
curl -fsSL https://fnm.vercel.app/install | bash

# Áp dụng cấu hình shell để kích hoạt lệnh fnm ngay lập tức
source ~/.bashrc

# Cài đặt phiên bản Node.js LTS mới nhất
fnm install --lts

# Thiết lập phiên bản vừa cài làm mặc định (ví dụ: phiên bản 24 vừa tải ở trên)
fnm default 24
```

### 1.7. Claude Code / Claude CLI
Cài đặt công cụ AI CLI toàn cục (không dùng `sudo` nhờ có `fnm` quản lý):
```bash
npm install -g @anthropic-ai/claude-code
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
