# Docker部署Yapi-API文档管理工具
## 1. 部署Mongodb
> 详情参照文档：Docker部署Mongodb

## 2. 部署Yapi
1. 获取Yapi纯净版本镜像
```bash
docker pull registry.cn-hangzhou.aliyuncs.com/anoy/yapi
```

2. Yapi配置文件
* 作用：用于Yapi初始化
* 命令：
```bash
mkdir -p /data/docker-data/yapi/config
vim /data/docker-data/yapi/config/config.json
```
* 内容：
```json
{
  "port": "9003",
  "adminAccount": "1524851775@qq.com",
  "db": {
    "servername": "mongodb",
    "DATABASE": "yapi",
    "port": 27017,
    "user": "admin",
    "pass": "jutao110",
    "authSource": "yapi"
  },
  "mail": {
    "enable": true,
    "host": "smtp.qq.com",
    "port": 465,
    "from": "1524851775@qq.com",
    "auth": {
      "user": "1524851775@qq.com",
      "pass": "gnrsztsyzqbrfhii"
    }
  }
}
```

3. 初始化Yapi数据库索引及管理员账号
```bash
docker run -it --rm \
  -v /data/docker-data/yapi/config/config.json:/api/config.json \
  --link mongodb:mongo \
  --entrypoint npm \
  --workdir /api/vendors \
  registry.cn-hangzhou.aliyuncs.com/anoy/yapi \
  run install-server
```

4. 启动 Yapi 服务
```bash
  docker run -d \
      --name yapi \
      -v /data/docker-data/yapi/config/config.json:/api/config.json \
      --link mongodb:mongo \
      --workdir /api/vendors \
      -p 9003:9003 \
      registry.cn-hangzhou.aliyuncs.com/anoy/yapi \
      server/app.js
```

## 3. 访问Yapi
浏览器输入地址  ip:9003


## 4. Yapi升级
* 停止并删除旧版本
```bash
docker rm -f yapi
```

* 获取最新镜像
```bash
docker pull registry.cn-hangzhou.aliyuncs.com/anoy/yapi
```

* 启动新容器
```bash
  docker run -d \
      --name yapi \
      -v /data/docker-data/yapi/config/config.json:/api/config.json \
      --link mongodb:mongo \
      --workdir /api/vendors \
      -p 9003:9003 \
      registry.cn-hangzhou.aliyuncs.com/anoy/yapi \
      server/app.js
```