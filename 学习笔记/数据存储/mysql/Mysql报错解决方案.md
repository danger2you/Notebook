# Mysql报错解决方案

1. 版本8.x，登录报错：the server requested authentication method unknown to the client
* 原因：从8.0.11版本起，不再像mysql5.7及以前版本那样，设置用户密码时默认的验证方式为caching_sha2_password，如果发现升级mysql8.0.11后原有的程序不能连接mysql，可迅速在mysql command line client客户端用下面的命令设置成mysql5.7及以前版本的密码验证方式，同时MYSQL8.0.11下修改密码的方式与原先也不大一样，原先的部分修改密码的命令在mysql8.0.11下不能使用
* 解决方案：
    * 使用Mysql命令行执行命令
```mysql
> use mysql  
> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码';  
> FLUSH PRIVILEGES; 
```
    * 添加Mysql启用参数或my.cnf添加参数 
```
default_authentication_plugin=mysql_native_password
```