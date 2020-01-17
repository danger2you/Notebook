# Docker部署Influxdb
1. 部署命令
```
docker run -d --name influxdb -p 8086:8086 -v $basedir/influxdb:/var/lib/influxdb influxdb
```
2. 部署说明