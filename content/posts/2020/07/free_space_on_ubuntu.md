---
title: "Free Space or Extend Space On Ubuntu"
date: 2020-07-11T19:36:26+08:00
draft: false
categories: ["Linux"]
tags: ["Linux", "Ubuntu", "disk management"]
---

# Free Space or Extend Space On Ubuntu
當使用 ubuntu 系統時，發現出現下列訊息，則代表必須要做清理磁碟的動作 <br>
```
Low Disk space on "Filesystem root"
The volume "Filesystem root" has only 856.0 MB disk space remaining.
```
<img src="../../../../../img/free_space_on_ubuntu/free_space_01.JPG"><br>
<br>
使用 `bf` 指令可以查看 目前 file system disk space 的使用情況 <br>
可以看到以下訊息，47 GB 大小的 `/dev/sda2` 使用率 99%
```sh
Sophie@ubuntu:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            3.9G     0  3.9G   0% /dev
tmpfs           794M  1.9M  792M   1% /run
/dev/sda2        47G   44G  501M  99% /
tmpfs           3.9G  216K  3.9G   1% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
/dev/loop0      2.3M  2.3M     0 100% /snap/gnome-system-monitor/148
/dev/loop1      162M  162M     0 100% /snap/gnome-3-28-1804/128
/dev/loop3      256M  256M     0 100% /snap/gnome-3-34-1804/36
/dev/loop4      2.5M  2.5M     0 100% /snap/gnome-calculator/748
/dev/loop5       98M   98M     0 100% /snap/core/9289
/dev/loop2      584M  584M     0 100% /snap/intellij-idea-community/226
/dev/loop6      1.0M  1.0M     0 100% /snap/gnome-logs/100
/dev/loop8      161M  161M     0 100% /snap/gnome-3-28-1804/116
/dev/loop11     384K  384K     0 100% /snap/gnome-characters/550
/dev/loop10      55M   55M     0 100% /snap/core18/1705
/dev/loop7       55M   55M     0 100% /snap/gtk-common-themes/1502
/dev/loop9      141M  141M     0 100% /snap/gnome-3-26-1604/100
/dev/loop14     256M  256M     0 100% /snap/gnome-3-34-1804/33
/dev/loop17     2.5M  2.5M     0 100% /snap/gnome-calculator/730
/dev/loop12      97M   97M     0 100% /snap/core/9436
/dev/loop19     384K  384K     0 100% /snap/gnome-characters/539
/dev/loop18      63M   63M     0 100% /snap/gtk-common-themes/1506
/dev/loop15     2.3M  2.3M     0 100% /snap/gnome-system-monitor/145
/dev/loop20     584M  584M     0 100% /snap/intellij-idea-community/232
/dev/loop13      55M   55M     0 100% /snap/core18/1754
/dev/loop22     1.0M  1.0M     0 100% /snap/gnome-logs/93
/dev/loop16     186M  186M     0 100% /snap/zaproxy/5
/dev/loop21     141M  141M     0 100% /snap/gnome-3-26-1604/98
/dev/sda1       946M  116M  765M  14% /boot
tmpfs           794M   16K  794M   1% /run/user/121
vmhgfs-fuse     224G  157G   68G  70% /mnt/hgfs
tmpfs           794M   32K  794M   1% /run/user/1000
```

```sh
Sophie@ubuntu:~$ sudo fdisk -l /dev/sda
Disk /dev/sda: 80 GiB, 85899345920 bytes, 167772160 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x316dedfd

Device     Boot     Start       End   Sectors  Size Id Type
/dev/sda1  *         2048   2002943   2000896  977M 83 Linux
/dev/sda2         2002944 102006783 100003840 47.7G 83 Linux
/dev/sda3       102006784 104855551   2848768  1.4G 82 Linux swap / Solaris
```
<br>
以下列出三個方法來避免這個問題 <br>

## Method 1: 清除不必要的套件、殘留檔

使用 `autoclean` 只會刪除 `/var/cache/apt/archives/` 已經過期的 deb 套件檔
```sh
sudo apt-get autoclean
```

而 `clean` 會將 `/var/cache/apt/archives/` 路徑內所有 deb 檔(安裝檔) 刪掉 
```sh
sudo apt-get clean
```

不定時使用 `autoremove`，它會移除掉不再使用的 dependencies (指安裝某些軟體所需的 packages )，並且會顯示移除了哪些 packages
```sh
Sophie@ubuntu:~$ sudo apt-get autoremove
0 upgraded, 0 newly installed, 66 to remove and 0 not upgraded.
After this operation, 251 MB disk space will be freed.
```



## Method 2: journalctl 指令

`journalctl` 是一個用來檢索 systemd journal 的指令，並且可透過不同的 option 來篩選想要的內容 （詳見 `man journalctl` 在此不多綴述)  <br>
systemd journal 為 log 管理系統，其保存了許多來自 kernal space 和 user space 的內容。 <br>
使用的是 ubuntu 18.04 虛擬機，並且下 `journalctl` 之後，會將我使用的情況紀錄下來

