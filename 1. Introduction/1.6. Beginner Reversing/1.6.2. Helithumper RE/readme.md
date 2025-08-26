# helithumper re

## 🔍 Bước 1: Chạy thử chương trình

```bash
$ chmod +x rev

$ ./rev
Welcome to the Salty Spitoon™, How tough are ya?
a
Yeah right. Back to Weenie Hut Jr™ with ya
```

👉 Ta thấy chương trình in ra lời thoại, nhận input từ người dùng, và kiểm tra. 

Nếu sai → in thông báo `Back to Weenie Hut Jr™...`. 

Nếu đúng → in `Right this way...`.

## 🔍 Bước 2: Kiểm tra loại file

```bash
$    file rev
rev: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=e4dbcb1281821db359d566c68fea7380aeb27378, for GNU/Linux 3.2.0, not stripped
```

-> `ELF 64-bit`, little endian.

-> `not stripped` → còn nhiều thông tin symbol, dễ phân tích.

## 🔍 Bước 3: Xem hàm main trong Ghidra

```bash
ulong main(void)
{
  int check;
  void *ptr;
 
  ptr = calloc(0x32,1);
  puts("Welcome to the Salty Spitoon™, How tough are ya?");
  __isoc99_scanf(&DAT_0010203b,ptr);
  check = validate(ptr);
  if (check == 0) {
    puts("Yeah right. Back to Weenie Hut Jr™ with ya");
  }
  else {
    puts("Right this way...");
  }
  return (ulong)(check == 0);
}
```

👉 Giải thích:

Cấp phát bộ nhớ `ptr` (50 bytes).

Đọc input từ user vào `ptr`.

Gọi hàm validate(`ptr`) để kiểm tra.

Nếu validate trả về 0 → sai, nếu 1 → đúng.

## 🔍 Bước 4: Phân tích hàm `validate`

```bash

undefined8 validate(char *param_1)

{
  size_t sVar1;
  undefined8 uVar2;
  long in_FS_OFFSET;
  int local_50;
  int local_48 [14];
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  local_48[0] = 0x66;
  local_48[1] = 0x6c;
  local_48[2] = 0x61;
  local_48[3] = 0x67;
  local_48[4] = 0x7b;
  local_48[5] = 0x48;
  local_48[6] = 0x75;
  local_48[7] = 0x43;
  local_48[8] = 0x66;
  local_48[9] = 0x5f;
  local_48[10] = 0x6c;
  local_48[0xb] = 0x41;
  local_48[0xc] = 0x62;
  local_48[0xd] = 0x7d;
  sVar1 = strlen(param_1);
  local_50 = 0;
  do {
    if ((int)sVar1 <= local_50) {
      uVar2 = 1;
LAB_001012b7:
      if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
        __stack_chk_fail();
      }
      return uVar2;
    }
    if ((int)param_1[local_50] != local_48[local_50]) {
      uVar2 = 0;
      goto LAB_001012b7;
    }
    local_50 = local_50 + 1;
  } while( true );
}
```

👉 Giải thích:

Hàm duyệt từng ký tự trong input.

So sánh với mảng `local_48`.

Nếu có ký tự sai → trả về 0.

Nếu duyệt hết mà đúng → trả về 1.

## 🔍 Bước 5: Chuyển số hex → ASCII bằng Python

```bash
>>> x = [0x66,0x6c,0x61,0x67,0x7b,0x48,0x75,0x43,0x66,0x5f,0x6c,0x41,0x62,0x7d]
>>> ''.join(chr(i) for i in x)
'flag{HuCf_lAb}'
```

## ✅ Chạy thử với flag

```bash
└─$ ./rev   
Welcome to the Salty Spitoon™, How tough are ya?
flag{HuCf_lAb}
Right this way...
```














