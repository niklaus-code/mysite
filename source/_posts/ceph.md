---
title: Ceph常用
date: 2024-07-17 09:33:35
tags:
    - ceph
---

osd kerying用cli创建
```bash
ceph auth get-or-create osd.222
ceph auth caps osd.222 mgr 'allow profile osd' mon 'allow profile osd' osd 'allow *
```

手动创建配置文件导入
```bash
[osd.222]
key = AQBm7CxhMVXjJxAA7aoY+cZcG9aK7qLoU4X2Rw==
caps mon = "allow profile osd"
caps mgr = "allow profile osd"
caps osd = "allow *"
```

删除osd
```bash
ceph osd out 36
ceph osd stop 36
ceph osd rm 36
ceph auth rm osd.36
ceph osd crush remove osd.36
```

手动创建osd
```bash
ceph-volume prepare /dev/sdb
ceph-volume lvm activate osd.id fsid ceph-volume lvm activate 2 08956dbe-0b6e-4577-8b00-ada197a05ac9
```
设置pool允许删除
```bash
ceph config set mon mon_allow_pool_delete true  
```

查看所有key
```bash
ceph config-key ls  
```

倒入镜像
```bash
qemu-img convert -p -f qcow2  -O raw ./0000_demo_winxp.qcow2 rbd:vm/0000_demo_winxp
```

倒出镜像
```bash
qemu-img convert -p -f raw -O qcow2 rbd:vm/9ed73e48-f451-4dae-a10d-912de83dd028  ./xx_.qcow2
```

映射块设备到dev并挂载
```bash
rbd map 083b9e72-7653-4615-a09a-98487529b938 --pool v_disk
rbd showmapped   
mount /dev/rbd0 /mnt/data  
rbd unmap pool/images  
```
podman 删除某个实例
```bash
cephadm ls #查看实例  
cephadm rm-daemon -n mds.cephfs-bigdata --fsid 86150a92-c060-11eb-bec5-bc97e1b56fd1
```

创建文件系统
```bash
ceph osd pool create cephfs_data 1024 1024 hdd_rule #创建pool 制定规则  
ceph osd pool create cephfs_metadata 1024 1024 hdd_rule  
ceph fs new cephfs cephfs_metadata cephfs_data  
ceph orch apply mds cephfs #创建mds  
```

挂载文件系统
```bash
mount -t ceph p2:6789,p5:6789:/ /mnt/cephfs/ -o fs=fs name=admin
```

cursh操作
```bash
提取CRUSH Map  
ceph osd getcrushmap -o /tmp/crush

反编译crush图  
crushtool -d /tmp/crush -o /tmp/decompiled_crush

反编译crush图  
crushtool -c /tmp/decompiled_crush -o /tmp/crush_new

注入   
ceph osd setcrushmap -i /tmp/crush_new
```

### resize ceph rbd 
```bash
rbd resize --image 5faa0840-ef5e-4cd7-a850-2f82878788d2 --size 500G --pool vm
virsh shutdown vm1
xfs_growfs /mnt

#lvm 扩展
parted /dev/vda
resizepart 2 100%
刷新分区表：
partprobe /dev/vda
重新调整物理卷
pvresize /dev/vda2
扩展逻辑卷
lvextend -l +100%FREE /dev/rl/root
扩展文件系统
对于 ext4 文件系统： resize2fs /dev/rl/root
对于 xfs 文件系统：xfs_growfs /
```

