# Giới thiệu: GDB và GEF

✳️ GDB là gì?
GDB (GNU Debugger) là trình gỡ lỗi dùng để:

- Phân tích quá trình thực thi của một chương trình.

- Quan sát và thay đổi nội dung của bộ nhớ, thanh ghi.

- Thiết lập breakpoint để dừng chương trình tại một điểm cụ thể.

- Thực hiện từng bước (step-by-step) để xem chương trình làm gì tại từng lệnh.

✳️ GEF là gì?
GEF (GDB Enhanced Features) là một plugin mở rộng cho GDB, giúp:

- Hiển thị thông tin một cách rõ ràng, màu sắc.

- Có nhiều lệnh tắt hữu ích cho RE, exploit, debug.

- Tự động hiển thị stack, code, register khi chạy.


Cài đặt GEF: `curl -fsSL https://gef.blah.cat/sh | sh`

Hoặc theo hướng dẫn tại: `https://github.com/hugsy/gef`

## ▶️ Chạy chương trình với GDB

Giả sử bạn có một file tên `hello_world`, chạy như sau: 

`gdb ./hello_world`

Khi vào GDB gõ `r` hoặc `run` để chạy chương trình

## ⛔️ Breakpoints – Dừng chương trình tại một điểm

Một điểm mạnh của GDB là có thể đặt breakpoint tại bất kỳ lệnh nào.

🔹 Ví dụ:
Dừng tại hàm main:

`break main`

Sau đó chạy chương trình `r`

Chương trình sẽ dừng tại lệnh đầu tiên của hàm main, cho bạn quan sát các thanh ghi, stack, code, v.v như hình trong GEF hiển thị.

```bash
gef➤  break main
Breakpoint 1 at 0x8048409
gef➤  r
Starting program: /home/devey/nightmare/modules/02-intro_tooling/hello_world 
[ Legend: Modified register | Code | Heap | Stack | String ]
────────────────────────────────────────────────────────────────────── registers ────
$eax   : 0xf7fb9dd8  →  0xffffd19c  →  0xffffd389  →  "CLUTTER_IM_MODULE=xim"
$ebx   : 0x0       
$ecx   : 0xffffd100  →  0x00000001
$edx   : 0xffffd124  →  0x00000000
$esp   : 0xffffd0e4  →  0xffffd100  →  0x00000001
$ebp   : 0xffffd0e8  →  0x00000000
$esi   : 0xf7fb8000  →  0x001d4d6c
$edi   : 0x0       
$eip   : 0x08048409  →  <main+14> sub esp, 0x4
$eflags: [zero carry PARITY adjust SIGN trap INTERRUPT direction overflow resume virtualx86 identification]
$cs: 0x0023 $ss: 0x002b $ds: 0x002b $es: 0x002b $fs: 0x0000 $gs: 0x0063 
────────────────────────────────────────────────────────────────────────── stack ────
0xffffd0e4│+0x0000: 0xffffd100  →  0x00000001	 ← $esp
0xffffd0e8│+0x0004: 0x00000000	 ← $ebp
0xffffd0ec│+0x0008: 0xf7dfbe81  →  <__libc_start_main+241> add esp, 0x10
0xffffd0f0│+0x000c: 0xf7fb8000  →  0x001d4d6c
0xffffd0f4│+0x0010: 0xf7fb8000  →  0x001d4d6c
0xffffd0f8│+0x0014: 0x00000000
0xffffd0fc│+0x0018: 0xf7dfbe81  →  <__libc_start_main+241> add esp, 0x10
0xffffd100│+0x001c: 0x00000001
──────────────────────────────────────────────────────────────────── code:x86:32 ────
    0x8048405 <main+10>        push   ebp
    0x8048406 <main+11>        mov    ebp, esp
    0x8048408 <main+13>        push   ecx
 →  0x8048409 <main+14>        sub    esp, 0x4
    0x804840c <main+17>        sub    esp, 0xc
    0x804840f <main+20>        push   0x80484b0
    0x8048414 <main+25>        call   0x80482d0 <puts@plt>
    0x8048419 <main+30>        add    esp, 0x10
    0x804841c <main+33>        mov    eax, 0x0
──────────────────────────────────────────────────────────────────────── threads ────
[#0] Id 1, Name: "hello_world", stopped 0x8048409 in main (), reason: BREAKPOINT
────────────────────────────────────────────────────────────────────────── trace ────
[#0] 0x8048409 → main()
─────────────────────────────────────────────────────────────────────────────────────

Breakpoint 1, 0x08048409 in main ()
```

