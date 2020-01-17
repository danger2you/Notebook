# Docker部署PHP容器

一. Docker exec 
1. 部署php-fpm
```
docker run --name  cma-php -v /data/program/nginx/php/www:/www -d php:5.6-fpm
```

2. 部署nginx
```
docker run --name cma-php-nginx -p 80:80 -d \
    -v /data/program/nginx/php/www:/usr/share/nginx/html:ro \
    -v /data/program/nginx/php/conf/conf.d:/etc/nginx/conf.d:ro \
    --link cma-php:php \
    nginx
```


二. 扩展模块安装 
1. 进行Docker容器
```bash
docker exec -it cma-php /bin/bash
```

2. 执行安装扩展模块
docker-php-ext-install  [模块名称]
```
docker-php-ext-install pdo pdo_mysql
```