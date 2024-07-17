---
title: 碎碎念
date: 2024-07-17 10:18:24
---

```bash
发现一个podman bug， 有时候停掉podman 容器后，查看映射的端口也没有了监听，但是端口不通，telnet不通。ps -a 查看所有容器，把历史容器删除掉之后，端口恢复正常
```

```bash
为什么Linux空目录是4k大小
Linux为了提高读写磁盘的效率，将磁盘分割为一个个4k的块，然后以块为单位读写。
操作系统会在inode中设置文件数据保存的块的索引，读取的时候再以块为单位读取到内存

所以一个文件大小的单位都可以被4k整除，因为操作系统会按磁盘块来进行申请空间

为什么空的普通文件大小为0k而空目录文件大小为4k ？
因为一个新创建的目录下会保存.和..这两个目录, 所以任何一个新的目录初始的大小一定是4k开始
```

```bash
更新零值 不能用struct 得用map ， 而且不能用指针
	u := map[string]interface{}{
		"is_active": state,
	}

	err = _db.Table("auth_user").Debug().Where("id=?", userid).Update(u).Error
	if err != nil {
		return err
	}
```

```bash
tcpdump -i wg0 port 8000 -XX -vvv -nn #tcpdump 抓包
```

```bash
mysql的binlog是按事务的提交(commit)记录的 回滚不记录，查不记录，只记录增删改
```

```bash
windows 可以同时两个程序同时监听一个端口
```

```bash
连接kafka需要配置本地host, kafka server 映射， 如果有集群， 集群slave的 hostname 也需要配置
```

```bash
moosefs 恢复误删除数据 find . -iname “cover” -type f -exec mv {} undel/ ;
```

```bash
keepalived VRRP协议 防火墙开放：-A INPUT -p vrrp -j ACCEPT
```

```bash
go 代理
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.io,direct
或者
go env -w GOPROXY=https://goproxy.cn,direct
```

```bash
ssh 隧道 ssh -N -f -L 3306:localhost:3306 $server_ip
```
