# Centos7下Python3与Python2共存
1. 安装python3.5可能使用的依赖
```
yum install openssl-devel bzip2-devel expat-devel gdbm-devel readline-devel sqlite-devel
```
2. 下载python
```
wget "https://www.python.org/ftp/python/3.5.0/Python-3.5.0.tgz"
```
3. 解压下载好的压缩包
```
tar -zxvf Python-3.5.0.tgz
```
4. 配置编译
```
sudo mkdir /usr/local/python3
sudo Python-3.5.0/configure --prefix=/usr/local/python3
sudo make
sudo make install
```
5. 备份旧版本python，链接新版本python
在/usr/bin中有python、python2、python2.7三个文件依次指向后者
```
sudo mv python python.bak
sudo ln -s /usr/local/python3/bin/python3  /usr/bin/python
```
6. 修改yum配置文件
```
sudo vi /usr/bin/yum
将第一行指定的python版本改为python2.7(#!/usr/bin/python 改为 #!/usr/bin/python2.7)
```