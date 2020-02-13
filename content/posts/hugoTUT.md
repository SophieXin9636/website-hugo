---
title: "Hugo Tutorial"
date: 2020-02-13T17:14:09+08:00
draft: false
---

# Hugo Tutorial

### 前言
原先使用 boostrap 前端框架撰寫個人網站,
由於有撰寫文章的需求，每一次新增文章就必須修改主頁面的index.html以及posts相關的連結。
然而在有網站開發經驗的室友推薦，便開始嘗試使用hugo


### hugo 介紹
hugo是使用**go程式語言**所開發的**靜態網頁生成器**，可透過一些hugo指令並且搭配別人撰寫好的主題，直接生成出一個基於html+css+javascript頁面，無須撰寫html+css+javascript

* 若想要新增文章，也無須手動建立 html網頁，而是使用 `hugo new posts/my_post.md` 直接撰寫markdown即可，對於懶人非常方便，無須做重複撰寫html的動作。

## 建立個人頁面

### 前言
由於在使用hugo之前，就已經建立好個人的github頁面
若是第一次使用github，並且想使用hugo工具產生個人頁面，
推薦搜尋 ==hugo github教學==
或是直接點進該連結 [Qucik Start](https://gohugo.io/getting-started/quick-start/)

此篇教學會以**已有個人github網站，且想要改成hugo快速建立個人頁面及文章的使用者。**

### 安裝 brew
brew 是套件管理工具，類似於ubuntu的apt-get
可根據個人的作業系統決定安裝方式
[安裝連結](https://gohugo.io/getting-started/installing)

### 安裝 hugo
```
brew install hugo
```
* 確認是否安裝成功
```
hugo version
```

### 將個人的github個人網站的repo下載至本地端

(個人建議備份整個資料夾在備份區，避免做到一半發生不可逆的問題)
```
git clone https://github.com/<account>/<account>.github.io
cd <account>.github.io
```

### 建立 hugo site

```
hugo new site hugo-website
cd hugo-website
```
則會產生 預設的hugo的檔案結構
![](https://i.imgur.com/jeKdCfT.png)

### 選擇主題
* 個人是選擇 hugo-notepadium 主題
* 可根據[hugo主題網站](https://themes.gohugo.io/)選擇喜歡的主題，在到該主題的repo下載

```
git init
cd hugo-website
git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
```

### 編輯 `config.toml`
選擇喜歡的編輯器開啟 `config.toml`
作者是使用ubuntu 18.04 sublime開啟
* Tips: 在編輯 `config.toml`時，可直接看該theme的`README.md`，裡面會有詳盡的介紹


## 建立個人post
```
hugo new posts/<name>.md
```

則該檔案會出現以下的header，該header的資訊是隱藏的

```
---
title: "Test"
date: 2020-02-13T18:28:53+08:00
draft: true
---
```
* draft為true時，則頁面不會出現在github上面，但在本地端執行`hugo server -D`時，則會出現在localhost
* 反之為draft為false，commit到github home page時將會出現在主頁上

### 使用 hugo server 架起網站（本地端測試）
```
hugo server -D
```
即可看到本地端的個人頁面

### 建立 hugo repo 在 github上
* 在個人github頁面建立 hugo-website 的倉庫

* 不要勾`Initialize this repository with a README`的選項
在本地端按照下列輸入指令
```
echo "# hugo-website" >> README.md
git add .
git commit -m "first commit"
git remote add origin https://github.com/<your-account>/hugo-website.git
git push -u origin master
```



最後選擇喜歡的編輯器撰寫markdown吧！