---
title: "Assignment to Expression With Array Type"
date: 2017-12-12T20:38:28+08:00
draft: false
tags: ["C", "程式"]
categories: ["Programming"]
---

Learning from Error : assignment to expression with array type.

## Error Learning : assignment to expression with array type
1. [Error 1](#1)
2. [Error 2](#2)

```C
#include <stdio.h>
#include <stdlib.h>

struct node{
	char course_name[20];
	struct node *next;
};

typedef struct node Node;

int main(void) {
	Node *head = malloc(sizeof(Node));
	head->course_name = "Opearating system";
	head->next = NULL;

	int i;
	int number; // the number of course
	printf("請輸入課程數量: ");
	scanf("%d", &number);

	Node *current = head;
	for(i = 1; i <= number; i++){
		current->next = malloc(sizeof(Node));

		char name[20]; // string
		printf("請輸入課程名稱: ");
		scanf("%s", name);

		current->course_name = name;
		current = current -> next;
		current -> next = NULL;
	}
	return 0;
}
```

### <a name="1">**Error1 : assignment to expression with array type (line 13, line 29)**</a>

Reason : You cannot directly assign to an array.

Solution : You need to use ```strcpy()``` to copy into the array.

1. What is LHS and RHS?  
Address1 is on the left hand side (LHS), and "Address2" is on the right hand side (RHS).
	<Ex> 
	
	```
    if(Address1 == Address2){...}
    else{...}
	```  

2. Difference between assignment and initialization.
    <Ex1>
    ```
    int a;  // declare a variable
    int b = 5; // declare and initialize a variable almost at the same moment
    ```  
    
    <Ex2>
    
    ```
    int a; // declare a variable
    a = 3; // assign a variable
    ```  
    
雖然看似沒有不同，但實際compiler轉譯到assembler language是代表不同的assembly code。如果使用在陣列上，那麼用第二個方法就會出現錯誤(直接assign到在LHS的array變數就會出錯)，必須使用 ```strcpy()``` (使用 ```strcpy()
```需引入標頭檔 ```#include<string.h>```)

除了array以外，```struct```結構型態、```const```常數修飾子，都不能這樣使用

<Error example 1> array
 + ```char name[] = "Sophie"; // is valid```
 + ```char name[]; name[]= "Sophie"; // is not valid```

<Error example 2> struct  

```struct classmate student1 = {.name ="Jack", .no ="34567"};  // valid```
  
  
``` 
struct classmate student1;
student1 = {.name ="Jack", .no ="34567"}; // is not valid 
```

<Error example 3> const
 + ``` const int a = 5; // is valid ```
 + ``` const int a; a = 5; // is not valid```  
 

### <a name="2">Error 2</a>
<img src="https://sophiexin9636.github.io/img/input-error.jpg" width="60%" height="60%" >
  
  
 
 Solution Code:  

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct node{
	char course_name[50];
	struct node *next;
};

typedef struct node Node;

int main(void) {
	Node *head = malloc(sizeof(Node));
	// head->course_name = "CS course"; //error
	strcpy(head->course_name, "CS course");
	head->next = NULL;

	int i;
	int number; // the number of course
	printf("請輸入課程數量: ");
	scanf("%d\n", &number);
	/* 輸入值時，會按Enter(換行字元'\n')，來結束number的輸入，置入到buffer中
	 * 若沒有再輸入值當中加入'\n'，會被fgets當作輸入用，
	 * 因此在輸入course name時，會少一次輸入
	 * 所以在%d加上'\n'字元讓scanf吃掉enter('\n'換行字元)
	 */
	 
	Node *current = head;
	for(i = 1; i <= number; i++){
		current->next = malloc(sizeof(Node));

		char name[50]; // string
		printf("Please Enter Course Name %d\n: ", i);
		fgets(name, sizeof(name), stdin);

		// 1. scanf("%s", name); 不使用這種是因為打空白直接算另一個字串
		// 2. gets(name) 不使用這種是因為也有一樣的問題

		// current->course_name = name // error
		strcpy(current->course_name, name);
		current = current -> next;
		current -> next = NULL;
	}

	// print all course and clear memory
	current = head;
	Node *temp; // temporary and delete
	i = 1;
	while(current != NULL && i <= number){
	    printf("Course %d is: %s\n", i, current->course_name );
        temp = current->next;
        free(current);
        current = temp;
        i++;
	}
	head = NULL;
	return 0;
}
```  


## Reference:  
[stack overflow](https://stackoverflow.com/questions/37225244/error-assignment-to-expression-with-array-type-error-when-i-assign-a-struct-f)  
[assignment and initialization](https://stackoverflow.com/questions/16217839/is-there-a-difference-between-initializing-a-variable-and-assigning-it-a-value-i)  