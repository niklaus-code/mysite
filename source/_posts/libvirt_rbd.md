---
title: Libvirt 通过key访问 Rbd
date: 2024-07-17 09:24:55
---

来源http://docs.ceph.org.cn/rbd/libvirt/ 定义 secret
``` bash
cat > secret.xml <<EOF
<secret ephemeral='no' private='no'>
        <usage type='ceph'>
                <name>client.libvirt secret</name>
        </usage>
</secret>
EOF
```

```bash
sudo virsh secret-define --file secret.xml
```

获取 client.libvirt 密钥并把字符串保存于文件
```bash
ceph auth get-key client.libvirt | sudo tee client.libvirt.key
```

设置 secret 的 UUID
```bash
virsh secret-set-value --secret {uuid of secret} --base64 $(cat client.libvirt.key)  
rm client.libvirt.key secret.xml
```

编辑镜像xml
```bash
<auth username='libvirt'>  
<secret type='ceph' uuid='9ec59067-fdbc-a6c0-03ff-df165c0587b8'/>
</auth>
```
多台服务器使用同一secret，非常重要的一步 把此密钥文件 /etc/libvirt/secrets/uuid.xml && uud.base64 拷贝到其它机器，就可以用同一secret访问ceph rbd了 密钥拷呗过去后不要忘记重启 libvirtd
查看libvirt secret
```bash
virsh secret-list
yum install libvirt qemu-kvm
systemctl start libvirtd
systemctl enable libvirtd
```