```sh
sophie@ubuntu:~$ journalctl 
-- Logs begin at Thu 2020-04-23 22:28:31 CST, end at Mon 2020-07-13 21:32:06 CST. --
 四  23 22:28:31 ubuntu-for-ais3 systemd[2034]: Starting D-Bus User Message Bus Socket.
 四  23 22:28:31 ubuntu-for-ais3 systemd[2034]: Listening on GnuPG cryptographic agent and passphrase cache (restricted).
 四  23 22:28:31 ubuntu-for-ais3 systemd[2034]: Reached target Timers.
... (還有201344行...此部份做省略)
...
...
 七  13 21:30:36 ubuntu dbus-daemon[1037]: [system] Successfully activated service 'org.freedesktop.nm_dispatcher'
 七  13 21:30:36 ubuntu systemd[1]: Started Network Manager Script Dispatcher Service.
 七  13 21:30:36 ubuntu nm-dispatcher[31819]: req:1 'dhcp4-change' [ens33]: new request (1 scripts)
 七  13 21:30:36 ubuntu nm-dispatcher[31819]: req:1 'dhcp4-change' [ens33]: start running ordered scripts...
 七  13 21:32:06 ubuntu systemd[1]: dev-disk-by\x2duuid-e49f06ad\x2d0d3a\x2d4498\x2d82a9\x2d58b2c9db3015.device: Job dev-disk-by\x2duuid-e49f06ad\x2d0d3a\x2d4498\x2d82
 七  13 21:32:06 ubuntu systemd[1]: Timed out waiting for device dev-disk-by\x2duuid-e49f06ad\x2d0d3a\x2d4498\x2d82a9\x2d58b2c9db3015.device.
```

```sh
sophie@ubuntu:~$ journalctl --disk-usage
Archived and active journals take up 840.3M in the file system.
```

由於佔用的空間實在太大，因此使用了 `--vacuum-time=3d`  option，僅保留過去三天的紀錄 <br>
清除完成後，還是有 5000 行左右，真的很可怕 XD
```sh
sophie@ubuntu:~$ sudo journalctl --vacuum-time=3d
Vacuuming done, freed 792.2M of archived journals from /var/log/journal/0c1a867975654e11ab0fa8ab9b00dc0f.
Vacuuming done, freed 0B of archived journals from /var/log/journal.
```
清除完後，從原本 840 MB 減少成 48 MB 
```sh
sophie@ubuntu:~$ journalctl --disk-usage
Archived and active journals take up 48.0M in the file system.
```

## Method 3: Gparted 硬碟分割工具
Gparted (GNOME Partition Editor) 為硬碟分割軟體，ubuntu 系統有內建，因此無須額外下載。 <br>
假如檔案系統還有其他的額外空間，便可使用此工具來做空間延伸。<br>

目前的硬碟分割配置 <br>
<img src="../../../../../img/free_space_on_ubuntu/free_space_02.JPG" width="80%"><br>

對 linux-swap 右鍵點選 Swapoff <br>
<img src="../../../../../img/free_space_on_ubuntu/free_space_03.JPG" width="80%"><br>

對 linux-swap 右鍵點選 delete <br>
<img src="../../../../../img/free_space_on_ubuntu/free_space_04.JPG" width="80%"><br>

在 /dev/sda2 (黃色區塊）右鍵點選 Resize/Move <br>
<img src="../../../../../img/free_space_on_ubuntu/free_space_05.JPG" width="80%"><br>

在 New Size(MiB) 欄位輸入要改成多大的空間 <br>
每個人系統能夠使用的空間不同，這裡僅供參考。 <br>
在此以 76 GB 為例，也就是 `76*1024 = 77824` 建議所輸入的大小以 1024 MiB 為單位 <br>
切記要預留空間給原先的 linux-swap 使用，個人會留 3~4 GB 的大小 <br>
完成後點選 Resize <br>
<img src="../../../../../img/free_space_on_ubuntu/free_space_06.JPG" width="70%"><br>

之後在 unallocated 點選右鍵 New 來新增分割配置 <br>
<img src="../../../../../img/free_space_on_ubuntu/free_space_07.JPG" width="80%"><br>

預設會幫你填入剩下所要分配的空間大小 <br>
切記 File System 選擇 linux-swap 在點選 add <br>
<img src="../../../../../img/free_space_on_ubuntu/free_space_08.JPG" width="70%"><br>

查看下面的 information，確認是否前述步驟都有確實完成，<br>
一旦分割不好容易發生不可復原的情況 <br>
最後在點選上面的綠色勾勾(Apply All Opeation) 即可完成硬碟分割<br>
<img src="../../../../../img/free_space_on_ubuntu/free_space_09.JPG" width="80%"><br>

## Reference
* [7 Simple Ways To Free Up Space On Ubuntu and Linux Mint](https://itsfoss.com/free-up-space-ubuntu-linux/)
* [APT 的 clean 與 autoclean 差異](https://blog.longwin.com.tw/2012/05/apt-clean-autoclean-diff-2012/)
* [使用 systemd 提供的 journalctl 日誌管理](http://linux.vbird.org/linux_basic/0570syslog.php#whatis_syslog_new)
* [Systemd Journal](https://wiki.archlinux.org/index.php/Systemd/Journal)
* [systemd-journald.service](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html)
* [journalctl 使用筆記](https://babygoat.github.io/2019/04/25/journalctl%E4%BD%BF%E7%94%A8%E7%AD%86%E8%A8%98/)