# Oracle语句
## 1. 创建数据表空间
```
create tablespace cinrad datafile '/share/database/oracle/cinrad01.dbf' size 5G autoextend on  next 100M  maxsize 30G;  
// size 5G， 文件初始大小5G
// autoextend on  next 100M   开启自动扩展，每次增加100M
// maxsize 30G  文件最大大小30G
```

## 2. 将表空间文件添加到该表空间
```
alter tablespace cinrad add datafile '/share/database/oracle/cinrad02.dbf' size 5G autoextend on next 100M maxsize 30G;
```

## 3. 查看是否开启归档模式
```
select name, log_mode from v$database;

// LOG_MODE ：NOARCHIVELOG  未开启归档模式
```

## 4. 查看表空间大小以及使用情况 
```
SELECT a.tablespace_name "表空间名",
a.bytes /1024/1024"表空间大小(M)",
(a.bytes - b.bytes) /1024/1024"已使用空间(M)",
b.bytes /1024/1024"空闲空间(M)",
round(((a.bytes - b.bytes) / a.bytes) *100,2) "使用比"
FROM ( SELECT tablespace_name,sum(bytes) bytes
FROM dba_data_files
GROUP BY tablespace_name) a,
(SELECT tablespace_name,sum(bytes) bytes,max(bytes) largest
FROM dba_free_space
GROUP BY tablespace_name) b
WHERE a.tablespace_name = b.tablespace_name
ORDER BY ((a.bytes - b.bytes) / a.bytes) DESC
```

## 5. 查看表空间数据文件信息
```
SELECT file_name,
tablespace_name,
bytes /1024/1024"bytes MB",
maxbytes /1024/1024"maxbytes MB"
FROM dba_data_files
```

## 6. 查看当前pool/cache使用情况 
```
SELECT component, current_size, min_size FROM v$sga_dynamic_components;
```

## 7. 查看Oracle内存情况 
```
 show parameter mem;
```

## 8. 修改Oracle内存设置
```
alter system set memory_max_target=16384M scope=spfile;
alter system set memory_target=16384M scope=spfile;
// 此处内存大小一般为物理内存的一半，若导入数据等大批量写入数据操作，则暂时改为80%
```

## 9. 删除表空间及文件
```
drop tablespace CINRAD_DATA including contents and datafiles cascade constraint;
// CINRAD_DATA  表空间名称
```


## 10.创建用户并指定表空间
```
       create user file_username identified by 12345678    -- ‘file_username’ 为创建的用户名  ；‘12345678’为创建的新用户登录密码
       default tablespace file_data  
       temporary tablespace file_temp;
```

## 11.给用户授权
```
SQL > grant all privileges to 用户名;
// 赋予该用户所有权限 

SQL > grant connect,resource,dba to 用户名;
// 赋予用户连接、资源、DBA权限
```
## 12. 关闭数据库
```
shutdown immediate 
```

## 13. 启动数据库
```
startup 
```

## 14. 删除用户及用户下所有数据
```
drop user user_name cascade; 
```

## 15. 查看死锁
一般表现为：ORA-00054: 资源正忙，要求指定 NOWAIT
```
SELECT sid, serial#, username, osuser FROM v$session where sid = (select session_id from v$locked_object);
```
## 16. 杀死死锁的会话
```
ALTER SYSTEM KILL SESSION '{sid},{serial#}';
示例：
ALTER SYSTEM KILL SESSION '142,38';
```
