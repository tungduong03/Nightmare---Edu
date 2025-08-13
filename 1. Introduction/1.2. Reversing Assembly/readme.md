# Assembly Reversing Problems

Học cách đọc và hiểu mã assembly, thường thấy khi dịch ngược file nhị phân bằng công cụ như `objdump`.

## 1. Hello World

CMD: `objdump -D hello_world -M intel | less`

Câu lệnh trên dùng objdump để hiển thị toàn bộ mã máy (machine code) đã được dịch ngược ra assembly theo cú pháp Intel.


Phân tích hàm `main`:

```asm
080483fb <main>:
 80483fb:  lea    ecx,[esp+0x4]
 80483ff:  and    esp,0xfffffff0
 8048402:  push   DWORD PTR [ecx-0x4]
 8048405:  push   ebp
 8048406:  mov    ebp,esp
 8048408:  push   ecx
 8048409:  sub    esp,0x4
 804840c:  sub    esp,0xc
 804840f:  push   0x80484b0
 8048414:  call   80482d0 <puts@plt>
 8048419:  add    esp,0x10
 804841c:  mov    eax,0x0
 8048421:  mov    ecx,DWORD PTR [ebp-0x4]
 8048424:  leave  
 8048425:  lea    esp,[ecx-0x4]
 8048428:  ret    
```

- Đoạn đầu chuẩn bị stack frame, không quá quan trọng bây giờ.

- `push 0x80484b0` → đẩy địa chỉ của chuỗi `"hello world!"`.

- `call puts` → gọi hàm `puts()` để in chuỗi ra màn hình.

- Các lệnh sau dọn dẹp stack và thoát chương trình.

Kết quả khi chạy:

```bash
$ ./hello_world
hello world!
```

## 2. If Then

Chuyển sang Assembly bằng `objdump`

`objdump -D if_then -M intel | less`

Hàm `main`:

```asm
080483fb <main>:
 80483fb:       8d 4c 24 04             lea    ecx,[esp+0x4]
 80483ff:       83 e4 f0                and    esp,0xfffffff0
 8048402:       ff 71 fc                push   DWORD PTR [ecx-0x4]
 8048405:       55                      push   ebp
 8048406:       89 e5                   mov    ebp,esp
 8048408:       51                      push   ecx
 8048409:       83 ec 14                sub    esp,0x14
 804840c:       c7 45 f4 0a 00 00 00    mov    DWORD PTR [ebp-0xc],0xa
 8048413:       83 7d f4 0a             cmp    DWORD PTR [ebp-0xc],0xa
 8048417:       75 10                   jne    8048429 <main+0x2e>
 8048419:       83 ec 0c                sub    esp,0xc
 804841c:       68 c0 84 04 08          push   0x80484c0
 8048421:       e8 aa fe ff ff          call   80482d0 <puts@plt>
 8048426:       83 c4 10                add    esp,0x10
 8048429:       b8 00 00 00 00          mov    eax,0x0
 804842e:       8b 4d fc                mov    ecx,DWORD PTR [ebp-0x4]
 8048431:       c9                      leave  
 8048432:       8d 61 fc                lea    esp,[ecx-0x4]
 8048435:       c3                      ret    
 8048436:       66 90                   xchg   ax,ax
 8048438:       66 90                   xchg   ax,ax
 804843a:       66 90                   xchg   ax,ax
 804843c:       66 90                   xchg   ax,ax
 804843e:       66 90                   xchg   ax,ax
```

Trong đó ta thấy

```asm
mov DWORD PTR [ebp-0xc],0xa       ; gán x = 10 (0xa = 10)
cmp DWORD PTR [ebp-0xc],0xa       ; so sánh x == 10?
jne 8048429 <main+0x2e>           ; nếu KHÁC 10 thì nhảy qua puts
push 0x80484c0                    ; đẩy địa chỉ chuỗi "x = ten"
call puts                         ; in chuỗi
```

Kết quả

```bash
$ ./if_then
x = ten
```

## 3. Loop

Quan sát asm

```asm
080483fb <main>:
 80483fb:       8d 4c 24 04             lea    ecx,[esp+0x4]
 80483ff:       83 e4 f0                and    esp,0xfffffff0
 8048402:       ff 71 fc                push   DWORD PTR [ecx-0x4]
 8048405:       55                      push   ebp
 8048406:       89 e5                   mov    ebp,esp
 8048408:       51                      push   ecx
 8048409:       83 ec 14                sub    esp,0x14
 804840c:       c7 45 f4 00 00 00 00    mov    DWORD PTR [ebp-0xc],0x0
 8048413:       eb 17                   jmp    804842c <main+0x31>
 8048415:       83 ec 08                sub    esp,0x8
 8048418:       ff 75 f4                push   DWORD PTR [ebp-0xc]
 804841b:       68 c0 84 04 08          push   0x80484c0
 8048420:       e8 ab fe ff ff          call   80482d0 <printf@plt>
 8048425:       83 c4 10                add    esp,0x10
 8048428:       83 45 f4 01             add    DWORD PTR [ebp-0xc],0x1
 804842c:       83 7d f4 13             cmp    DWORD PTR [ebp-0xc],0x13
 8048430:       7e e3                   jle    8048415 <main+0x1a>
 8048432:       b8 00 00 00 00          mov    eax,0x0
 8048437:       8b 4d fc                mov    ecx,DWORD PTR [ebp-0x4]
 804843a:       c9                      leave  
 804843b:       8d 61 fc                lea    esp,[ecx-0x4]
 804843e:       c3                      ret    
 804843f:       90                      nop
```

Phân tích 

```asm
mov DWORD PTR [ebp-0xc], 0       ; i = 0
jmp 804842c                      ; nhảy đến phần kiểm tra điều kiện
...
cmp DWORD PTR [ebp-0xc], 0x13    ; i <= 19 ?
jle 8048415                      ; nếu ĐÚNG thì in i
...
push [ebp-0xc]                   ; đẩy giá trị i
push 0x80484c0                   ; đẩy địa chỉ định dạng "%d"
call printf
add [ebp-0xc], 0x1               ; i++
jmp kiểm tra điều kiện lại
```

0x13 = 19 → chương trình chạy vòng lặp từ 0 đến 19.

Mỗi vòng lặp:
- In giá trị i.
- Tăng i.
- Quay lại kiểm tra i <= 19.

Tương đương đoạn mã C

```cpp
int i = 0;
for (i = 0; i <= 19; i++) {
    printf("%d", i);
}
```





