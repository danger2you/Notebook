# Docker部署SFTP
1. 部署命令
```
docker run --name htdf-sftp -p 8022:22 --restart=always -v /mnt/data/sftp/test:/home/htdf/test -d atmoz/sftp htdf:htdf123456:::test  
```
2. 部署参数
* --name htdf-sftp　　容器名称
* htdf:htdf123456:::upload　　其中htdf为用户名，htdf123456为密码，upload为上传的文件会保存到容器里面的/home/htdf/upload目录里面
* -p 8022:22　　将宿主机的22端口映射到容器的22端口，这样方位宿主机的22端口则会转发到容器的22端口上
* -d atmoz/sftp　　使用dockup hub中的atmoz/sftp镜像创建容器