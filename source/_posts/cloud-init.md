---
title: 使用cloud-init初始化云主机密码
date: 2024-10-22 16:29:52
tags: 云计算
---

### 一句话简述过程：生成随机密码文件（user-data），利用genisoimage合成iso文件，把iso作为cdrom挂载到虚拟机（修改虚拟机配置文件）
- 在基座镜像里面安装yum install cloud-init && systemctl enable cloud-init 
- cloud-init clean 清除cloud-init 信息，不然的话只初始化一次。
- 创建两个配置文件 user-data(用户配置，初始化密码，安装一些定义服务)，meta-data (配置一些系统信息，hostname等等)
- user-data 内容
```bash
chpasswd:
  list: |
    root:e5GlYZ
  expire: False
ssh_pwauth: True
```

- meta-data 没有需求可以是一个空文件

- 生成所需要的iso文件，挂载到虚拟机
```bash
genisoimage -output seed.iso -volid cidata -joliet -rock user-data meta-data
```

- 在kvm配置文件中添加生成的iso文件
```bash
    <disk type='file' device='cdrom'>
      <driver name='qemu' type='raw'/>
      <source file='/tmp/seed.iso'/>
      <target dev='sda' bus='sata'/>
      <readonly/>
   <address type='drive' controller='1' bus='0' target='0' unit='0'/>
    </disk>
```

- 私有云平台的话，可以把iso文件上传ceph, 让虚拟机访问(修改source)