## 🐾 Điều khiển chương trình

| Lệnh | 	Mô tả |
|------|-------|
next   |	Chạy 1 dòng lệnh C (bỏ qua hàm con như `puts`)
step   |	Chạy 1 dòng lệnh C, bước vào hàm con
nexti  |	Chạy 1 instruction (asm), bỏ qua hàm con
stepi  |	Chạy 1 instruction, bước vào hàm con

Dùng các lệnh này để đi qua từng bước của chương trình.

## 📜 Disassemble – Xem mã máy

Xem lệnh Assembly của hàm main:

```bash
gef➤  disass main
Dump of assembler code for function main:
   0x080483fb <+0>:	lea    ecx,[esp+0x4]
   0x080483ff <+4>:	and    esp,0xfffffff0
   0x08048402 <+7>:	push   DWORD PTR [ecx-0x4]
   0x08048405 <+10>:	push   ebp
   0x08048406 <+11>:	mov    ebp,esp
   0x08048408 <+13>:	push   ecx
   0x08048409 <+14>:	sub    esp,0x4
   0x0804840c <+17>:	sub    esp,0xc
   0x0804840f <+20>:	push   0x80484b0
   0x08048414 <+25>:	call   0x80482d0 <puts@plt>
   0x08048419 <+30>:	add    esp,0x10
   0x0804841c <+33>:	mov    eax,0x0
   0x08048421 <+38>:	mov    ecx,DWORD PTR [ebp-0x4]
   0x08048424 <+41>:	leave  
   0x08048425 <+42>:	lea    esp,[ecx-0x4]
   0x08048428 <+45>:	ret    
End of assembler dump.
```

## ⛳ Break tại địa chỉ cụ thể

Bạn có thể dừng tại một địa chỉ cụ thể:

```bash
gef➤  b *main+25
Breakpoint 1 at 0x8048414
```

hoặc 

```bash
gef➤  b *0x08048414
Note: breakpoint 1 also set at pc 0x08048414
Breakpoint 2 at 0x08048414
```

show all breakpoints:

```bash
gef➤  info breakpoints
Num     Type           Disp Enb Address    What
1       breakpoint     keep y   0x08048414 <main+25>
    breakpoint already hit 1 time
2       breakpoint     keep y   0x08048414 <main+25>
```

To delete a breakpoint Num 2:

`gef➤  delete 2`

cũng có thể thiết lập điểm dừng cho các hàm như `puts`:

