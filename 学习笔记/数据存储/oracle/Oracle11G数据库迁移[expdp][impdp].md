# Oracle11G数据库迁移[expdp][impdp]
## 0x01 环境
A 机器，操作系统 CentOS7.3，Oracle版本：11G，IP地址：192.168.1.11
B 机器，操作系统 CentOS7.3，Oracle版本：11G，IP地址：192.168.1.12

将数据库从A机器迁移到B机器
A机器 Oracle 监听SID：orcl, 帐号：CINRAD，密码：***， 表空间名称：CINRAD_DATA,  表空间文件大小：共10个文件，每个文件初始大小10G，可自动增长，增长幅度100M，最大大小为30G，表空间中的数据约为120G左右

B机器 Oracle 监听SID：orcl

## 0x02 迁移
### 1.  使用dba登录A机器的Oracle数据库
```
sqlplus / as sysdba
```
### 2.  创建数据库导出目录 
目录可自定义。若系统中无该目录，则手动创建，并赋予777权限
```
SQL > create directory dumpdir as '/data/oracle/dump';
```
### 3.  赋予Oracle用户对于导出目录的读写权限 
```
SQL > grant write,read on directory dumpdir to CINRAD;
```
### 4.  使用expdp导出数据
进入Linux命令行，登录Oracle帐号，使用导出工具expdp对CINRAD用户下的所有数据导出【可使用并行参数paraller进行多文件同时导出】
单文件导出：
```
expdp CINRAD/cinrad@orcl directory=dumpdir dumpfile=cinrad.dump
```
多文件并行导出：
```
expdp CINRAD/cinrad@orcl directory=dumpdir dumpfile=cinrad_20180204_%U.dump filesize=500M paraller=5

// filesize=500M  单文件最大500M
// paraller = 5   并行数为5
```

这里，我导出的是单文件，下面，以单文件导出为例，继续进行。

### 5. 传输导出文件
 将A机器导到的dump文件传输到B机器/data/oracle/dump目录下
```
scp /data/oracle/dump/cinrad.dump root@192.168.1.12:/data/oracle/dump/cinrad.dump
scp /data/oracle/dump/export.log root@192.168.1.12:/data/oracle/dump/export.log
```
也可以将两个导出文件压缩后一起传输


### 6. 使用dba登录B机器的Oracle数据库
```
sqlplus / as sysdba 
```

### 7. 创建与A机器相同的数据表空间
```
create tablespace CINRAD_DATA datafile '/share/database/oracle/cinrad01.dbf' size 10G autoextend on next 100M  maxsize 30G;  
alter tablespace CINRAD_DATA add datafile '/share/database/oracle/cinrad02.dbf' size 10G autoextend on next 100M maxsize 30G;  
alter tablespace CINRAD_DATA add datafile '/share/database/oracle/cinrad03.dbf' size 10G autoextend on next 100M maxsize 30G;
alter tablespace CINRAD_DATA add datafile '/share/database/oracle/cinrad04.dbf' size 10G autoextend on next 100M maxsize 30G;
alter tablespace CINRAD_DATA add datafile '/share/database/oracle/cinrad05.dbf' size 10G autoextend on next 100M maxsize 30G;  
alter tablespace CINRAD_DATA add datafile '/share/database/oracle/cinrad06.dbf' size 10G autoextend on next 100M maxsize 30G;  
alter tablespace CINRAD_DATA add datafile '/share/database/oracle/cinrad07.dbf' size 10G autoextend on next 100M maxsize 30G;  
alter tablespace CINRAD_DATA add datafile '/share/database/oracle/cinrad08.dbf' size 10G autoextend on next 100M maxsize 30G;  
alter tablespace CINRAD_DATA add datafile '/share/database/oracle/cinrad09.dbf' size 10G autoextend on next 100M maxsize 30G;  
alter tablespace CINRAD_DATA add datafile '/share/database/oracle/cinrad10.dbf' size 10G autoextend on next 100M maxsize 30G; 
```
创建数据表空间，并向该表空间中添加数据文件


### 8. 创建用户，并指定默认表空间
创建与A机器相同的用户，表将表空间CINRAD_DATA指定给该用户
```
SQL > CREATE USER CINRAD IDENTIFIED BY cinrad DEFAULT TABLESPACE CINRAD_DATA

// IDENTIFIED BY cinrad     此cinrad 为密码，可更换
// DEFAULT TABLESPACE CINRAD_DATA  CINRAD_DATA 为指定的默认表空间
```


