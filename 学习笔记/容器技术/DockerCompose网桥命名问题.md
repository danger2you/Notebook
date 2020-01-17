# DockerCompose网桥命名问题
docker-compose关于网络名的定义：
> 实际使用的网络是：<当前路径名_default>，如果<当前路径名>太长，会截取前缀部分.
> 例如当然目录名为testdocker，那么最终生产的网络名为：testdocker_default

**替换前缀名：**
当然使用路径名作为网络名是很不友好的，docker-compose使用环境变量来取代路径名。

解决方法：设置环境变量：COMPOSE_PROJECT_NAME
```
COMPOSE_PROJECT_NAME=aaa
```


**结论：**
docker-compose的网络名的规则是：&#60;COMPOSE_PROJECT_NAME&#62;_&#60;NETWORKS&#62;
1. 如果NETWORKS没有定义，那么就是default。
2. 如果COMPOSE_PROJECT_NAME，那么就是当前路径名(取前缀)。

