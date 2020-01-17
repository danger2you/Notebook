# Docker镜像源修改
## 镜像源
1. Docker 官方中国区
```
https://registry.docker-cn.com
```
2. 网易
```
http://hub-mirror.c.163.com
```
3. USTC
```
https://docker.mirrors.ustc.edu.cn
```

## 修改方式 
* 直接添加 –registry-mirror 参数，仅对当前的命令有效
```
docker run hello-world --registry-mirror=https://docker.mirrors.ustc.edu.cn
```
* 修改 /etc/default/docker，加入 DOCKER_OPTS=”镜像地址”，可以有多个 
```
DOCKER_OPTS="--registry-mirror=https://docker.mirrors.ustc.edu.cn"
```
* 支持 systemctl 的系统，通过 sudo systemctl edit docker.service，会生成 etc/systemd/system/docker.service.d/override.conf 覆盖默认的参数，在该文件中加入如下内容：
```
[Service] 
ExecStart= 
ExecStart=/usr/bin/docker -d -H fd:// --registry-mirror=https://docker.mirrors.ustc.edu.cn
```
* 新版的 Docker 推荐使用 json 配置文件的方式，默认为 /etc/docker/daemon.json，非默认路径需要修改 dockerd 的 –config-file，在该文件中加入如下内容：
```
{ 
"registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"] 
}
```