---
title: Ceph日常故障记录
date: 2024-07-17 09:40:23
tags:
    - ceph
---

将XFS文件系统挂载到ECS实例时, 出现以下报错

```bash
mount: /mnt: wrong fs type, bad option, bad superblock on /dev/vdc1,  missing codepage or helper program, or other error.
```

问题原因:  
磁盘是通过快照生成的，因此UUID与已有的磁盘存在冲突
挂载时添加-o nouuid参数，暂时忽略检查UUID的操作。  
```bash
mount -t xfs -o nouuid /dev/vdc1 /mnt
```

部署节点（mon节点） 是centos8.3 , 增加加点的时候报错
```bash
ERROR: No time synchronization is active
chronyd.service 这个东西也是安装起了的  
systemctl enable chronyd  设置为开机启动 这个问题就好了
```

ceph mon节点挂掉 恢复（3个mon节点只剩下一个，无法正常服务）
参考文档
```bash
https://docs.ceph.com/en/latest/rados/troubleshooting/troubleshooting-mon/ 
https://www.cnblogs.com/yanling-coder/p/12160813.html
```

查看mon状态 
```bash
ceph mon getmap -o /tmp/monmap
mon不能正常服务（只剩一个mon)  
monmaptool --create --clobber --fsid 45b34caa-83b8-4c36-833b-544bba873456 --add control3 172.16.12.43:6789 --add control1 172.16.12.41:6789 --add control2 172.16.12.42:6789 /tmp/monmap
```

拷贝到新机器  
```bash
scp /tmp/monmap p75:/tmp/
```

注射monmap  
```bash
ceph-mon -i p75 --extract-monmap /tmp/monmap
拷贝keyring  
最后拷贝systemd脚本到目标机器，启动服务。  
操作系统要对应，原来mon使用centos8 新加入机器也要使用centos8 
```

ERROR: lvcreate binary does not appear to be installed
```bash
yum install lvm2
```

一个dhcp-server问题
```bash
Not configured to listen on any interfaces! 要配置 DHCPDARGS=eth1 绑定网卡 很重要的一点 网络环境要支持。也就是说网卡所在 网段 分配IP的网段，网络 gw啥的要正常，不然 dhcpserver 报这个错起不来
```