```bash
gef➤  b *puts
Breakpoint 1 at 0x80482d0
gef➤  r
Starting program: /home/devey/nightmare/modules/02-intro_tooling/hello_world 
[ Legend: Modified register | Code | Heap | Stack | String ]
────────────────────────────────────────────────────────────────────── registers ────
$eax   : 0xf7fb9dd8  →  0xffffd19c  →  0xffffd389  →  "CLUTTER_IM_MODULE=xim"
$ebx   : 0x0       
$ecx   : 0xffffd100  →  0x00000001
$edx   : 0xffffd124  →  0x00000000
$esp   : 0xffffd0cc  →  0x08048419  →  <main+30> add esp, 0x10
$ebp   : 0xffffd0e8  →  0x00000000
$esi   : 0xf7fb8000  →  0x001d4d6c
$edi   : 0x0       
$eip   : 0xf7e4a360  →  <puts+0> push ebp
$eflags: [zero carry parity ADJUST SIGN trap INTERRUPT direction overflow resume virtualx86 identification]
$cs: 0x0023 $ss: 0x002b $ds: 0x002b $es: 0x002b $fs: 0x0000 $gs: 0x0063 
────────────────────────────────────────────────────────────────────────── stack ────
0xffffd0cc│+0x0000: 0x08048419  →  <main+30> add esp, 0x10	 ← $esp
0xffffd0d0│+0x0004: 0x080484b0  →  "hello world!"
0xffffd0d4│+0x0008: 0xffffd194  →  0xffffd34e  →  "/home/devey/nightmare/modules/02-intro_tooling/hel[...]"
0xffffd0d8│+0x000c: 0xffffd19c  →  0xffffd389  →  "CLUTTER_IM_MODULE=xim"
0xffffd0dc│+0x0010: 0x08048451  →  <__libc_csu_init+33> lea eax, [ebx-0xf8]
0xffffd0e0│+0x0014: 0xf7fe59b0  →   push ebp
0xffffd0e4│+0x0018: 0xffffd100  →  0x00000001
0xffffd0e8│+0x001c: 0x00000000	 ← $ebp
──────────────────────────────────────────────────────────────────── code:x86:32 ────
   0xf7e4a356 <popen+134>      call   0xf7dfb608 <free@plt>
   0xf7e4a35b <popen+139>      add    esp, 0x10
   0xf7e4a35e <popen+142>      jmp    0xf7e4a333 <popen+99>
 → 0xf7e4a360 <puts+0>         push   ebp
   0xf7e4a361 <puts+1>         mov    ebp, esp
   0xf7e4a363 <puts+3>         push   edi
   0xf7e4a364 <puts+4>         push   esi
   0xf7e4a365 <puts+5>         push   ebx
   0xf7e4a366 <puts+6>         call   0xf7f17c89
──────────────────────────────────────────────────────────────────────── threads ────
[#0] Id 1, Name: "hello_world", stopped 0xf7e4a360 in puts (), reason: BREAKPOINT
────────────────────────────────────────────────────────────────────────── trace ────
[#0] 0xf7e4a360 → puts()
[#1] 0x8048419 → main()
─────────────────────────────────────────────────────────────────────────────────────

Breakpoint 1, 0xf7e4a360 in puts () from /lib32/libc.so.6
```

## 🔍 Xem nội dung vùng nhớ

Bạn có thể in nội dung tại một địa chỉ:

```bash
x/s 0x80484b0
```

hiển thị:

```bash
0x80484b0: "hello world!"
```

Lệnh |	Mô tả
|----|-------|
x/s  |	Hiển thị string
x/10c|	Hiển thị 10 ký tự
x/a  |	Hiển thị địa chỉ (address)
x/w  |	Hiển thị 4 byte (word)
x/g  |	Hiển thị 8 byte (qword)

## 📥 Thay đổi nội dung vùng nhớ

Ví dụ: muốn đổi “hello world” thành “hello venus”:

