---
title: 如何通过代理让Docker拉取国外镜像
date: 2025-09-18 17:22:22
tags:
---
### 第一步：建立 SSH SOCKS5 代理隧道

```bash 
ssh -f -N -D 1080 your_username@your-server-ip
```
-f：后台运行
-N：不执行远程命令
-D 1080：本地开启 SOCKS5 代理

验证:
```bash
curl --socks5-hostname 127.0.0.1:1080 https://ifconfig.me
```
#### 应返回 xxx.xxx.xxx.xxx

### 第二步：安装并配置 privoxy（SOCKS5 → HTTP 转换）
```bash
sudo dnf install privoxy -y
```

配置 /etc/privoxy/config
```bash
sudo vim /etc/privoxy/config
```
修改以下两行：
```bash
listen-address  127.0.0.1:8118
forward-socks5t / 127.0.0.1:1080 . 
```

🔑 行尾的 . 不能少！这是 privoxy 语法。 

### 第三步：为 Docker daemon 配置 HTTP 代理
```bash
sudo mkdir -p /etc/systemd/system/docker.service.d
```

创建代理配置文件:
```bash
sudo tee /etc/systemd/system/docker.service.d/http-proxy.conf <<EOF
[Service]
Environment="HTTP_PROXY=http://127.0.0.1:8118"
Environment="HTTPS_PROXY=http://127.0.0.1:8118"
Environment="NO_PROXY=localhost,127.0.0.1,.local"
EOF
```

重载并重启 Docker:
```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### 第四步：解决 DNS 问题（关键！）
```bash
sudo vim /etc/docker/daemon.json

{
  "dns": ["8.8.8.8", "1.1.1.1"],
  "dns-opts": ["timeout:3", "attempts:3"]
}
```

重启 Docker：
```bash
sudo systemctl restart docker
```
### 第五步：终极测试
```bash
docker pull alpine
```

### 使用containerd
```
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
```
#### 修改 /etc/containerd/config.toml
```bash
[plugins."io.containerd.grpc.v1.cri".registry]
  [plugins."io.containerd.grpc.v1.cri".registry.configs]
    [plugins."io.containerd.grpc.v1.cri".registry.configs."registry.k8s.io".proxy]
      http_proxy = "http://127.0.0.1:8118"
      https_proxy = "http://127.0.0.1:8118"
      no_proxy = "localhost,127.0.0.1,0.0.0.0,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16"

    [plugins."io.containerd.grpc.v1.cri".registry.configs."k8s.gcr.io".proxy]
      http_proxy = "http://127.0.0.1:8118"
      https_proxy = "http://127.0.0.1:8118"
      no_proxy = "localhost,127.0.0.1,0.0.0.0,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16"

  [plugins."io.containerd.grpc.v1.cri".registry.mirrors]
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors."registry.k8s.io"]
      endpoint = ["https://registry.k8s.io"]
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors."k8s.gcr.io"]
      endpoint = ["https://k8s.gcr.io"]
```
#### 重启sudo systemctl restart containerd

拉取镜像测试
```bash
sudo crictl pull registry.k8s.io/pause:3.9
```
