---
title: "函式呼叫(I) - 參數暫存"
date: 2020-04-21T11:30:51+08:00
draft: false
tags: ["CTF", "pwn", "reverse"]
categories: ["CTF pwn"]
---

# 函式呼叫(I) - 參數暫存

## 前言
這學期修了計算機組織的課程，了解到 MIPS 呼叫函式所暫存的參數方式與 x86、x64 有一些不同，在了解逆向分析之函數分析的流程之前，必須先了解呼叫前後的參數所在。
透過本篇文章可以了解到，一個函式的呼叫，在**不同的指令集有很大的差別**，並透過函數的呼叫，可以了解到**在呼叫函式時傳入了多少引數，哪些引數？**，以及**是在呼叫前暫存引數，還是呼叫後暫存參數？**，在使用逆向分析時會更方便。

### 名詞介紹
* argument (引數)：呼叫函式時，其程式碼括號內的參數稱之。
* parameter (參數)：定義或宣告函式時，其程式碼括號內的參數稱之。
* caller：呼叫函式的函式 (呼叫者)
* callee：被呼叫的函式 (被呼叫者)
```C
#include <stdio.h>

void hello(long int a, long int b){ 
	// a, b are parameters
    /* statement */
}

int main(){
    long int x = 1000, y = 2000;
    
    /* x, y are arguments, 
       and hello is callee, main is caller */
    hello(x, y); 
    
    return 0;
}
```

## MIPS

以 MIPS 來看，進入到 callee 之後，才將 parameter 及 return address push 到 stack 暫存

![](https://i.imgur.com/gKkOjlT.png)


## x86
對 x86 來說，在 caller 呼叫 callee 之前，就會先將 argument push 到 stack 之後再呼叫函數

以 ubuntu 為例，若 OS 環境為64位元，但想要編譯 32 位元的 x86 <br>
則可按照下列方式安裝 gcc 32-bit 可支援的 library
```shell
sudo apt-get install gcc-multilib
```
編譯與執行
```shell
gcc -m32 -o hello.o hello.c
./hello.o
```
使用 objdump 反組譯分析組合語言 (-d為disassemble, -M為disassembler-options)
```shell
objdump -d -M intel ./hello.o
```
在 main function 要呼叫 puts 之前，<br>
x86 會先將處理好參數內容保存在暫存器，並在psuh到stack上暫存

此圖為 main function scope 的程式碼
![](https://i.imgur.com/yDOIKLE.png)


在呼叫function之前幾行有幾次push(大部分情況)，就代表有幾個 argument
![](https://i.imgur.com/6CvC33p.png)



## x64
根據 x64 呼叫慣例 (calling convention)，x64 與 x86 不同的是，x86 並不會 push 到 stack 中，而是將所須參數，**各自儲存在暫存器**中 <br>

但在使用暫存參數的暫存器，會分為兩種：function
 與 system call<br>

* `system call` 暫存參數所使用的暫存器如下
```
rdi：第一個參數所存放的暫存器
rsi：第二個參數所存放的暫存器
rdx：第三個參數所存放的暫存器
r10：第四個參數所存放的暫存器
r8：第五個參數所存放的暫存器
r9：第六個參數所存放的暫存器
rax：保存 syscall 編號，以便讓系統知道要執行何種 syscall
```

* 一般 function (包含 user-defined function 與 library function )
若超過 6 個 parameter，則將剩下的 parameter `push` 到 stack (reversed order，也就是從最後一個 parameter 開始 psuh)

```
rdi：第一個參數所存放的暫存器
rsi：第二個參數所存放的暫存器
rdx：第三個參數所存放的暫存器
rcx：第四個參數所存放的暫存器
r8：第五個參數所存放的暫存器
r9：第六個參數所存放的暫存器
```


編譯
```shell
gcc -o hello.o hello.c
```
使用 objdump 反組譯分析組合語言
```shell
objdump -d -M intel ./hello.o
```

![](https://i.imgur.com/UYWKrKa.png)

![](https://i.imgur.com/pT62R86.png)



## Reference

* [x64 呼叫慣例](https://docs.microsoft.com/zh-tw/cpp/build/x64-calling-convention?view=vs-2019)
* [Sample 64-bit nasm programs](https://www.csee.umbc.edu/portal/help/nasm/sample_64.shtml)
* [x86-64 calling convention](https://aaronbloomfield.github.io/pdr/book/x86-64bit-ccc-chapter.pdf)