# Docker容器默认存储位置更改

## 新版本
### 方案一
1. 添加并配置文件：/etc/docker/daemon.json
```bash
{
    "data-root": "/data/docker"
}
```
2. 重启Docker
```bash
systemctl restart docker
```

### 方案二
1. 编辑文件 /usr/lib/systemd/system/docker.service
```bash
ExecStart=/usr/bin/dockerd
    ....
    --data-root=/data/docker
    ...
```

注意：目前网上大多数配置参数是  --graph ，其实这是老版本中的使用方法，新版本已经抛弃，改用了 --data-root ，具体可以通过命令 dockerd --help 查看使用的参数
2. 重启Docker
```bash
systemctl daemon-reload
systemctl restart docker
```

## 旧版本
### 1. 修改docker.service文件
* 文件位置：
  * /etc/systemd/system/multi-user.target.wants/docker.service 
  * /usr/lib/systemd/system/docker.service
注：任取其一，两个文件是关联的，其中一个是软连接
* 添加或修改文件内容
```
ExecStart=/usr/bin/dockerd
    ....
    --graph=/data/docker --storage-driver=overlay
    ...
```
注：
* --graph=/data/docker：docker新的存储位置
* --storage-driver=overlay ： 当前docker所使用的存储驱动

### 2. 重启Docker
```
systemctl daemon-reload
systemctl restart docker
```


