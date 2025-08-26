# pico ctf 2018 strings

Mục tiêu của challenge này là tìm flag.

## 🔍 Bước 1: Kiểm tra file nhị phân

`file <tên files>`

```bash
$ file strings
strings: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=e337b489c47492dd5dff90353eb227b4e7e69028, not stripped
```

👉 Giải thích:

Đây là một `ELF 64-bit` (Linux Executable).

`LSB` = `Little Endian` (kiểu lưu trữ byte nhỏ trước).

`not stripped` = binary chưa bị “xóa symbol/debug info”, có thể chứa thông tin/chuỗi dễ đọc.


## 🔍 Bước 2: Chạy thử binary

```bash
$ ./strings
Have you ever used the 'strings' function? Check out the man pages!
```

👉 Giải thích:

Chạy chương trình, nó gợi ý chúng ta dùng `strings`.

`strings` là 1 tool trong Linux để quét file nhị phân, hiển thị ra các chuỗi ASCII đọc được.

Nhiều công cụ phân tích binary như Ghidra, Binary Ninja, IDA Pro cũng có chức năng này.

## 🔍 Bước 3: Dùng lệnh strings

Ta chạy strings trên file `strings`, rồi dùng grep để lọc ra flag (flag thường có dạng {} trong picoCTF):

```bash
$ strings strings | grep {
picoCTF{sTrIngS_sAVeS_Time_3f712a28}
```

👉 Giải thích:

strings strings: liệt kê tất cả chuỗi ASCII trong file strings.

`| grep {`: lọc những dòng có ký tự `{` → thường là flag.










