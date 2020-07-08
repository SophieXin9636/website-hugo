---
title: "Format_string"
date: 2020-07-06T18:44:25+08:00
draft: true
---

## 什麼是 Format String (格式化字串) ?

當我們在撰寫程式時候，若需要 I/O，<br>
根據 CWE (Common Weakness Enumeration) 弱點分類系統，編號為 CWE-134[1]: Use of Externally-Controlled Format String<br>
也就是說，如果能夠從外部來源輸入 **格式化字串(Format String)** 進去，便能夠取得執行時的參數值。 <br>
以 C 語言為例子： <br>
```C
#include <stdio.h>

int main(){
	char buf[100];
	scanf("%s", buf);
	printf(buf); /* Format String 漏洞 */
	return 0;
}
```

編譯所印出訊息
```shell
% gcc test.c -o test
test.c: In function ‘main’:
test.c:6:9: warning: format not a string literal and no format arguments [-Wformat-security]
  printf(buf); /* Format String  */
         ^~~
```

若正常輸入，則會按照輸入的值來輸出
```
./test
10
10
```

反之，若輸入的是格式化字串，則會 leak 出 memory 的內容


通常使用到下列 function，必須注意避免 format string 漏洞： <br>

```C
#include <stdio.h>
printf(const char *format, ...);
fprintf(FILE *stream, const char *format, ...);
dprintf(int fd, const char *format, ...);
sprintf(char *str, const char *format, ...);
snprintf(char *str, size_t size, const char *format, ...);
int __printf_chk(int flag, const char * format);

#include <stdarg.h>
vprintf(const char *format, va_list ap);
vfprintf(FILE *stream, const char *format, va_list ap);
vdprintf(int fd, const char *format, va_list ap);
vsprintf(char *str, const char *format, va_list ap);
vsnprintf(char *str, size_t size, const char *format, va_list ap);
```

## 備註
[1] [CWE-134: Use of Externally-Controlled Format String](https://cwe.mitre.org/data/definitions/134.html)