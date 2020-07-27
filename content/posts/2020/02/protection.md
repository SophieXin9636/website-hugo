---
title: ELF 五種保護機制
date: 2020-02-13T00:10:46+08:00
draft: false
tags: ["CTF", "pwn"]
categories: ["CTF pwn"]
---

# ELF 五種保護機制

* Stack Canary
    * Stack Protector，也就是用來保護 Stack 段
    * 用來防 bof 漏洞攻擊
    * 若有開啟 Canary，則程式執行時會在 return address 前放一個隨機值叫 Canary
    * 如果利用bof等方式去竄改 return address，這個隨機值就會被改
    * return之後會檢查Canary，若發現Canary被竄改則會直接停止執行

* NX (No-Execution) / DEP (Data Execution Prevention)
    * 若該 Section 有 write 權限，則一定沒有 execute 權限
    * 反之 Section 有 execute 權限，則一定沒有 write 權限
    * 例如：stack, heap 都只有 rw- 權限，而 data segment 通常不該有執行權限
    * 因此可防止利用 Bof 漏洞 在 stack 放置 shellcode 執行程式碼

* PIE (Position Independent Code)
    * 隨機化 code 段的 base 位址
    * 否則固定 0x400000
    * 可以竄改PIE，讓他關掉

* ASLR (Address Space Layout Randomization)
    * 載入 Library 的 base 位址隨機化
        * Libc base 位址是隨機的
    * stack 段的 base 位址隨機化
    * 通常都是有開啟的
    * gdb 預設會將 ASLR 保護關閉，因此在用 gdb 工具時，base 位址會是固定的
    * `ldd elffile`
        * 查看該 ELF 檔案引入哪個 library (.so檔，相當於Windows的dll)

* RELRO (Relocation Read-Only)
    * partial RELRO / disable 可以改寫 GOT
    * full RELRO：在 load 時就先決定好 GOT address 了，所以權限設定成不可寫
