---
title: "Free_space_on_ubuntu"
date: 2020-07-11T19:36:26+08:00
draft: true
categories: ["Linux"]
tags: ["Linux"]
---

# Free Space On Ubuntu


## Method 2: journalctl 指令

```sh
sophie@ubuntu:~$ journalctl --disk-usage
Archived and active journals take up 840.3M in the file system.
```


```sh
sophie@ubuntu:~$ sudo journalctl --vacuum-time=3d
Vacuuming done, freed 792.2M of archived journals from /var/log/journal/0c1a867975654e11ab0fa8ab9b00dc0f.
Vacuuming done, freed 0B of archived journals from /var/log/journal.
```

```sh
sophie@ubuntu:~$ journalctl --disk-usage
Archived and active journals take up 48.0M in the file system.
```

## Reference
* [7 Simple Ways To Free Up Space On Ubuntu and Linux Mint](https://itsfoss.com/free-up-space-ubuntu-linux/)