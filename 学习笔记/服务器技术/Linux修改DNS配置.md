# Linux修改DNS配置


## 方法一：修改/etc/resolve.conf
* 文件位置：/etc/resolv.conf
* 解决方案：文件末尾添加配置
```bash
nameserver 8.8.8.8
nameserver 114.114.114.114
```
