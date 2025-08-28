# 🟢 CSAW 2019 – Challenge beleaf

👉 Mục tiêu: Tìm ra flag.

## 1. Kiểm tra file ban đầu

```bash
─$ file beleaf 
beleaf: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=6d305eed7c9bebbaa60b67403a6c6f2b36de3ca4, stripped
```

Kết quả cho thấy đây là một binary 64-bit ELF.

`stripped` cho thấy các chuỗi đã bị làm nhiễu


```bash
$ pwn checksec beleaf
Arch:     amd64-64-little
RELRO:    Full RELRO
Stack:    Canary found
NX:       NX enabled
PIE:      PIE enabled
```

Binary này có đủ cơ chế bảo vệ:

- `Canary` (chống tràn bộ nhớ stack)

- `NX` (không cho thực thi trên stack)

- `PIE` (địa chỉ bộ nhớ random).

Khi chạy thử:

```bash
$ ./beleaf
Enter the flag
>>> 15935728
Incorrect!
```

→ Chương trình yêu cầu nhập flag, nếu sai thì báo `Incorrect!` → Đây chính là một dạng crackme (bạn phải đoán input đúng để qua kiểm tra).

## 2. Reversing

Đưa vào Ghidra 

```c
undefined8 main(void)
{
  size_t inputLen;
  long transformedInput;
  char input[136];

  printf("Enter the flag\n>>> ");
  scanf("%s", input);

  inputLen = strlen(input);

  if (inputLen < 0x21) {   // phải >= 33 ký tự
    puts("Incorrect!");
    exit(1);
  }

  for (i = 0; i < inputLen; i++) {
    transformedInput = transformFunc(input[i]);

    if (transformedInput != desiredOutput[i]) {
      puts("Incorrect!");
      exit(1);
    }
  }

  puts("Correct!");
  return 0;
}
```

📌 Ý nghĩa:

- Flag phải có ít nhất 33 ký tự.

- Mỗi ký tự được đưa qua hàm `transformFunc()`.

- Sau đó so sánh với mảng desiredOutput. Nếu khác → sai ngay.

## 3. Mảng desiredOutput

```asm
                             desiredOutput                                   XREF[2]:     main:0010096b(*),
                                                                                          main:00100972(R)  
        003014e0 01              ??         01h
        003014e1 00              ??         00h
        003014e2 00              ??         00h
        003014e3 00              ??         00h
        003014e4 00              ??         00h
        003014e5 00              ??         00h
        003014e6 00              ??         00h
        003014e7 00              ??         00h
        003014e8 09              ??         09h
        003014e9 00              ??         00h
        003014ea 00              ??         00h
        003014eb 00              ??         00h
        003014ec 00              ??         00h
        003014ed 00              ??         00h
        003014ee 00              ??         00h
        003014ef 00              ??         00h
        003014f0 11              ??         11h
        003014f1 00              ??         00h
        003014f2 00              ??         00h
        003014f3 00              ??         00h
        003014f4 00              ??         00h
        003014f5 00              ??         00h
        003014f6 00              ??         00h
        003014f7 00              ??         00h
        003014f8 27              ??         27h    '
        003014f9 00              ??         00h
        003014fa 00              ??         00h
        003014fb 00              ??         00h
        003014fc 00              ??         00h
        003014fd 00              ??         00h
        003014fe 00              ??         00h
        003014ff 00              ??         00h
        00301500 02              ??         02h
```
Vậy ở đây chúng ta thấy rằng đầu ra đầu tiên phải bằng 0x1, đầu ra thứ hai phải bằng 0x9, đầu ra thứ ba phải bằng 0x11, v.v. (cách 8 bytes)

##  4. Hàm transformFunc

```c
long transformFunc(char input)
{
  long i = 0;

  while (i != -1 && input != lookup[i]) {
    if (input < lookup[i])
      i = i * 2 + 1;
    else
      i = (i + 1) * 2;
  }
  return i;
}
```

👉 Đây là một binary search tree (cây nhị phân) được lưu trong mảng lookup.

Hàm nhận 1 ký tự.

Đi tìm vị trí của ký tự đó trong lookup.

Trả về chỉ số (index).

## 5. Mảng lookup

```asm
00301020 77    w
00301024 66    f
00301028 7b    {
0030102c 5f    _
00301030 6e    n
00301034 79    y
00301038 7d    }
...
00301040 62    b
00301044 6c    l
00301048 72    r
...
00301064 61    a
00301068 65    e
0030106c 69    i
```

Ở đây chúng ta có thể thấy ký tự f được lưu trữ tại `00301024`. Kết quả sẽ là 1 vì ((`0x00301024` - `0x00301020`) / 4) = 1 (`0x00301020` là số bắt đầu của mảng). Điều này cũng tương ứng với byte đầu tiên của mảng desiredOutput, vì nó là 1. Byte thứ hai là `0x9`, do đó ký tự tương ứng với nó là (0x00301020 + (4*9)) = `0x301044` và chúng ta có thể thấy ký tự ở đó là l.

📌 Ví dụ:

lookup[1] = f → transformFunc('f') = 1.

lookup[9] = l → transformFunc('l') = 9.

Điều này trùng khớp với các giá trị trong desiredOutput.

Ghép flag: flag{we_beleaf_in_your_re_future}







