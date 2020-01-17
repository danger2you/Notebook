# Vsftpd虚拟用户配置
vsftpd 虚拟用户的作用是 通过不同的虚拟用户可以有不同的根目录。

从 2.3.5 版本之后，vsftpd增强了安全检查，如果用户被限定在了其主目录下，则该用户的主目录不能在具有写权限了，如果检查发现还有写权限，就会报该错误！
```
500 OOPS: vsftpd: refusing to run with writable root inside chroot ()
```

因此，本次分别用 Centos 6 和 Centos 7 来分别配置 vsftpd 虚拟用户

## 1. Centos6 vsftpd-虚拟用户配置
\----------------------------------------------------
系统版本：CentOS release 6.7
vsftpd版本：vsftpd-2.2.2-24
虚拟用户：hkey
虚拟用户根目录：/data/hkey
\----------------------------------------------------

```
# yum install vsftpd -y
# mkdir -pv  /data/hkey
# sed -i -e 's/^anonymous_enable=YES/anonymous_enable=NO/g' /etc/vsftpd/vsftpd.conf
# sed -i -e 's/^write_enable=YES/#write_enable=YES/g' /etc/vsftpd/vsftpd.conf
# sed -i -e 's/^#chroot_local_user=YES/chroot_local_user=YES/g' /etc/vsftpd/vsftpd.conf
# sed -i -e 's/^pam_service_name=vsftpd/#pam_service_name=vsftpd/g' /etc/vsftpd/vsftpd.conf
# sed -i -e 's/listen=NO/listen=YES/g' /etc/vsftpd/vsftpd.conf
# sed -i -e 's/listen_ipv6=YES/#listen_ipv6=YES/g' /etc/vsftpd/vsftpd.conf
# echo -e 'pam_service_name=vsftpd.vu
virtual_use_local_privs=NO
guest_enable=YES
guest_username=root
user_config_dir=/etc/vsftpd/vconf' >> /etc/vsftpd/vsftpd.conf
```

```
# egrep -v '^#|^$' /etc/vsftpd/vsftpd.conf
#关闭匿名访问
anonymous_enable=NO
#启用本地系统用户，包括虚拟用户
local_enable=YES
#本地用户umask值
local_umask=022
dirmessage_enable=YES
#启用日志
xferlog_enable=YES
xferlog_std_format=YES
#关闭ftp-data端口，相当于不使用主动模式
connect_from_port_20=YES
#限制用户不能离开FTP主目录，启用并设置例外用户清单
chroot_local_user=YES
#使用ipv4进行监听
listen=YES
#启用全局用户例外清单
userlist_enable=YES
#启用tcp封装
tcp_wrappers=YES
#pam认证文件名称，位于/etc/pam.d/
pam_service_name=vsftpd.vu
#虚拟用户权限是否与本地用户相同。为NO时，将与匿名用户的权限相同，在每个虚拟用户配置文件里设置匿名用户的选项等于虚拟用户的权限
virtual_use_local_privs=NO
#启用guest后，所有非匿名用户将映射到guest_username进行访问，包括本地系统用户也不能使用，并且转换成一个虚拟用户，与其他虚拟用户的配置方法一样
guest_enable=YES
# 虚拟用户使用的主用户（这里使用 root是为了比较方便配置各种目录）
guest_username=root
#虚拟用户配置文件目录
user_config_dir=/etc/vsftpd/vconf
```

```
# mkdir /etc/vsftpd/vconf/
 
# 一行用户名，一行密码，严格遵守这样的格式
# cat /etc/vsftpd/vuser.txt
hkey
hkey123
 
# 生成数据库
# db_load -T -t hash  -f /etc/vsftpd/vuser.txt /etc/vsftpd/vuser.db
 
# 添加认证文件
# cat /etc/pam.d/vsftpd.vu
auth required /lib64/security/pam_userdb.so db=/etc/vsftpd/vuser
account required /lib64/security/pam_userdb.so db=/etc/vsftpd/vuser
```

【开放所有权限】

```
# cat /etc/vsftpd/vconf/hkey  
 
local_root=/data/hkey # 设置登录后禁锢的根目录
write_enable=yes # 开发写权限
anon_world_readable_only=no # 开放下载权限
anon_upload_enable=yes # 开放上传权限
anon_mkdir_write_enable=yes # 开放创建目录的权限
anon_other_write_enable=yes # 开放删除和重命名的权限
```

【只开启上传下载权限】

```
# cat /etc/vsftpd/vconf/hkey
local_root=/data/hkey
write_enable=YES
anon_world_readable_only=no
anon_upload_enable=yes
```

## 2. Centos7 vsftpd-虚拟用户配置
\----------------------------------------------------
系统版本：CentOS release 7.0
vsftpd版本：vsftpd-3.0.2-22
虚拟用户：hkey
虚拟用户根目录：/data/hkey
\----------------------------------------------------

```
# yum install vsftpd -y
# mkdir -pv  /data/hkey
# sed -i -e 's/^anonymous_enable=YES/anonymous_enable=NO/g' /etc/vsftpd/vsftpd.conf
# sed -i -e 's/^write_enable=YES/#write_enable=YES/g' /etc/vsftpd/vsftpd.conf
# sed -i -e 's/^#chroot_local_user=YES/chroot_local_user=YES/g' /etc/vsftpd/vsftpd.conf
# sed -i -e 's/^pam_service_name=vsftpd/#pam_service_name=vsftpd/g' /etc/vsftpd/vsftpd.conf
# sed -i -e 's/listen=NO/listen=YES/g' /etc/vsftpd/vsftpd.conf
# sed -i -e 's/listen_ipv6=YES/#listen_ipv6=YES/g' /etc/vsftpd/vsftpd.conf
# echo -e 'pam_service_name=vsftpd.vu
virtual_use_local_privs=NO
guest_enable=YES
guest_username=vuser
user_config_dir=/etc/vsftpd/vconf
allow_writeable_chroot=YES' >> /etc/vsftpd/vsftpd.conf
```


```
# mkdir /etc/vsftpd/vconf/
 
# 一行用户名，一行密码，严格遵守这样的格式
# cat /etc/vsftpd/vuser.txt
hkey
hkey123
 
# 生成数据库
# db_load -T -t hash  -f /etc/vsftpd/vuser.txt /etc/vsftpd/vuser.db
 
# 添加认证文件
# cat /etc/pam.d/vsftpd.vu
auth required /lib64/security/pam_userdb.so db=/etc/vsftpd/vuser
account required /lib64/security/pam_userdb.so db=/etc/vsftpd/vuser
```


【开放所有权限】

```
# cat /etc/vsftpd/vconf/hkey  
 
local_root=/data/hkey # 设置登录后禁锢的根目录
write_enable=yes # 开发写权限
anon_world_readable_only=no # 开放下载权限
anon_upload_enable=yes # 开放上传权限
anon_mkdir_write_enable=yes # 开放创建目录的权限
anon_other_write_enable=yes # 开放删除和重命名的权限
```

【只开启上传下载权限】

```
# cat /etc/vsftpd/vconf/hkey <br>
local_root=/data/hkey
write_enable=YES
anon_world_readable_only=no
anon_upload_enable=yes
```