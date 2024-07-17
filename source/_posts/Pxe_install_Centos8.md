---
title: Pxe 安装 Centos8
date: 2024-07-17 09:29:37
---

dhcp 配置
```bash
ption space pxelinux;
option pxelinux.magic code 208 = string;
option pxelinux.configfile code 209 = text;
option pxelinux.pathprefix code 210 = text;
option pxelinux.reboottime code 211 = unsigned integer 32;
option architecture-type code 93 = unsigned integer 16;

subnet 10.0.82.0 netmask 255.255.255.0 {
    range 10.0.82.160 10.0.82.162;
    class "pxeclients" {
        match if substring (option vendor-class-identifier, 0, 9) = "PXEClient";
        next-server 10.0.82.158;
        if option architecture-type = 00:07 {
            filename "uefi/shim.efi";
            } else {
            filename "/pxelinux.0";
        }
    }
    option routers 10.0.82.254;
    next-server 10.0.82.158;
    filename "pxelinux.0";
}
```

安装 tftp nginx 挂载 iso 镜像 提供http 服务
```bash
cp /usr/share/nginx/html/centos8/isolinux/libcom32.c32  /var/lib/tftpboot/
cp /usr/share/nginx/html/centos8/isolinux/libutil.c32  /var/lib/tftpboot/
cp /usr/share/nginx/html/centos8/isolinux/ldlinux.c32  /var/lib/tftpboot/
cp /usr/share/nginx/html/centos8/isolinux/vesamenu.c32 /var/lib/tftpboot/menu.c32
cp /usr/share/nginx/html/centos8/isolinux/initrd.img  /var/lib/tftpboot/
cp /usr/share/nginx/html/centos8/isolinux/vmlinuz  /var/lib/tftpboot/
yum -y install syslinux
cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/
```

nginx配置跟路径
```bash
/usr/share/nginx/html/centos8/
mount /opt/CentOS-8.3.2011-x86_64-dvd1.iso  /usr/share/nginx/html/centos8/
```

编辑pxe配置文件d vi /var/lib/tftpboot/pxelinux.cfg/default
```bash
default menu.c32
prompt 0
timeout 300
ONTIMEOUT local
menu title ########## PXE Boot Menu ##########

label 1
menu label ^1) Install CentOS 8 x64 with HTTP
kernel vmlinuz
append initrd=initrd.img method=http://10.0.82.158/centos8 devfs=nomount
```

参考文档
```bash
https://www.server-world.info/en/note?os=CentOS_8&p=pxe&f=4
https://docs.centos.org/en-US/8-docs/advanced-install/assembly_preparing-for-a-network-install/
```
