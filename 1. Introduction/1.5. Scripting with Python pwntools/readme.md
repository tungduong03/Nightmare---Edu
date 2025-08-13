# 🔧 Giới thiệu Pwntools

Pwntools là một thư viện Python chuyên cho CTF (Capture The Flag) và phát triển exploit nhanh chóng.
Nó giúp:

- Kết nối tới các dịch vụ mạng (TCP/UDP).

- Gửi/nhận dữ liệu dễ dàng.

- Chạy file nhị phân và tương tác.

- Đính kèm GDB để debug trực tiếp.

- Chuyển đổi dữ liệu (packing/unpacking) phục vụ khai thác.

⚠️ Phiên bản:

Có bản cho Python2 và Python3.

📦 Cài đặt Pwntools: `sudo pip install pwn`

📥 Import vào Python: `from pwn import *`

🌐 Kết nối đến server: Kết nối TCP tới github.com port 9000: `target = remote("github.com", 9000)`

🖥 Chạy file nhị phân: `target = process("./challenge")`

    process() sẽ chạy file cục bộ.

    Rất tiện khi bạn debug/chạy exploit thử trước khi tấn công server thật.

🐞 Gắn GDB để debug 

Gắn GDB vào process đang chạy: `gdb.attach(target)`

Gắn GDB và đặt breakpoint ngay tại hàm main: `gdb.attach(target, gdbscript='b *main')`

📡 Gửi dữ liệu đến target

Gửi dữ liệu thô: `target.send(x)`

Gửi dữ liệu + xuống dòng (\n): `target.sendline(x)`