### 9.  授权
B机器的相应的Oracle用户创建完毕之后，需要给该用户相应的数据库操作权限，与A相同或者直接赋予所有权限 
```
SQL > grant all privileges to CINRAD
// CINRAD 为用户名
```

### 10. 创建导入目录 
在B机器上创建导入目录 ，并授予该目录777 权限 ，在Oracle中创建该目录 
```
SQL > create directory dumpdir as '/data/oracle/dump';
```
### 11. 授予Oracle用户导入目录读写权限 
```
SQL > grant write,read on directory dumpdir to CINRAD;
```

### 12. 使用impdp导入数据
进入Linux命令行，登录Oracle帐号，使用导出工具impdp对数据进行导入
```
impdp system/oracle@orcl directory=dumpdir dumpfile=cinrad.dump parallel=10 
```
此次导出从晚上22点开始，次日01:13:58结束，耗时过长，需要优化。 主要针对索引导入的效率， 导入日志见附件。
导入选项： 
* table_exists_action ： skip 是如果已存在表，则跳过并处理下一个对象；append是为表增加数据；truncate是截断表，然后为其增加新数据；replace是删除已存在表，重新建表并追加数据




### 13. 检查迁移数据

## 0x03 错误
若没有遇到报错，则可忽略，进行0x04的优化
### 1. 使用expdp/impdp工具时报错
#### 1.  Oracle软件与数据库不匹配错误：
问题描述： 
```
ORA-31626: job does not exist 
ORA-31637: cannot create job SYS_EXPORT_SCHEMA_01 for user SYSTEM 
ORA-06512: at "SYS.DBMS_SYS_ERROR", line 95 
ORA-00942:table or view does not exist
```
解决方案： 
*  使用DBA用户登录Oracle数据库
*  执行以下代码
```
SQL>@?/rdbms/admin/catalog.sql
SQL>@?/rdbms/admin/catproc.sql
``` 
* [后续此库出现问题：EXP-00056: ORACLE error 932 encountered] ， 可继续执行
```
SQL> @?/rdbms/admin/catmetx.sql 
SQL> @?/rdbms/admin/utlrp.sql
```
### 2. 重启Oracle数据库报错
#### 1. 数据库无法正常关闭，并锁定内存问题
问题描述：
 在数据库中执行shutdown immediate关闭数据库，报错： 
```
ORA-27102: out of memory
Linux Error: 12: Cannot allocate memory
```
 查看Linux中虚拟内存/dev/shm的占用情况 [命令：df -hT]，发现占用在90%以上，内存已经被锁定无法释放

解决方案: 
* 在Linux中执行 ps -ef | grep oracle ，查看oracle进行的pid
* 使用 kill -9 {pid}  的方式将Oracle进程杀死
* 使用DBA登录Oracle
* startup 启动数据库

