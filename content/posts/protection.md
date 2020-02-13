---
title: ELF 五種保護機制
date: 2020-02-13T00:10:46+08:00
draft: true
---

# ELF 五種保護機制

* Stack Canary
    * Stack Protector
    * 防bof漏洞攻擊
    * 若有開啟Canary，則程式執行時會在return address前放一個隨機值叫Canary
    * 如果利用bof等方式去竄改return address，這個隨機值就會被改
    * return之後會檢查Canary，若發現Canary被竄改則會直接停止執行
* NX (No-Execution) / DEP (Data Execution Prevention)
    * 有些Section有執行權限，有些沒有
    * data segment 通常不該有執行權限
    * 例如stack、heap都只有rw-權限
    * 因此無法透過Bof在stack放置shellcode執行程式碼
* PIE
    * 隨機化code段的base位址
    * 否則固定0x40000000
    * 可以竄改PIE，讓他關掉
* ASLR
    * Library和stack的base位址隨機化
        * Libc base位址是隨機的 (但在用gdb時，base位址會是固定的)
    * 通常都是有開啟的
    * gdb預設會是關閉
    * `ldd elffile`
        * 查看該ELF檔案使用什麼library (.so檔，相當於Windows的dll)
* RELRO (Relocation Read-Only)
    * partial RELRO / disable 可以改寫 GOT
    * full RELRO：在 load 時就先決定好 GOT address 了，所以權限設定成不可寫
