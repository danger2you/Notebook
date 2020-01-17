# Docker部署RabbitMQ
1. 部署命令
```
docker run -d --name rabbit-test --restart=always -e RABBITMQ_DEFAULT_USER=htdf -e RABBITMQ_DEFAULT_PASS=htdf123456 -p 15672:15672 -p 5672:5672 -p 25672:25672 -p 61613:61613 -p 1883:1883 rabbitmq:management 
```

2. 部署参数
* --name： 容器名称
* -e RABBITMQ_DEFAULT_USER=htdf 指定参数 默认用户为htdf
* -e RABBITMQ_DEFAULT_PASS=htdf123456 指定参数 默认用户密码htdf123456