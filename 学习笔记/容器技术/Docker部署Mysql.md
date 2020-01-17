# Docker部署Mysql
1. 部署命令
```
docker run -p 9306:3306 --name cma-mysql -v /data/program/mysql/conf:/etc/mysql/conf.d -v /data/program/mysql/logs:/logs -v /data/program/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=htdf2019 --restart=always -d mysql:5.7.27 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci --lower_case_table_names=1
```
2. 部署参数
