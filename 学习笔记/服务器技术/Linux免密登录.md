# Linux免密登录

## 环境
* Node1： Centos7.4  
* Node2：Centos7.4

## 需求
> Node1 可以免密登录 Node2


## 解决方案
### 1. Node1生成公钥和私钥
使用ssh-keygen工具生成相应的公钥(id_rsa.pub)和私钥(id_rsa), 路径：~/.ssh/
```bash
ssh-keygen -t rsa -b 4096
```

### 2. 将公钥传输至Node2
使用scp 将公钥传输至Node2 ~/.ssh 目录下
```bash
scp .ssh/id_rsa.pub centos@defengweather_node2:/home/centos/.ssh/
```

### 3. Node2 将公钥添加至允许列表
```
cd ~/.ssh
cat id_rsa.pub >> authorized_keys
```

### 4. 测试Node1登录Node2
```bash
ssh centos@node2
```