# 🏴‍☠️ CSAW 2018 – Tour of x86 (Phần 1)

## 🎯 Mục tiêu

Challenge này yêu cầu trả lời một số câu hỏi dựa trên file stage1.asm (một file text chứa code Assembly x86).

### Câu 1 – Giá trị của dh sau dòng 129

Dòng 29 

` xor dh, dh  ; <- Question 1`

xor với chính mình -> 0

Vậy dh = 0x0

### Câu 2 – Giá trị của gs sau dòng 145

Dòng 145 

`mov gs, dx ; to use them to help me clear     <- Question 2`

Đầu tiên, `mov` là lệnh lấy giá trị từ `dx` gán cho `gs`

Vậy cần tìm giá trị `dx`

Trước đó (dòng 131–132):

```asm
mov dx, 0xffff
not dx
```

Gán `dx` = 0xffff

`not` (phủ định) `dx` suy ra `dx` bây giờ = `0x0000`

Vậy `gs` = `0x0000`

### Câu 3 – Giá trị của si sau dòng 151

Dòng 151

`mov si, sp ; Source Index       <- Question 3`

Gán `sp` cho `si`

-> Tìm `sp`

Dòng 149 

`mov sp, cx ; Stack Pointer`

Gán `cx` cho `sp`

-> Tìm `cx`

Tìm ngược lên dòng 107 

`mov cx, 0 ;`

Vậy `si` = `sp` = `cx` = 0

### Câu 4 – Giá trị của ax sau dòng 169

```asm
mov al, 't'    ; 't' = 0x74
mov ah, 0x0e   ; <- Question 4
```

Đầu tiên ta cần biết, thanh ghi `ax` (16 bit) bao gồm `al`(8 bit thấp) và `ah` (8 bit cao)

Ở đây:

- `al` = `0x74` (`t`)

- `ah` = `0x0e`

Ghép lại: `ax` = `0x0e74`.

✅ Đáp án: `0x0e74`

### Câu 5 – Giá trị của ax sau dòng 199 (chạy lần đầu tiên)

```asm
mov al, [si]   ; al = giá trị tại địa chỉ si
mov ah, 0x0e   ; <- Question 5
```

Vậy ta đã có `ah` là `0x0e`

Ta cần `al` 

Ở đây `al` lấy giá trị tại địa chỉ `si`

Cần tìm `si`

Nhìn lên ta có 

`mov si, ax`

Mà `ax` lúc đó chứa địa chỉ của chuỗi:

`mov ax, .string_to_print`

Chuỗi được định nghĩa:

`.string_to_print: db "acOS", 0x0a, 0x0d, "  by Elyk", 0x00`

```asm
; Now let's make a whole 'function' that prints a string
print_string:
  .init:
    mov si, ax  ; We have no syntactic way of passing parameters, so I'm just going to pass the first argument of a function through ax - the string to print.

  .print_char_loop:
    cmp byte [si], 0  ; The brackets around an expression is interpreted as "the address of" whatever that expression is.. It's exactly the same as the dereference operator in C-like languages
                        ; So in this case, si is a pointer (which is a copy of the pointer from ax (line 183), which is the first "argument" to this "function", which is the pointer to the string we are trying to print)
                        ; If we are currently pointing at a null-byte, we have the end of the string... Using null-terminated strings (the zero at the end of the string definition at line 178)
    je .end
   
    mov al, [si]  ; Since this is treated as a dereference of si, we are getting the BYTE AT si... `al = *si`

    mov ah, 0x0e  ; <- Question 5!
    int 0x10      ; Actually print the character
 
    inc si        ; Increment the pointer, get to the next character
    jmp .print_char_loop
    .end:
```

Ký tự đầu tiên: "a" = 0x61.

Vậy lần đầu chạy:

- `al` = `0x61`

- `ah` = `0x0e`

Ghép lại: `ax` = 0x0e61.

✅ Đáp án: 0x0e61





