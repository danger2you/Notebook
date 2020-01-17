# Docker部署Redis
1. 部署命令
```
docker run -p 6379:6379 -v /data/redis/data:/data -it --name redis-test --restart=always  -d redis redis-server --appendonly yes --requirepass "123456"

```

2. 部署参数