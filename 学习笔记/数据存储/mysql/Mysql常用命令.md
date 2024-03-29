# Mysql常用命令

## 1. 创建用户
* CREATE USER 'username'@'host' IDENTIFIED BY 'password';
* 说明:username - 你将创建的用户名, host - 指定该用户在哪个主机上可以登陆,如果是本地用户可用localhost, 如果想让该用户可以从任意远程主机登陆,可以使用通配符%. password - 该用户的登陆密码,密码可以为空,如果为空则该用户可以不需要密码登陆服务器. 
```sql
CREATE USER 'owncloud'@'%' IDENTIFIED BY 'owncloud';
```
## 2. 给用户赋予权限
* GRANT privileges ON databasename.tablename TO 'username'@'host' 
* 说明: privileges - 用户的操作权限,如SELECT , INSERT , UPDATE 等(详细列表见该文最后面).如果要授予所的权限则使用ALL.;databasename - 数据库名,tablename-表名,如果要授予该用户对所有数据库和表的相应操作权限则可用*表示, 如*.*. 
```sql
GRANT ALL PRIVILEGES ON *.* TO 'owncloud'@'%';
```

## 3. 刷新权限 
```sql
FLUSH PRIVILEGES;
```

## 4. 删除用户
* DROP USER 'username'@'host';
```sql
DROP USER 'owncloud'@'%'
```

## 5. 修改用户密码
```sql
 ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';
```
## 6. 允许其它机器访问 
```sql
GRANT ALL PRIVILEGES ON 数据库名.* TO root@"%" IDENTIFIED BY "123456";
# 123456是密码
```