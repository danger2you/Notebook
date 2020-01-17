# 磁盘管理fdisk


## 1. 查看磁盘列表
```bash
sudo fdisk -l 
```

## 2. 创建分区
```bash
$ sudo fdisk  /dev/nvme1n1 

Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0xe72607ef.

Command (m for help):n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 
First sector (2048-2147483647, default 2048): 
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-2147483647, default 2147483647): 
Using default value 2147483647
Partition 1 of type Linux and of size 1024 GiB is set

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

## 3. 分区操作参数 
```bash
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   g   create a new empty GPT partition table
   G   create an IRIX (SGI) partition table
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)

```