```bash
gef➤  set {char [12]} 0x080484b0 = "hello venus"
gef➤  x/s 0x080484b0
0x80484b0:	"hello venus"
gef➤  nexti
hello venus
[ Legend: Modified register | Code | Heap | Stack | String ]
────────────────────────────────────────────────────────────────────── registers ────
$eax   : 0xc       
$ebx   : 0x0       
$ecx   : 0x0804a160  →  "hello venus\n"
$edx   : 0xf7fb9890  →  0x00000000
$esp   : 0xffffd0d0  →  0x080484b0  →  "hello venus"
$ebp   : 0xffffd0e8  →  0x00000000
$esi   : 0xf7fb8000  →  0x001d4d6c
$edi   : 0x0       
$eip   : 0x08048419  →  <main+30> add esp, 0x10
$eflags: [ZERO carry PARITY adjust sign trap INTERRUPT direction overflow resume virtualx86 identification]
$cs: 0x0023 $ss: 0x002b $ds: 0x002b $es: 0x002b $fs: 0x0000 $gs: 0x0063 
────────────────────────────────────────────────────────────────────────── stack ────
0xffffd0d0│+0x0000: 0x080484b0  →  "hello venus"	 ← $esp
0xffffd0d4│+0x0004: 0xffffd194  →  0xffffd34e  →  "/home/devey/nightmare/modules/02-intro_tooling/hel[...]"
0xffffd0d8│+0x0008: 0xffffd19c  →  0xffffd389  →  "CLUTTER_IM_MODULE=xim"
0xffffd0dc│+0x000c: 0x08048451  →  <__libc_csu_init+33> lea eax, [ebx-0xf8]
0xffffd0e0│+0x0010: 0xf7fe59b0  →   push ebp
0xffffd0e4│+0x0014: 0xffffd100  →  0x00000001
0xffffd0e8│+0x0018: 0x00000000	 ← $ebp
0xffffd0ec│+0x001c: 0xf7dfbe81  →  <__libc_start_main+241> add esp, 0x10
──────────────────────────────────────────────────────────────────── code:x86:32 ────
    0x804840c <main+17>        sub    esp, 0xc
    0x804840f <main+20>        push   0x80484b0
    0x8048414 <main+25>        call   0x80482d0 <puts@plt>
 →  0x8048419 <main+30>        add    esp, 0x10
    0x804841c <main+33>        mov    eax, 0x0
    0x8048421 <main+38>        mov    ecx, DWORD PTR [ebp-0x4]
    0x8048424 <main+41>        leave  
    0x8048425 <main+42>        lea    esp, [ecx-0x4]
    0x8048428 <main+45>        ret    
──────────────────────────────────────────────────────────────────────── threads ────
[#0] Id 1, Name: "hello_world", stopped 0x8048419 in main (), reason: SINGLE STEP
────────────────────────────────────────────────────────────────────────── trace ────
[#0] 0x8048419 → main()
─────────────────────────────────────────────────────────────────────────────────────
0x08048419 in main ()
```

Giả sử bạn muốn đổi giá trị tại `0x08048451` thành `0xfacade`:

```bash
gef➤  x/g 0x08048451
0x8048451 <__libc_csu_init+33>:	0xff08838d
gef➤  set *0x08048451 = 0xfacade
gef➤  x/g 0x08048451
0x8048451 <__libc_csu_init+33>:	0xfacade
```

## 🧭 Nhảy tới một địa chỉ (jump)
GDB cho phép bạn nhảy tới một địa chỉ và bỏ qua đoạn code khác:

```bash
gef➤  j *0x08048451
Continuing at 0x0x08048451.
```

## 🧠 Xem thanh ghi, stack, frame

```
info registers    # Xem tất cả thanh ghi
info frame        # Xem stack frame hiện tại
```

ví dụ

```bash
gef➤  info registers
eax            0xf7fb9dd8	0xf7fb9dd8
ecx            0xffffd100	0xffffd100
edx            0xffffd124	0xffffd124
ebx            0x0	0x0
esp            0xffffd0d0	0xffffd0d0
ebp            0xffffd0e8	0xffffd0e8
esi            0xf7fb8000	0xf7fb8000
edi            0x0	0x0
eip            0x8048414	0x8048414 <main+25>
eflags         0x296	[ PF AF SF IF ]
cs             0x23	0x23
ss             0x2b	0x2b
ds             0x2b	0x2b
es             0x2b	0x2b
fs             0x0	0x0
gs             0x63	0x63
```

hay 

```bash
gef➤  info frame
Stack level 0, frame at 0xffffd100:
 eip = 0x8048414 in main; saved eip = 0xf7dfbe81
 Arglist at 0xffffd0e8, args: 
 Locals at 0xffffd0e8, Previous frame's sp is 0xffffd100
 Saved registers:
  ebp at 0xffffd0e8, eip at 0xffffd0fc
```








