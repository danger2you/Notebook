# Linux安装OracleJdk


## 1. 下载软件包
```bash
wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u141-b15/336fa29ff2bb4ef291e347e091f7f4a7/jdk-8u141-linux-x64.tar.gz"
```

## 2. 解压并放入/usr/local
```bash
tar -xzvf jdk-8u141-linux-x64.tar.gz
sudo mv jdk1.8.0_141/ /usr/local/jdk8
```

## 3.添加到环境变量
修改文件/etc/profile ，并在最后一行添加 
```bash
export JAVA_HOME=/usr/local/jdk8
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
```