### 3. 数据导入过程中报错
#### 1. DB_LINK创建失败，权限不足
问题描述： 导入过程中，导入DB_LINK时提示，权限不足
解决方案： 赋予该用户DBA的权限或所有权限即可
#### 2. DB_LINKE 创建失败，缺失关键字
问题描述：
```
失败的 sql 为:
CREATE DATABASE LINK "ASOM"  CONNECT TO "ASOM" IDENTIFIED BY VALUES '05535CD93437C21A745CE9EB530617B269'  AUTHENTICATED BY "ASOM" IDENTIFIED BY VALUES '0506C0E222927A938BF9F44DCB31C2038D'  USING '10.36.6.55:1521/orcl'
ORA-00905: 缺失关键字
```
解决方案：待解决，暂时导入完成后手动创建。
#### 3.  用户不存在
问题描述：
```
处理对象类型 SCHEMA_EXPORT/TABLE/GRANT/OWNER_GRANT/OBJECT_GRANT
ORA-39083: 对象类型 OBJECT_GRANT 创建失败, 出现错误:
ORA-01917: 用户或角色 'CINRAD_META' 不存在
失败的 sql 为:
GRANT SELECT ON "CINRAD"."STA_RADAR" TO "CINRAD_META"

ORA-39083: 对象类型 OBJECT_GRANT 创建失败, 出现错误:
ORA-01917: 用户或角色 'CINRAD_META' 不存在
失败的 sql 为:
GRANT SELECT ON "CINRAD"."TEST_STA_RADAR" TO "CINRAD_META"
```
解决方案： 若该用户下的数据里有你想要导入的数据，则创建该用户重新导入，若没有你想导入的数据，则忽略即可。
#### 4. 视图存在编译警告
问题描述： 
```
ORA-39082: 对象类型 VIEW:"CINRAD"."V_AWS_OBSERVE_DATA" 已创建, 但带有编译警告
ORA-39082: 对象类型 VIEW:"CINRAD"."V_SONDE_OBSERVE_DATA" 已创建, 但带有编译警告
ORA-39082: 对象类型 VIEW:"CINRAD"."V_FLASHLIGHT_OBSERV_DATA_HIS" 已创建, 但带有编译警告
ORA-39082: 对象类型 VIEW:"CINRAD"."V_STA_AWS_REG" 已创建, 但带有编译警告
ORA-39082: 对象类型 VIEW:"CINRAD"."V_AWS_OBSERVE_REG" 已创建, 但带有编译警告
ORA-39082: 对象类型 VIEW:"CINRAD"."V_EVA_THUNDER_EFF" 已创建, 但带有编译警告
ORA-39082: 对象类型 VIEW:"CINRAD"."V_EVA_THUNDER_OUTSIDE" 已创建, 但带有编译警告
ORA-39082: 对象类型 VIEW:"CINRAD"."V_EVA_THUNDER_UTILIZATION" 已创建, 但带有编译警告
ORA-39082: 对象类型 VIEW:"CINRAD"."V_EVA_THUNDER_PARTAKE" 已创建, 但带有编译警告
ORA-39082: 对象类型 VIEW:"CINRAD"."V_EVA_THUNDER_LOCATEMODE" 已创建, 但带有编译警告
ORA-39082: 对象类型 PACKAGE_BODY:"CINRAD"."GP_DISPATCHER" 已创建, 但带有编译警告
```
解决方案：使用PLSQL连接数据库，右击该视图，编辑，回车，即可解决。

## 0x04 优化
### 1. Oracle内存优化
假设服务器内存32G
#### 1. 查看用于Oracle的虚拟内存[/dev/shm]大小, 执行命令 df -hT
tmpfs                                    tmpfs      16G  2.1G  14G  14% /dev/shm
此内存一般为服务器物理内存的50%到80%， 若读写均占差不多的比重，则为50%【16G】左右，若写的比重更大，则适当增加虚拟内存的大小

#### 2. 使用DBA登录数据库，查看Oracle内设置的内存大小情况 
```
SQL >  show parameter mem;
```
查看memory_max_target 与 memory_target 的值， 该值必须小于等于服务器设置的虚拟内存【16G】的大小，并且大于SGA+PGA的内存大小。
若memory_max_target 与 memory_targety 设置过小，则需要调整。

#### 3.  修改Oracle的内存大小【memory_max_target与memory_targety 】
* 以DBA登录Oracle数据库
* 执行语句 ：SQL> shutdown immediate  关闭数据库
* 执行语句 ：SQL> create pfile from spfile;
* 编辑pfile文件: Linux 下执行 ， vim $ORACLE_HOME/dbs/initorcl.ora
  调整memory_max_target   memory_target, 两个值都相同
  调整 *.sga_target 的值 
  调整 *.pga_aggregate_target 的值
调整原则：/dev/shm虚拟内存 >=【memory_max_target/memory_target】>= sga_target + pga_aggregate_target 

若没有按照该原则调整，则会报以下错误 ： 
```
ORA-00838: Specified value of MEMORY_TARGET is too small, needs to be at least 4016M
```
```
ORA-00845: MEMORY_TARGET not supported on this system
```
* 执行语句：SQL> startup ，启动数据库

### 2. 关闭归档模式
1. 查看归档模式是否开启，执行语句 ： SQL>  select name, log_mode from v$database;   若返回为NOARCHIVELOG, 则归档模式已关闭。
2. 若归档模式开启，则执行语句：alter database noarchivelog;  若不生效，可重启数据库查看效果
### 3. 更改最大连接数
将最大连接数调整为2000
1. 查看processes和sessions参数  
SQL> show parameter processes
SQL> show parameter sessions
2. 修改processes
SQL> alter system set processes=2000 scope=spfile;
3. 修改sessions
SQL> alter system set sessions=2000 scope=spfile;
### 4. 关闭监听日志
修改文件 $ORACLE_HOME/network/admin/listener.ora
在文件顶部添加： LOGGING_LISTENER = OFF 
保存退出

重启lsnrctl:  执行命令
```
lsnrctl stop 
lsnrctl start
```





