---
title: "Example of common recursion."
date: 2017-12-16T10:26:44+08:00
draft: false
tags: ["C", "程式"]
categories: ["Programming"]
---

# 常見的遞迴式總整理 <br>
1. [Factorial](#1)
2. [最大公因數 GCD (Greatest Common Division)](#2)
3. [Fibonacci Number](#3)
4. [1+2+3+...+N](#4)
5. [1^2+2^2+3^2+...+N^2](#5)
6. [1!+2!+...+N!](#6)
7. [二項式係數 (Binomial Coefficient)](#7)
8. [計算x^y](#8)
9. [河內塔(Hanoi)](#9)

## <a name="1">1. Factorial</a>

```C
#include <stdio.h>
 
int main(void) {
	int k = 10;
	int sum;
	sum = factorial(k);
	printf("%d", sum);
}
 
int factorial(int n){
	if(n == 0) return 1;
	else return (n * factorial(n - 1));
}
```

## <a name="2">2. 最大公因數 GCD (Greatest Common Division)</a>
```C
#include <stdio.h>
 
int main(void) {
	int x, y;
	int result;
	scanf("%d %d", &x, &y);
	result = gcd(x, y);
	printf("%d", result);
}
int gcd(int a, int b){
	if(b == 0) return a;
	else return gcd(b, a % b);
}
```

## <a name="3">3. Fibonacci Number</a>
```C
include <stdio.h>
 
int main(void) {
	int k;
	int sum;
	scanf("%d", &k);
	sum = fib(k);
	printf("%d", sum);
}
 
int fib(int n){
	if(n == 1 || n == 0) return n;
	else return fib(n - 1) + fib(n - 2);
}
```

## <a name="4">4. 1+2+3+...+N</a>
```C
#include <stdio.h>
 
int main(void) {
	int k;
	int x;
	scanf("%d", &x);
	k = sum(x);
	printf("%d", k);
}
int sum(int n){
	int total;
	if(n == 0) return 0;
	else return( n + sum(n - 1));
}
```

## <a name="5">5. 1^2+2^2+3^2+...+N^2</a>
```C
#include <stdio.h>
 
int main(void) {
	int i;
	int k;
	scanf("%d", &i);
	k = squareSum(i);
	printf("%d", k);
}
 
int squareSum(int n){
	if(n <= 0) return 0;
	else return n * n + squareSum(n - 1);
}
```

## <a name="6">6. 1!+2!+...+N!</a>
```C
#include <stdio.h>
 
int main(void) {
	int k, i;
	scanf("%d", &i);
	k = sum(i);
	printf("%d", k);
}
int factorial(int n){
	if(n == 1) return n;
	else return( n * factorial(n - 1) );
}
int sum(int n){
	if(n == 1) return n;
	else return(factorial(n) + sum(n - 1));
}
```

## <a name="7">7. 二項式係數 (Binomial Coefficient)</a>
```C
#include <stdio.h>
 
int main(void) {
	int k;
	int a = 5 , b = 2;
	k = bin(5 , 2);
	printf("%d", k);
	return 0;
}
 
int bin(int n, int m){
	if(m == 0 || m == n) return 1;
	else return bin(n - 1, m) + bin(n - 1, m - 1);
}
```

## <a name="8">8. 計算x^y </a>
```C
#include <stdio.h>
 
int main(void) {
	int a = 5 , n = 4;
	int value;
	value = Exp(a, n);
	printf("%d", value);
	return 0;
}
 
int Exp(int x, int y){
	if(y == 0) return 1;
	else return Exp(x, y - 1) * x;
}
```

## <a name="9">9. 河內塔(Hanoi)</a>
```C
#include <stdio.h>

/* Hanoi : with complete step */

int main()
{
    int number;
    printf("Enter a number of towers of Hanoi: ");
    scanf("%d", &number);

    hanoi(number, 'B', 'A', 'C');
    return 0;
}

void hanoi(int n, char buffer, char source, char destination)
{
    printf("hanoi(%d, %c, %c, %c)\n", n, buffer, source, destination);
    if(n == 1) printf("move a disk from %c to %c\n", source, destination);
    else{
        hanoi(n-1, destination, source, buffer);
        printf("move a disk from %c to %c\n", source, destination);
        hanoi(n-1, source, buffer, destination);
    }
}
```