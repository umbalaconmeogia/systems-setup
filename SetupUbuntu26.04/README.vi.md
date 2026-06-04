[English](README.md) | [Tiếng Việt](README.vi.md) | [日本語](README.ja.md)

# Cài đặt Ubuntu 26.04

## Tổng quan

Tài liệu này là ghi chú về việc cài đặt hệ điều hành Ubuntu 26.04 (cả bản Desktop và Server) và các ứng dụng trên máy tính cá nhân (PC) cục bộ.

* Cài đặt hệ điều hành với ổ cứng được mã hóa.
* Cài đặt ứng dụng và thiết lập.

## Cài đặt hệ điều hành với ổ cứng được mã hóa

Cài đặt Ubuntu tự động bằng cách sử dụng **autoinstall.yaml**.

* Sử dụng autoinstall.yaml là một trải nghiệm khó khăn. Tôi đã kết thúc với một cấu hình tối giản.
* Autoinstall trên Ubuntu 26.04 khác với trên Ubuntu 24.04.
  * Trên Ubuntu 26.04, chỉ cần đặt tệp autoinstall.yaml vào USB cài đặt, trình cài đặt Ubuntu sẽ tự động tìm thấy tệp đó và hỏi bạn có muốn sử dụng hay không (bạn có thể thêm tùy chọn khởi động autoinstall để tệp chạy tự động).
  * Trên Ubuntu 24.04, bạn nên tạo tệp *user-data* (giống như autoinstall.yaml) và một tệp trống *meta-data*, đặt chúng vào USB, và khi khởi động từ USB, hãy thêm tùy chọn *autoinstall ds=nocloud;s=/cdrom/*.

Trong ví dụ [autoinstall.yaml](autoinstall.yaml), chúng tôi tạo 3 phân vùng và để lại một khoảng trống không phân bổ.
| STT | Điểm gắn   | Kích thước | Mô tả                                                                                                                                                         |
| --- | ----------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | /boot/efi   | 1 GB       | Cài đặt GRUB                                                                                                                                                  |
| 2   | /           | 200 GB     | Cài đặt root Ubuntu                                                                                                                                           |
| 3   | /home       | 630 GB     | Lưu trữ /home, và được mã hóa bằng LUKS                                                                                                                       |
| 4   | chưa phân bổ| 100 GB     | Chúng tôi để lại 100 GB chưa phân bổ cho [SSD garbage collection](https://umbalaconmeogia.wordpress.com/2026/05/05/tai-sao-nen-chua-ra-10-unallocated-space-tren-ssd/) |

Để sử dụng autoinstall.yaml này, bạn phải thiết lập 3 giá trị:
1. Số sê-ri SSD (để giúp trình cài đặt tìm chính xác thiết bị cần định dạng).
2. Mật khẩu để mã hóa phân vùng gắn vào /home.
3. Mã băm mật khẩu cho người dùng Ubuntu. Để tạo mã băm, hãy sử dụng lệnh `openssl passwd -6 'your_password_here'`

## Cài đặt ứng dụng và thiết lập

Tham khảo SetupUbuntu26.04.md để biết các ghi chú về việc thiết lập môi trường Ubuntu 26.04.
