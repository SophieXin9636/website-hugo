---
title: "質數相關程式題"
date: 2017-12-19T20:25:01+08:00
draft: false
tags: ["C", "程式"]
categories: ["Programming"]
---

# 質數相關程式題
程式的構想由簡易至難分類
1. [判斷是否為質數](#1)
2. [列出n以內的所有質數 list prime number](#2)
3. [補充：政大106年資科系轉學考考題 Next Prime Number](#3)
4. [質因數分解(Integer Factorization)](#4)
5. [完美數(Perfect Number)](#5)
	+ 補充：北大96年資工轉學考考題

## <a name="1">判斷是否為質數 </a>
  
#### <方法一> 最直覺，最好想，但較耗時
```C
#include <stdio.h>
 
int main() 
{
	int x;
	while(scanf("%d", &x) != EOF){
		printf("%d", IsPrime(x));
	}
}
int IsPrime(int p){
	if(p < 2) return 0;
	for(int i = 2; i < p; i++)
	  if(p % i == 0) return 0;
	return 1;	  
}
```

#### <方法二> 需要一點邏輯概念  
使用 **flag(旗標)** ，我們用它來判斷一件事情是否發生過。旗標的初始值為 0，
如果最後變成1，則代表該數「曾經發生過i能夠整除number」，即為合成數。  

如果旗標始終為0，則number為質數。
  
```C
#include <stdio.h>

int main(void) {
	int number;
	int i = 2, flag = 0;
	scanf("%d", &number);
	
	while(i * i <= number){
		if(number % i == 0){
			flag = 1;
			break;
		}
		else i++;
	}

	if(flag == 0) printf("%d is a prime number.\n", number);
	else printf("%d is not a prime number.\n", number);
	
	return 0;
}
```  

#### <方法三> 與方法二雷同，只是在語法上些許不同
方法二使用的迴圈是while，方法三則是使用for迴圈，
在寫法上有一點巧思，能夠減少行數，非常值得學起來。
```C
#include <stdio.h>

int main(void) {
	int number;
	int i, flag = 0;
	scanf("%d", &number);
	
	for(i = 2; ((i * i) <= number) && (flag == 0); i++){
		if( (number % i) == 0){
			flag = 1;
			break;
		}
	}

	if(flag == 0) printf("%d is a prime number.\n", number);
	else printf("%d is not a prime number.\n", number);
	
	return 0;
}
```
#### <方法四>  建立質數表，最快速
一旦建立質數表後，判斷某數是否為質數的時間複雜度為 O(1)
缺點是需要O(n)的空間複雜度 (以空間換取時間的概念)
```C++
#include <iostream>
#include <cstring>
using namespace std;
 
int main() {
	int n; // number
	cin >> n;
	/* Create Prime Table */
	bool IsPrime[1100];
	memset(IsPrime, true, 1100);
	for(int i=2; i*i<=1100; i++){
		for(int j=2*i; j < 1100; j+=i){
			IsPrime[j] = false;
		}
	}

	if(IsPrime[n]) cout << n <<" is prime number." << endl;
	else cout << n <<" is not prime number." << endl;
	return 0;
}
```
## <a name="2">列出n以內的所有質數 list prime number</a>

#### <方法一> My version
```C
#include <stdio.h>

int isPrime(int number);
void print_prime_number(int n);

int main(void) {
	int number;
	printf("Please enter the range of prime number: ");
	scanf("%d", &number);
	printf("\n");
	
	print_prime_number(number);
}

int isPrime(int number){
	int i; // counter

	// if the number can be divided, then it isn't a prime number.
	for(i = 2; i < number; i++){
		if(number % i == 0) return 0;
	}
	return 1;
}

void print_prime_number(int n){
	
	int i;
	printf("The list of prime number within the %d:\n 2", n);
	for(i = 3; i <= n; i++)
		if( isPrime(i) ) printf(", %d", i);
}
```
#### <方法二> **旗標陣列** + 篩法 (Sieve)
某值加上原數 **j** 必為合數 (Composite number)(非質數的意思)，
無論i是否為質數，意思只要 i+kj (k = 1, 2, 3, ... , n)必為合數，意指 i 的倍數必為合成數。  
  
列出所有正整數。從 2 開始，刪掉 2 的倍數。找下一個未被刪掉的數字，找到 3 ，
刪掉 3 的倍數。找下一個未被刪掉的數字，
找到 5 ，刪掉 5 的倍數。如此不斷下去，就能刪掉所有合數，找到所有質數。

```C
#include <stdio.h>

int main(){
	int primearray[101];
	int i, n, j = 2;
	scanf("%d", &n);

	for(i = 2; i <= n; i ++){
		primearray[i] = 0;
	}
	while(j * j <= n) {
		while( primearray[j] == 1){
		  j++;
		}
        // ex: J = 2 -> 4 -> 6, 8, 10
        // ex: J = 3 -> 6 -> 9, 12, 15
        // ex: J = 11 -> 22 -> 33, 44, 55....
        // i的倍數，某值i加上原數j必為合數
		for(i = 2 * j; i <= n; i += j){
		  primearray[i] = 1;
		}
		j++;
	}
	for(i = 2; i <= n; i ++)
		if( primearray[i] == 0) // isPrime
			printf("%d ", i);
}
// reference: NTU C course C-slide
```

#### NTU version 
也是建立質數表的概念
```C
#include <stdio.h>
main() 
{
	int composite[1000];
	int i, j = 2;
	int count = 0;
	int column;
	int n;
	scanf("%d", &n);
	scanf("%d", &column);
	for (i = 2; i <= n; i++)
	  composite[i] == 0;
	while (j * j <= n) {
		while(composite[j] == 1)
		  j++;
		for(i = 2 * j; i <= n; i += j)
		  composite[i] = 1;
		j++;  
	}  
	for (i = 2; i <= n; i++)
	  if (composite[i] == 0) {
	  	if (count % column == (column - 1))
	  	  printf("%3d\n", i);
	  	else
	  	  printf("%3d ", i);
	  	count++;  
	  }
}
```

### <a name="3">補充：政大106年資科系轉學考考題 Next Prime Number</a>
Problem：Write a C function int NextPrime(int n) that ouput the smallest prime number greater than n.  
Note that two is the smallest prime number. For example, NextPrime(6) should output 7.

```C
#include <stdio.h>
int next_prime(int n)
{
	int j = 2, check;
	while(check == 0)
	{
		n++;
		for(j = 2; j < n - 1; j++)
		{
			if(n % j == 0) continue;
			else j++;
		}
		if(j == n - 1) check = 1;
		else check = 0;
	}
	printf("%d", n);
}
int main(void) {
	int x = 0;
	x = next_prime(6);
	return 0;
}
```

## <a name="4">質因數分解(Integer Factorization)</a>

#### <方法一> 不斷地用"判斷"處理
1. 判斷是否要印出```*```，以免多印或少印
2. 有的質因數會超過 某值得開根號，必須要用判斷的方式讓他印出，否則印出就會不對
```C
#include <stdio.h>
#include <math.h>

/* Integer Factorization */

int isPrime(int number);

int main(void) {
	int i = 2;
	int number, temp;
	scanf("%d\n", &number);
	if(isPrime(number)){
		printf("This number is a prime number.");
	}
	else{
		printf("The Integer Factorization is: ");
		temp = number;
		while(i * i <= number){
			if(temp % i == 0){
				temp = temp / i;
				printf("%d", i);
				if(temp != 1) printf(" * ");
			}
			else i++;
		}
		if(temp > sqrt(number)) printf("%d", temp);
	}
	return 0;
}

int isPrime(int number){
	int j;
	for(j = 2; j <= sqrt(number); j++){
		if(number % j == 0) return 0; // is not prime number
	}
	return 1; // is prime number
}
```

#### <方法二> 用陣列存質因數(prime factor)在一次印出來
```C
#include <stdio.h>

/* Integer Factorization */

int main(void) {
	int n;
	int i = 2; // counter
	int j = 0; // count
	int temp;
	int pri_fac[30]; // prime factor
	
	scanf("%d", &n);
	temp = n;
	
	while(i * i <= n){
		if(temp % i == 0){
			temp = temp / i;
			pri_fac[j] = i;
			j++;
			if(isprime(temp)){
				pri_fac[j] = temp;
				break;
			}
		}
		else i++;
	}
	
	for(i = 0; i <= j; i++){
		if(i == j) printf("%d", pri_fac[i]);
		else printf("%d * ", pri_fac[i]);
	}
	printf("\n");
	
	int count = 1;
	for(i = 0; i <= j; i++){
		if(pri_fac[i] == pri_fac[i+1])	count++;
		else{
			printf("%d^%d", pri_fac[i], count);
			if(i != j) printf(" * ");
			count = 1;
		}
	}
	return 0;
}
int isprime(int number){
	int i = 2;
	while(i * i <= number){
		if(number % i == 0) return 0;
		else i++;
	}
	return 1;
}
```

## <a name="5">完美數 (Perfect Number)，又稱完全數</a>
### 補充：北大96年資工轉學考考題
Problem：寫一個C語言程式，可以印出2~10000的數中所有的完美數。所謂完美數是某數的所有因數(含1但不含自己本身)總和等於該數，例如6的因數有1、2、3，1+2+3=6，所以6是一個完美數。  

#### <方法一> 用函數方式，缺點是要多次呼叫
```C
#include <stdio.h>
int main(void) {
	int i = 0;
	for(i = 2; i <= 10000; i++)
		perfect(i);
}
void perfect(int a) {
	int sum = 0;
	if(a > 0){
		for(int p = 1; p < a; p++){
		if (a % p == 0)	sum += p;
		}
		if(sum == a)	printf("%d ", a);
	}
}
```

#### <方法二> 在main直接處理
```C
#include <stdio.h>

/* Perfect Number */

int main(void) {
	int i; // counter of number
	int j; // counter of factor
	int sum; // Sum of all factors in one number
	
	for(i = 2; i <= 10000; i++){
		sum = 0;
		
		for(j = 1; j < i; j++){
			if(i % j == 0){
				sum += j;
			}
		}
		
		if(sum == i) printf("%d ", sum);
	}
	return 0;
}
```