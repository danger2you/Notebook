# Docker部署Mongodb

## 1. 创建数据目录 
```bash
mkdir -p /data/docker-data/mongodb/data
```

## 2. 运行容器
```bash
docker run -p 17018:27017 -v /data/docker-data/mongodb/data:/data/db --name mongodb --restart=always -d mongo --auth  --bind_ip=0.0.0.0
```

## 3. 创建超级管理员

*  进入容器并执行mongo相关命令
```bash
docker exec -it mongodb mongo admin;
```

* 使用集合
```bash
use admin;
```

* 创建用户并赋予权限
```bash
db.createUser({ user: 'admin', pwd: 'jutao110', roles: [ { role: "userAdminAnyDatabase", db: "admin" } ] });
```

## 4. 创建数据库及用户
* 使用超级管理员登录
```bash
docker exec -it mongodb mongo --port 27017 -u "admin" -p "jutao110" --authenticationDatabase "admin"
```

* 创建数据库
```bash
# use [库名]
use yapi
```

* 创建用户
```bash
db.createUser({ user: 'admin', pwd: 'jutao110', roles: [ { role: "readWrite", db: "admin" } ] });
```
