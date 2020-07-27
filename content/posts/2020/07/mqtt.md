---
title: "MQTT 教學筆記"
date: 2020-07-26T13:25:56+08:00
draft: false
categories: ["Note"]
tags: [MQTT, IOT, Note]
---

# MQTT 教學筆記

MQTT (Message Queuing Telemetry Transport) 是物聯網極受歡迎的訊息傳輸協定 <br>

在使用 IOT 相關裝置時，當 Sensor 接收到資料時可透過 MQTT協定來傳送資料。<br>



##  MQTT 介紹

MQTT 以 發布（Publish）/ 訂閱（Subscribe）**主題(Topic)**的方式來進行資料傳輸，發布的那端稱為 **發布者(Publisher)**，而訂閱的那端稱為 **訂閱者(Subscriber)**，而發布者和訂閱者之間則透過 **代理人(Broker)** 來進行中繼傳輸者，提供發布者和訂閱者服務。以現今的角度來看，發布者可以比喻像是 影音平台上的創作者，如：Youtube、Netflix，每天可隨時發布影片到影音平台上，而訂閱者可比喻為觀看影音平台上影片的觀眾，可選擇性訂閱喜歡的頻道來獲取娛樂、吸取新知，影音平台就能形容成代理人，接收創作者的影片到平台上，並將你所訂閱的頻道發布到你的影音平台上。<br>

相信這樣的比喻你已經懂 MQTT 基本概念了，現在讓我們來了解一下這些名詞的真實定義：<br>

* **發布者(Publisher)**：傳送 IOT 裝置所蒐集到的資料給 Broker，通常是 Sensor 的資料，這樣的行為稱為 **發布（Publish）**，而負責這個行動的角色就稱為發布者。
* **代理人(Broker)**：負責接收發布者的資料，並且**將所接收到的資料傳送給有訂閱該主題的訂閱者，此行為稱為訂閱**（Subscribe），負責這些行動的角色就稱為代理人。
* **訂閱者(Subscriber)**：負責接收 Broker 所傳送的資料。

發布者和代理人為 **Client 端**，代理人為 **Server 端**。<br>

MQTT 為輕量化的傳輸協定，屬於連線導向，將訊息從某個端點傳送至 Server，現今由於行動裝置技術逐漸成熟， IOT 裝置輕巧、能夠遠端控制與監控，甚至作為穿戴式裝置，功能性的支援上就會有所限制，利用無線傳輸技術，網路頻寬與低功耗就成了研究問題。 <br>

隨著硬體研發越來越進步，智慧家庭的時代來臨，以及工業自動化、環境監測的應用，無線感測網路（Wireless Sensor Netowks, WSN）的技術越來越受到重視，有時候身邊有琳瑯滿目的 IOT 感測裝置，但在某些情境下，要如何只提取**需要的特定資料，而不是接收所有的資料**？假設 A 設備可以感測溫度、濕度或是離前方物體的距離，我們為這幾個感測器資料設置各種不同的主題：

* 溫度主題：`Home/Temperature`
* 濕度主題：`Home/Humidity`
* 距離主題：`Home/Distance`

若現在我們只想知道溫度，我們就可以透過 MQTT 的協定機制，只需要訂閱 `Home/Temperature` ，就能夠有效減少資料傳輸的流量，如此傳輸效率也就更好。<br>

## 安裝 MQTT mosquitto

安裝 broker (server 端)

```sh
sudo apt install mosquitto
```

安裝完成之後，本機端將會開啟 1883 port 服務來聆聽是否有請求要連線，若有開啟服務即安裝成功。<br>

```sh
Sophie@ubuntu:~$ netstat -a | grep 1883
tcp        0      0 0.0.0.0:1883            0.0.0.0:*               LISTEN     
tcp6     0      0 [::]:1883            		    [::]:*                	     LISTEN
```

## 安裝 MQTT mosquitto-clients

安裝 clinet 端

```sh
sudo apt install mosquitto-clients
```

安裝之後便可使自由使用 publish 和 subscribe。 <br>

發布（Publish）<br>

```sh
mosquitto_pub -h localhost -t Home/Temperature -m "Sensor Data or message"
```

訂閱 （Subscribe）<br>

```sh
mosquitto_sub -h localhost -t Home/Temperature
```

* options <br>
  `-h` ： host  <br>
  `-t` ： topic <br>
  `-m` ： message <br>
  `-k` ： keep alive <br>
  `--help` 詳細說明

使用範例如下圖 <br>

<img src="../../../../../img/mqtt/02.png"><br>



## Reference

[1]  [Ravi Kishore Kodali, SreeRamya Soratkal, MQTT based home automation system using ESP8266, 2016](https://ieeexplore.ieee.org/abstract/document/7906845)

