---
title: "Print right triangle and diamond in C."
date: 2017-12-14T15:45:44+08:00
draft: false
tags: ["C", "程式"]
categories: ["Programming"]
---

# 印出直角三角形
由簡單到難的難度分別為以下四種
1. [第一種直角三角形](#1)
2. [第二種直角三角形](#2)
3. [第三種直角三角形](#3)
4. [第四種直角三角形](#4)
5. [菱形(Diamond)](#5)
6. [特殊菱形](#6)

## <a name="1">第一種直角三角形</a>
```
@
@@
@@@
```
#### <方法一>

```C
#include <stdio.h>

int main(void) {
	int i, j;
	int number;
	scanf("%d", &number);
	for(i = 1; i <= number; i++){
		for(j = 1; j <= i; j++){
			printf("@");
		}
		printf("\n");
	}
	return 0;
}
```



## <a name="2">第二種直角三角形</a>
```
@@@
@@
@
```

#### <方法一>
```C
#include <stdio.h>

int main(void) {
	int i, j;
	int number;
	scanf("%d", &number);
	for(i = 1; i <= number; i++){
		for(j = i; j <= number; j++){
			printf("@");
		}
		printf("\n");
	}
	return 0;
}
```

#### <方法二>
```C
#include <stdio.h>

int main(void) {
	int i , j;	//counter
	int number;
	scanf("%d", &number);
	for(i = n; i >= 1; i--){
		for(j = 1; j <= i; j++){
		  printf("@");
		}
		printf("\n");
	}
	return 0;
}
```

#### <方法三> 函數
```C
// 多行成直角三角形
void print_triangle(int row)  // row 是行數
{
    int i;
    for (i=row; i>=1; i--) print_line(i);
}

// 多字成行
void print_line(int L)  // L 是一行的長度
{
    int i;
    for (i=1; i<=L; i++) printf("@");
    printf("\n");
}

int main(void) {
	int number;
	scanf("%d", &number);
	print_triangle(number);
	return 0;
}
// URL: http://www.csie.ntnu.edu.tw/~u91029/AlgorithmDesign.html#1
```

## <a name="3">第三種直角三角形</a>
```
  @
 @@
@@@ 
```
	
#### <方法一>
```C
#include <stdio.h>

int main(void) {
	int i, j;
	int number;
	scanf("%d", &number);
	for(i = 1; i <= number; i++){
		for(j = number; j >= 1; j--){
			if(j > i) printf(" ");
			else printf("@");
		}
		printf("\n");
	}
	return 0;
}
```

## <a name="4">第四種直角三角形</a>
```
@@@
 @@
  @
```

```C
#include <stdio.h>

int main(void) {
	int i, j;
	int number;
	scanf("%d", &number);
	for(i = 1; i <= number; i++){
		for(j = 1; j <= number; j++){
			if(j >= i) printf("@");
			else printf(" ");
		}
		printf("\n");
	}
	return 0;
}
```

想法：
如圖

## <a name="5">進階版：菱形(Diamond)</a>
```
  *
 ***
*****
 ***
  *
```

#### <方法一>
```C
#include <stdio.h>

int main(void) {
	int i, j; // counter
	int number; // size
	scanf("%d", &number);
	
	/* the upper half : big isosceles triangle */
	for(i = 1; i <= number; i++){
		for(j = i + 1; j <= number; j++){
			printf(" ");
		}
		for(j = 1; j <= 2 * i - 1; j++){
			printf("*");
		}
		printf("\n");
	}
	
	/* the lower hakf : small isosceles triangle */
	for(i = number -1; i >= 1; i--){
		for(j = number; j > i; j--){
			printf(" ");
		}
		for(j = 1; j <= 2 * i - 1; j++){
			printf("*");
		}
		printf("\n");
	}
	return 0;
}
```

#### <方法二>
```C
#include <stdio.h>

int main(void) {
	int a; // size of diamond
	int b, c, d, e, f;
	scanf("%d", &a);
	f = (a+1)/2;
	for (b = 1; b <= f; b++) {
		for (d = f - b ; d > 0; d--) { printf(" "); }
			for (e = 1; e<= 2 * b - 1; e++) { printf("*"); }
	printf("\n"); }
	for(b = f - 1; b > 0; b--) {
		for(d = f - b; d > 0;d--) { printf(" "); }
			for(e = 1; e <= 2 * b - 1; e++) { printf("*"); }
	printf("\n"); }
}
```

## <a name="6">補充：特殊菱形</a>
```
　　＊　　　
　＊　＊　　
＊　＊　＊
　＊　＊　　
　　＊　
```

```C
#include <stdio.h>

int main(void) {
	int i, j;
	int number;
	scanf("%d", &number);
	
	for(i = 1; i <= number; i++){
		for(j = number; j > i; j--)
			printf("　");
		for(j = 1; j <= 2 * i - 1; j++){
			if(j % 2 == 1) printf("＊");
			else printf("　");
		}
		printf("\n");
	}
	for(i = number - 1; i >= 1; i--){
		for(j = number; j > i; j--)
			printf("　");
		for(j = 1; j <= 2 * i - 1; j++){
			if(j % 2 == 1) printf("＊");
			else printf("　");
		}
		printf("\n");
	}
	return 0;
}
```

**以上CODE皆為自己想的內容，如有更好的方式歡迎告訴我~!**