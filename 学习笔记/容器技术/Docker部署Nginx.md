# Docker部署Nginx
* 软件版本：最新
* 软件部署：Docker容器内
* 部署命令：
```
docker run -d -p 80:80 --restart=always --name cma-nginx-observation  -v /data/program/nginx/observation/html:/usr/share/nginx/html           -v /data/program/nginx/observation/conf:/etc/nginx -v /data/program/nginx/observation/logs:/var/log/nginx nginx
```
 
* 相关目录：
    * 配置文件：/data/program/nginx/observation/conf
    * 网站目录：/data/program/nginx/observation/html
    * 日志目录：/data/program/nginx/observation/logs
* 占用端口： 80
* 服务重启：
```
docker restart cma-nginx-observation
```

* 注意： 需要将Docker容器内 /etc/nginx 里的内容复制至物理机的 /data/program/nginx/observation/conf 目录下
```bash
docker cp [容器名]:/etc/nginx /data/program/nginx/observation/conf
```