# Docker远程管理
1. 修改文件
* 文件路径  /usr/lib/systemd/system/docker.service
* 添加参数
```
   OPTIONS='
   ...
   -H tcp://0.0.0.0:2375
   ...'
```
2. 重载服务
```
systemctl daemon-reload
systemctl restart docker
```