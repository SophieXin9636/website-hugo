---
title: "ROP Skill - Stack Migration"
date: 2020-05-14T23:53:59+08:00
draft: true
tags: ["CTF", "pwn"]
categories: ["CTF pwn"]
---

# ROP Skill - Stack Migration
**Stack Migration** 是指 ROP (Return Oriented Programming) 的一種技巧，可在當 stack 不夠使用時，利用 ROP 控制並竄改 `stack pointer (esp or rsp)` 來獲取額外的寫入空間。

範例原始碼

```C=
#include <stdio.h>
#include <unistd.h>

char nothing[128] = "";

void meet() {
    char something[32];
    printf("What are you looking for? ");
    read(0, something, 128);
    return;
}

int main() {
    setvbuf(stdout, 0, 2, 0);
    setvbuf(stdin, 0, 2, 0);
    printf("What are you looking for? ");
    fflush(stdout);
    read(0, nothing, 128);
    
    meet();
    return 0;
}
```

看完原始碼後，我們先假設此程式沒有開啟 canary 保護，<br>
也就是說，我們可以使用 bof 來突破 stack 的寫入空間，<br>
並將原先的 retrun address 竄改成 `leave ; ret` 的 gadget 位址，<br>
只要執行到 `ret` 指令時，就會將 program counter (eip, rip) 指向 `leave ; ret` 之位址，<br>
就能夠透過 leave 的 `mov rsp, rbp ; pop rbp` 來竄改原先的 `rsp` <br>


compile & static linking
```
gcc lab.c -fno-stack-protector -no-pie -static -o lab
```

使用 `checksec` 檢查有哪些保護關閉
<img src="../../../img/migration/1.png">
