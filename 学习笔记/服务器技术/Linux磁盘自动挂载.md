# Linux磁盘自动挂载

## 方案一：修改文件
### 文件说明
1. 文件路径： /etc/fstab
2. 文件内容：
```
#
# /etc/fstab
# Created by anaconda on Mon Nov 27 22:04:35 2017
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=6f15c206-f516-4ee8-a4b7-89ad880647db /                       xfs     defaults        0 0
```
3. 文件说明：
可以很明显的看到文件有6列。
* 第1列是设备名或者卷标
* 第2列是挂载点（也就是挂载目录）
* 第3列是所要挂载设备的文件系统或者文件系统类型
* 第4列是挂载选项，通常使用defaults就可以
* 第5列设置是否使用dump备份，置0为不备份，置1，2为备份，但2的备份重要性比1小
* 第6列设置是否开机的时候使用fsck检验所挂载的磁盘，置0为不检验，置1，2为检验，但置2盘比置1的盘晚检验

### 文件修改
文件末尾添加行：
```
/dev/data_vg/data	/mnt	xfs	defaults	0	0
```


## 方案二：添加开机启动

1. 修改/etc/rc.d/rc.local文件
末尾添加行
```
sudo mount /dev/data_vg/data /mnt
```