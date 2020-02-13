---
title: "Example of common iteration"
date: 2017-12-16T10:26:44+08:00
draft: false
tags: ["C", "程式"]
categories: ["Programming"]
---

# 常見Iterative式
* [Iteration 疊代、跌代](#0)
* [Iteration 例子](#0-1)
1. [Factorial](#1)
2. [最大公因數 GCD (Greatest Common Division)](#2)
3. [Fibonacci Number](#3)
4. [1+2+3+...+n](#4)
5. [1^2+2^2+3^2+...+n^2](#5)
6. [1!+2!+3!+…+n!](#6)
7. [x^n](#7)

### <a name="0">Iteration 疊代、跌代</a>
定義：不斷利用目前求得的數值，再求得新數值，再用新數值繼續求得下一個新的值。  

實作方式：使用迴圈(for、while、do-while)

* 相關連結  
	+ [演算法筆記](http://www.csie.ntnu.edu.tw/~u91029/AlgorithmDesign.html#4)   
	+ [Incremental vs. Iterative development](http://alistair.cockburn.us/Incremental+versus+iterative+development)  
	
* 補充  

Development | Define | Goal 
--- | --- | --- 
Incremental | **staging and scheduling strategy** | improve your **process** 
Iterative   | **rework scheduling strategy** | improve your **product** 

  
## <a name="0-1">Iteration </a>
  
## <a name="1">1. Factorial </a>
```C
#include <stdio.h>
 
int main(void) {
	int i;
	int sum = 1, n;
	for(i = 1; i <= n; i++){
		sum *= i;
	}
	printf("%d", sum);
}
```

## <a name="2">2. 最大公因數 GCD (Greatest Common Division)</a>
```C
#include <stdio.h>
 
int main(void) {
	int k; 
	int a, b;
	scanf("%d %d", &a, &b);
	while(a % b != 0){
		k = a % b;
		a = b;
		b = k;
	}
	printf("%d", b);
}
```

## <a name="3">3. Fibonacci Number</a>
```C
#include <stdio.h>
 
int main(void) {
	int i = 0, n;
	int fib[n];
	fib[0] = 0;
	fib[1] = 1;
 
	scanf("%d", &n);
	for(i = 2; i <= n; i++){
		fib[i] = fib[i -1] + fib[i - 2];	
	}
	for(i = 2; i <= n; i++){
		printf("%d\n", fib[i]);
	}
}
```

## <a name="4">4. 1+2+3+...+n</a>
```C
#include <stdio.h>
 
int main(void) {
	int i, n;
	int sum = 0;
	scanf("%d", &n);
	for(i = 1; i <= n; i++){
		sum += i;
	}
	printf("%d", sum);
}
```

## <a name="5">5. 1^2+2^2+3^2+...+n^2</a>
```C
#include <stdio.h>
 
int main(void)
{
    int i, sum=0;
    int n;
    scanf("%d", &n);
 
    if(n <= 0) return 0;
    for(i = 0; i <= n; i++){
    	sum += i * i;
    }
    printf("%d", sum);
    return 0;
}
```

## <a name="6">6. 1!+2!+3!+…+n!</a>
```C
#include <stdio.h>
int main(void) {
	int i = 0, n;
	int sum = 1, total = 0;
	scanf("%d", &n);
	for(i = 1; i <= n; i++){
		sum *= i;
		total += sum;
	}
	printf("%d", total);
}
```

## <a name="7">7. x^n</a>
```C
#include <stdio.h>

/* Iteration : x ^ n */

int main(void) {
	int x;
	int i; // counter
	int n; // power
	int sum = 1;
	
	scanf("%d%d", &x, &n);
	
	for(i = 1; i <= n; i++){
		sum *= x;
	}
	printf("%d^%d is %d", x, n, sum);
	return 0;
}
```