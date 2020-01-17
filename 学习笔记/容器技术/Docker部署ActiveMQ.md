# Docker部署ActiveMQ
1. 部署命令
```
docker run -d --name activemq-test -p 61616:61616 -p 9161:8161 webcenter/activemq:5.11.1
```
2. 部署说明
61616为数据传输端口， 9161为fileserver端口，
想要修改账号密码，则使用命令 docker exec -it activemq-test /bin/bash 登录之后，修改conf/jetty-realm.xml文件
htdf: htdf_2019, admin