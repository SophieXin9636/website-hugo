---
title: "Commonly Used Taylor Series"
date: 2018-01-07T15:45:44+08:00
draft: false
tags: ["C", "程式"]
categories: ["Programming"]
---

# 常見的泰勒級數 Commonly Used Taylor Series
常見的泰勒展開式 (Taylor expansion) 有以下
1. [1/(1-x)](#1)
2. [sin(x)](#2)
3. [cos(x)](#3)
4. [e^x](#4)
5. [ln(1+x)](#5)
6. [arctan(x)](#6)

## <a name="1">1/(1-x)</a>
<img src="https://sophiexin9636.github.io/img/post/c/1-x.jpg" width="50%" height="50%" >

```C
#include <stdio.h>
 
/* Taylor's Series : 1/(1-x) */
 
int main(void) {
	double x; // input data
	double sum = 1.0;
	int i, n = 100;
	double xpower = 1.0;
 
	scanf("%lf", &x);
	if( x > -1 && x < 1){
		for(i = 1; i <= n; i++){
			xpower *= x;
			sum += xpower;
		}
		printf("%f\n", sum);
	}
	return 0;
}
```
## <a name="2">sin(x)</a>
<img src="https://sophiexin9636.github.io/img/post/c/sinx.jpg" width="50%" height="50%" >

```C
#include <stdio.h>
#include <math.h>
 
/* Taylor's Series : sin(x) 
 * Sine function using Taylor expansion */
 
int main(void) {
	double x; // input data
	double sum = 0.0;
	int i, n = 15;
	
	printf("Enter the value of x : ");
	scanf("%lf", &x);
	double term = x;
	sum += term;
	
	for(i = 2; i <= (2 * n + 1); i++){
		term *= (x / i);
		if(i % 2 == 1){
			term = -term;
			sum += term;
		}
	}
	printf("\nuse taylor series is : %f\n", sum);
	printf("sin(x) is : %f\n", sin(x)); // check
	return 0;
}
```

## <a name="3">cos(x)</a>
<img src="https://sophiexin9636.github.io/img/post/c/cosx.jpg" width="60%" height="60%" >

```C
#include <stdio.h>
#include <math.h>
 
/* Taylor's Series : con(x) 
 * Cosine function using Taylor expansion */
 
int main(void) {
	double x; // input data
	double sum = 0.0;
	int i, n = 15;
	
	printf("Enter the value of x : ");
	scanf("%lf", &x);
	double term = 1.0;
	sum += term;
	
	for(i = 1; i <= (2 * n); i++){
		term *= (x / i);
		if(i % 2 == 0){
			term = -term;
			sum += term;
		}
	}
	printf("\nuse taylor series is : %f\n", sum);
	printf("cos(%f) is : %f\n", x, cos(x)); // check
	return 0;
}
```

## <a name="4">e^x</a>
<img src="https://sophiexin9636.github.io/img/post/c/e^x.jpg" width="50%" height="50%" >

#### <方法一> 
由於float占4 Bytes，double占8 Bytes，需要高度精密計算時，使用double會勝過float型態，
因此使用**double**型態來宣告變數**e**，較float精確。  
  
```C
#include <stdio.h>

/* Taylor's Series : e^x */

int main(void) {
	double x;
	double e = 1.0;
	int i;  // counter
    int n = 10;
	int factorial = 1;
	double xpower = 1.0;
	
	scanf("%lf", &x);
	for(i = 1; i <= n; i++){
		factorial *= i;
		xpower *= x;
		e += xpower / factorial;
	}
	printf("%f\n", e);
	return 0;
}
```

#### <方法二>
使用<方法一>會有一個問題，當n 變大時，變數factorial容易發生溢位(overflow)，將可能導致計算結果不正確。  

此方法不直接計算階乘(分母)的部分，避免溢位。
```C
#include <stdio.h>

/* Taylor's Series : e^x */

int main(void) {
	int i;	// counter
	double term = 1.0; // 數列的第n項的值
	double e = 1.0;
	double x; // input variable
	int n = 20; // 級數加到第n項
	
	scanf("%lf", &x);
	for(i = 1; i <= n; i++){
		term *= (x / i);
		e += term; 
	}
	return 0;
}
```

## <a name="5">ln(1+x)</a>
<img src="https://sophiexin9636.github.io/img/post/c/ln(1+x).jpg" width="50%" height="50%" > 

```C
#include <stdio.h>
#include <math.h>
 
/* Taylor's Series : ln(1+x) 
 * Sine function using Taylor expansion */
 
int main(void) {
	double x; // input data
	double sum = 0.0;
	int i, n = 20;
	
	printf("Enter the value of x : ");
	scanf("%lf", &x);
	double xpower = x;
	sum += x;
	
	for(i = 2; i <= n; i++){
		xpower *= -x;
		sum += xpower / i;
	}
	printf("\nln(%f) use taylor series is : %f\n", 1+x, sum);
	printf("ln(%f) = %f", 1+x, log(1+x)); // check
	return 0;
}
```

## <a name="6">arctan(x)</a>
<img src="https://sophiexin9636.github.io/img/post/c/arctan(x).jpg" width="50%" height="50%" > 

```C
#include <stdio.h>
#include <math.h>
 
/* Taylor's Series : arctan(x) 
 * Sine function using Taylor expansion */
 
int main(void) {
	double x; // input data
	double sum = 0.0;
	int i, n = 15;
	
	printf("Enter the value of x : ");
	scanf("%lf", &x);
	double xpower = x;
	sum += x;
	
	for(i = 2; i <= (2 * n + 1); i++){
		xpower *= x;
		if(i % 2 == 1){
			xpower = -xpower;
			sum += xpower / i;
		}
	}
	printf("\narctan(%f) use taylor series is : %f\n", x, sum);
	printf("arctan(%f) = %f", x, atan(x)); // check
	return 0;
}
```