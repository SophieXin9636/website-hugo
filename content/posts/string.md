---
title: "String"
date: 2018-01-07T14:45:44+08:00
draft: false
tags: ["C", "程式"]
categories: ["Programming"]
---

字串相關整理

# 字串 (string)
* [strcpy、strcat比較](#1)
* [使用strcpy容易發生幾種狀況](#2)
* [反轉字串 Reverse a string](#3)
* [strcmp介紹、字串排序 (string sort)](#4)

## <a name="1">strcpy、strcat比較</a>
需使用```#include <string.h>```引入標頭檔
* ```strcpy```：string copy
* ```strcat```：string concatenation (字串連接)
```C
#include <stdio.h>
#include <string.h>

int main(void) {
	char source_1[50], destination_1[50]; // use for strcpy
	char source_2[50], destination_2[50]; // use for strcat
	
	scanf("%s%s", source_1, destination_1);
	scanf("%s%s", source_2, destination_2);
	
	printf("Before strcpy:\n");
	printf("* source_1 string is: %s\n", source_1);
	printf("* destination_1 string is: %s\n\n", destination_1);
	
	strcpy(destination_1, source_1);
	
	printf("After strcpy:\n");
	printf("* source_1 string is: %s\n", source_1);
	printf("* destination_1 string is: %s\n\n", destination_1);
	
	
	printf("Before strcat:\n");
	printf("* source_2 string is: %s\n", source_2);
	printf("* destination_2 string is: %s\n\n", destination_2);
	
	strcat(destination_2, source_2);
	
	printf("After strcat:\n");
	printf("* source_2 string is: %s\n", source_2);
	printf("* destination_2 string is: %s\n\n", destination_2);
	return 0;
}
```

##### Output
```
Before strcpy:
* source_1 string is: Test_for_source_1
* destination_1 string is: Test_destination_1

After strcpy:
* source_1 string is: Test_for_source_1
* destination_1 string is: Test_for_source_1

Before strcat:
* source_2 string is: Test_for_source_2
* destination_2 string is: destination_2

After strcat:
* source_2 string is: Test_for_source_2
* destination_2 string is: destination_2Test_for_source_2
```


## <a name="2">使用strcpy容易發生幾種狀況</a>

1. ```char string1[80] = "programming```本身宣告80個位元組(Bytes)大小的字元陣列，使用```strcpy(string1, "more programming");```是沒問題的。 
(除非source字串超過80 Bytes，就會發生如同狀況2)

2. ```char string2[] = "programming```本身就沒有宣告大小，因此在編譯時，編譯器會計算初始化該字串的總大小(包含'\0'字元)為12 Bytes。
在執行strcpy時，會發生**緩衝區覆蓋(buffer overrun)**
  * 緩衝區覆蓋(buffer overrun)：使用```strcpy```及```strcat```時，如果source string超過destination string的長度，將可能會覆蓋到其他變數的內容。

3. ```char *string3 = "programming"```本身是指標，占4 Bytes。
編譯器會在唯讀記憶體中放一個字元陣列，初始化成"programming"，再將```*string3```指向這個字元陣列。
因此一執行strcpy時，系統會宣告執行錯誤。
* 第三種狀況多半放在唯獨記憶中，盡量不要嘗試寫入這個字串 (寫字元進入會執行錯誤)

```C
#include <stdio.h>
#include <string.h> // 引入 string header file

int main(void) {
	char string1[80] = "programming";
	char string2[] = "programming";
	char *string3 = "programming";
	
	// string copy
	strcpy(string1, "more programming"); // 1. success
	strcpy(string2, "more programming"); // 2. buffer overrun 緩衝區覆蓋
	strcpy(string3, "more programming"); // 3. runtime error

	return 0;
}
```

## <a name="3">反轉字串 Reverse a string</a>
#### 方法一
```C
#include <stdio.h>
#include <string.h>

/*  Reverse a string 反轉字串  */

int main(void) {
	int i, length;
	char s[80]; // declare a string
	char temp;  // temporary
	
	scanf("%s", s);
	length = strlen(s);
	printf("The length of 『%s』 is %d\n", s, length);
	printf("The size   of 『%s』 is %d\n", s, sizeof(s));
	
	// Reverse a string 反轉字串
	for(i = 0; i < length / 2; i++){
		temp = s[i];
		s[i] = s[length - 1 - i];
		s[length - 1 - i] = temp; 
	}
	
	printf("The reversed string is : %s\n", s);
}
```

#### 方法二
```C++
#include <iostream>
#include <cstring>
using namespace std;

void reverse_string(char *a);

int main() {
	char s[] = "ABCDEFGH";
	reverse_string(s);
	cout << s;
	return 0;
}

void reverse_string(char *a){ // 注意此行
	int l = strlen(a);
	for(int i=0, j=l-1; i<int(l/2); i++, j--){
		char temp = a[i];
		a[i] = a[j];
		a[j] = temp;
	}
}
```

## <a name="4">strcmp介紹、字串排序 (string sort)</a>
* ```strcmp``` : string compare，同樣也在```<string.h>```標頭檔裡。```strcmp```是用來比較兩個字串的大小，並會回傳整數值。
* ```strcmp```的比較方式：兩字串的第一個字元開始，以ASCII碼大小比較，如果左字串大於右字串則回傳正數，反之回傳負數，一樣大(代表兩者字串相同)則回傳 0。

#### <方法一> 使用strcpy作swap
```C
#include <stdio.h>
#include <string.h>

/* string sort : use bubble sort */

int main(void) {
	char zodiac[12][20];
	int i, j; // counter
	char temp[20]; // temporary
	
	for(i = 0; i < 12; i++)
		scanf("%s", zodiac[i]);
		
	for(i = 11; i >= 0; i--){
		for(j = 0; j < i; j++){
			if(strcmp(zodiac[j], zodiac[j + 1]) > 0){
				strcpy(temp, zodiac[j]);
				strcpy(zodiac[j], zodiac[j + 1]);
				strcpy(zodiac[j + 1], temp);
			}
		}
	}
	
	for(i = 0; i < 12; i++)
		printf("%s\n", zodiac[i]);
	return 0;
}
```

#### <方法二> 使用指標陣列作swap
```C
#include <stdio.h>
#include <string.h>
 
/* string sort : use bubble sort */
 
int main(void) {
	char zodiac[12][20];
	char *zptr[12];
	int i, j; // counter
	char *temp; // temporary
 
	for(i = 0; i < 12; i++){
		scanf("%s", zodiac[i]);
		zptr[i] = zodiac[i];
	}
 
	for(i = 11; i >= 0; i--){
		for(j = 0; j < i; j++){
			if(strcmp(zptr[j], zptr[j + 1]) > 0){
				temp = zptr[j];
				zptr[j] = zptr[j + 1];
				zptr[j + 1] = temp;
			}
		}
	}
 
	for(i = 0; i < 12; i++)
		printf("%s\n", zptr[i]);
	return 0;
}
```

### Reference
[1] 由片語學習C程式設計 劉邦鋒