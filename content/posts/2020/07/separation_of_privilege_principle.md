---
title: "Separation of Privilege Principle"
date: 2020-07-16T10:36:25+08:00
draft: true
categories: ["Linux"]
tags: ["Security", "Linux"]
---

# Separation of Privilege Principle

在聆聽 Coursera 《Principle of Secure Programming》課程時，提到其中一個安全程式設計原則：Separation of Privilege Principle，其中一個例子為 `su` 指令(substitute user or superuser)，`su` 指令在 Linux 上會將原先的使用權限晉升為 root 權限(最高權限)

## `su` 指令
在 Linux 環境上可透過 `su` 指令來替換其他使用者，甚至是 root(superuser)，但在能夠使用的前提之下，必須要知道其使用者的登入密碼，而非目前的使用者密碼