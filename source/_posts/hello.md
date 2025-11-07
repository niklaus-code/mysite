---
title: Kvm虚拟机迁移问题
date: 2024-07-16 17:39:50
tags:
    - 云计算
---

centos6系统，使用centos7 xml模板模板后网卡不能启动
原来的虚机有额外的挂载导致不能正常启动，需要进入到修复模式后，编辑/etc/fstab注释掉挂载



如果fstab无法编辑
``` bash
mount -o remount,rw /
```


编辑此文件修正 mac && 网卡名称：
``` bash
/etc/udev/rules.d/70-persistent-ipoib.rules   
SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*",  
ATTR{address}=="c8:00:0a:00:5a:64", ATTR{type}=="1", KERNEL=="eth*", NAME="eth0"
```
