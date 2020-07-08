---
title: "Variable Length Arguments"
date: 2020-03-22T16:33:34+08:00
draft: false
tags: ["C", "程式"]
categories: ["Programming"]
---

## Variable Length Arguments
當輸入的引數長度不確定時，我們可以使用 Variable Length Arguments 來實作。 <br>
其來自於 `<stdarg.h>` 標準函式庫中<br>


### 意義
`va_list` 為 variable argument lists <br>
無論輸入的參數個數為何，皆可處理


### 實作原理
```C
typedef char *va_list;
#define va_start(ap, param) (ap = (((va_list)&param) + sizeof(param)))
#define va_end(ap) (void)((ap) = 0)
#define va_arg(ap, type) (*(type *)((ap += sizeof(type)) - sizeof(type)))
```

![](https://i.imgur.com/Mma58fM.png)


### 用法
* 宣告 資料型態為 va_list 的 ap (為 pointer)
```C
va_list ap;
```

Macro 之 prototype
```C
void va_start(va_list ap, last_arg);
type va_arg(va_list ap, type);
void va_end(va_list ap);
void va_copy(va_list dest, va_list src);
```
* va_start：將 ap (pointer)設定成第一個參數的位址
* va_arg：  將 ap 設定成下一個參數之位址
* va_end：  表示走訪結束，ap 設定成 NULL
* va_copy： 複製一份 va_list到 dest


### 例子
```C=
#include <stdarg.h>
#include <stdio.h>

int sum(int, ...);
void print_name(const char *, ...);

int main() {
	int count = 3;
    printf("Sum of 1, 2, 3 = %d\n", sum(count, 1, 2, 3));
    count = 4;
    printf("Sum of 14, 2, 25, 30 = %d\n\n", sum(count, 14, 2, 25, 30));
    print_name("Sophie", "Meg", "Kenny", "Smith", "John");
    return 0;
}

int sum(int multi_nums, ...) {
    int total = 0;
    va_list ap;

    va_start(ap, multi_nums);
    for(int i = 0; i < multi_nums; i++) {
        total += va_arg(ap, int);
    }
    va_end(ap);
    return total;
}

void print_name(const char *firstStr, ...){
    va_list ap;
    va_start(ap, firstStr);
    int num = 0;
    const char* str = firstStr;
    while(str != NULL){
        printf("%d. %s\n", num+1, str);
        str = va_arg(ap, const char *);
        num++;
    }
    va_end(ap);

    printf("Total: %d people\n", num);   
}
```

編譯
```shell
gcc test.c -o0 -o test
```
執行
```shell
./test
```
```
Sum of 1, 2, 3 = 6
Sum of 14, 2, 25, 30 = 71

1. Sophie
2. Meg
3. Kenny
4. Smith
5. John
Total: 5 people
```

## Reference
* [What is the format of the x86_64 va_list structure?](https://stackoverflow.com/questions/4958384/what-is-the-format-of-the-x86-64-va-list-structure/4958507#4958507)
* [How are variable arguments implemented in gcc?
](https://stackoverflow.com/questions/12371450/how-are-variable-arguments-implemented-in-gcc)
* [談談va_list與void *](https://www.ptt.cc/man/C_and_CPP/DB9B/DE78/M.1265892928.A.A3C.html)