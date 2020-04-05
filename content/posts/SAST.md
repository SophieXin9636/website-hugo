---
title: "靜態分析工具介紹"
date: 2020-02-17T14:35:31+08:00
draft: false
tags: ["CTF", "pwn", "reverse"]
categories: ["CTF pwn"]
---

# Intro to Static Analysis Tools

打 CTF 時，若沒有用對工具，在分析時會花費許多時間 <br>
在寫 reverse 或 pwn 時，使用工具是絕對必要的，能夠適時減少不必要的時間。 <br>
在此介紹幾個打 CTF 常使用的工具及指令，作為參考。

## 基本概念介紹

### 工具

* IDA：ELF/PE
* Objdump：ELF、COFF
* readelf：ELF
* Radare2：ELF
* rabin2：ELF/PE/MZ

### Object File
以文件形式存放在磁碟（Disk）中的 object module
有三種形式
* Relocatable Object File
	* 在編譯時期與其他  Relocatable Object File 合併起來，產生出一個 Executable Object File
* Executable Object File
	* 直接載入到主記憶體執行
* Shared Object File
	* 為 Relocatable Object File 的一種
	* 可在執行時期動態連結並載入到主記憶體
	* 也可在載入到記憶體時期，才動態連結此檔案


Compiler 和 Assembler 可產生 Relocatable Object Files （包含 Shared Object File）<br>
而 Linkers 可產生 executable Object Files <br>

### 副檔名及 Object File 格式

各個作業系統的 Object File 格式不盡相同， <br>
像是 Windows 使用的是 PE 格式，而 Mac OS-X 使用的是 Mach-O 格式， <br>
而 Linux 與 Unix 系統使用 ELF 格式 <br>
上述格式統稱為 COFF 格式

* 可執行檔格式
* COFF (Common Object File Format)
	* PE 格式 (Portable Executable)
		* Executable (.exe)
		* Object File
		* shared Object File (.dll)
	* ELF 格式 (Executable Linkable Format)
		* Relocatable (.o/.obj)
		* Executable (`/bin/bash 文件`)
		* Object File
		* shared Object File (.so)

```
Windows: PE  （.exe)
Linux  : ELF (.o/.so/.elf 或無副檔名)
Unix   : .out/.o 或無副檔名
```

* 動態連結庫 檔案格式
```
Windows: .dll
Linux  : .so
```

* 靜態連結庫 檔案格式
```
Windows: .lib
Linux  : .a
```



## IDA
功能最完整，且可自行加plugin，例如暗色主題，<br>
由於此款軟體需要付費，在此不多做介紹

## Objdump
顯示 Object File 資訊

* -d： --disassemble 反組譯 （最常使用）
```
objdump -d -M intel ./run
```
* -f： 顯示檔頭，最有意義的資訊為 start address
* -s： 顯示所有 section 對應的 binary & hexdump
* -R： 顯示 dynamic relocation entries
* -p： 顯示 program header table
	* vaddr: virtual address
	* paddr: process address
	* filesz: segment size in object file 
	* memsz: segment size in memory
	* flags: run-time permissions

## readelf
顯示 ELF file 資訊

* `readelf -S binary` : 顯示 secion header 資訊

## Radare2
非常強大的靜態分析工具

* `(r2) pdf`：print disassemble function
* `(r2) aa` ：Analyze all (functions )
* `(r2) afl`：Analyze functions list
* `(r2) s main`：Seek to main()

## rabin2
Binary program info extractor <br>
可對 ELF/PE/MZ 檔做靜態分析

* `rabin2 -g binary`：顯示所有有意義的訊息

* `rabin2 -M binary`：顯示 `main()` 的位址

* `rabin2 -i binary`：顯示 使用了哪些 library 的函式 (show imports)
 <img src="https://i.imgur.com/2VUe3IH.png" width=80%>

* `rabin2 -S binary`：顯示所有的 section 的 位址及 rwx 權限

* `rabin2 -l binary`：顯示此 binary 所 linking 的 library(.so 檔案)
<img src="https://i.imgur.com/aBvvmC2.png" width=80%>

* `rabin2 -z binary`：印出檔案的可視字串及所在的 .data 及 .rodata section 及位址
<img src="https://i.imgur.com/iwoQKaI.png" width=80%>

* `rabin2 -e  binary`：顯示程式的 entrypoints (reverse 分析很方便！)

## reference
* Computer Systems. A Programmer’s Perspective 3e [7.3],[7.8]