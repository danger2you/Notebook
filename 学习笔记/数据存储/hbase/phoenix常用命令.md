# phoenix常用命令

## 1. 登录
```
./sqlline.py localhost:2181
```

## 2. 退出
```
!quit
!exit
```

## 3. 创建表
```
create table if not exists ljc.student(id integer primary key,name varchar(20));
```

## 4. 查看所有表
```
!tables
```

## 5. 查看表结构
```
!describe 表名
```

## 6. 删除表
```
drop table 表名
```

## 7. 插入、更新
```
upsert into ljc.student(id,name) values(1,'zhangsan');
```