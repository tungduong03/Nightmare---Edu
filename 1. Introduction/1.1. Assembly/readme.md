## 1. Giới thiệu về Assembly

👉 https://www.youtube.com/watch?v=75gBFiFtAb8

## 2. Compilation – Quá trình biên dịch

Assembly là ngôn ngữ gần với máy nhất mà CPU có thể hiểu được.

Ví dụ đoạn mã C:

```c
#include <stdio.h>

void main(void) {
    puts("Hello World!");
}
```

Đoạn mã này không chạy trực tiếp. Trình biên dịch sẽ biên dịch mã C thành mã Assembly, ví dụ:

```asm
0000000000001135 <main>:
1135:  55                    push   rbp
1136:  48 89 e5              mov    rbp,rsp
1139:  48 8d 3d ...          lea    rdi,[rip+0xec4]
1140:  e8 eb fe ff ff        call   1030 <puts@plt>
...
```

Trình biên dịch (compiler) chuyển C → Assembly → mã máy để CPU chạy.

Vì sao phải học Assembly?

- Khi bạn làm việc với file nhị phân đã biên dịch, bạn chỉ còn mã Assembly để phân tích.

- Công cụ như Ghidra, IDA, hay Radare2 có thể hỗ trợ bạn dịch ngược sang dạng giống C, nhưng vẫn cần hiểu Assembly để phân tích chính xác.

## 3. Kiến trúc & Tập lệnh
Có nhiều kiến trúc CPU khác nhau (x86, x64, ARM...). Ở đây ta tập trung vào:

- x86: 32-bit

- x64 (x86_64): 64-bit

→ Cả hai đều dùng định dạng ELF (Executable and Linkable Format).

## 4. Thanh ghi (Registers)

Thanh ghi là vùng nhớ nhỏ, truy cập nhanh trong CPU.

Một số thanh ghi phổ biến:

| Thanh ghi | Vai trò                                                  |
| --------- | -------------------------------------------------------- |
| `rbp`     | **Base Pointer** – chỉ đến đáy của stack frame hiện tại  |
| `rsp`     | **Stack Pointer** – chỉ đến đỉnh của stack               |
| `rip`     | **Instruction Pointer** – chỉ đến lệnh sắp được thực thi |


Thanh ghi đa dụng (General Purpose):
```
rax, rbx, rcx, rdx, rsi, rdi, r8, r9, r10, r11, r12, r13, r14, r15
```
