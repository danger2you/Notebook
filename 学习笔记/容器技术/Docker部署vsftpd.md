# Docker部署vsftpd


```
docker run -d -v /mnt/data/vsftp:/home/vsftpd -p 8020:20 -p 8021:21 -p 8400-8410:8400-8410 -e FTP_USER=htdf -e FTP_PASS=htdf123456 -e PASV_MIN_PORT=8400 -e PASV_MAX_PORT=8410 --name htdf-vsftpd --restart=always fauria/vsftpd
```