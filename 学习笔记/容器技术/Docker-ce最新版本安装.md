# Docker-ce最新版本安装 

## 1. 卸载旧版本
```bash
sudo yum remove docker \
           docker-common \
           docker-selinux \
           docker-engine
```

## 2. 安装环境
``` bash
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

## 3. 添加yum仓库
```bash
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

## 4. 安装docker-ce
```bash
 sudo yum install docker-ce
```

## 5. 设置开机启动
```bash
sudo systemctl enable docker
```