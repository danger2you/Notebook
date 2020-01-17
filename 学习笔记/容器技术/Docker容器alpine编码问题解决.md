# Docker容器alpine编码问题解决


解决示例Dockerfile:
```dockerfile 
FROM anapsix/alpine-java
LABEL name="skydefender-business-webapp"

WORKDIR /app
COPY conf/fonts/msyh.ttf /usr/share/fonts/msyh.ttf
COPY target/skydefender-business-webapp.jar /app/skydefender-business-webapp.jar

RUN echo "https://mirrors.aliyun.com/alpine/v3.6/main/" > /etc/apk/repositories \
        && echo "https://mirrors.aliyun.com/alpine/v3.6/community/" >> /etc/apk/repositories \
        && chmod a+r /usr/share/fonts/msyh.ttf \
        && apk update \
        && apk add font-adobe-100dpi \
        && cd /usr/share/fonts/ \
        && fc-cache -f

CMD export LC_ALL=C.UTF-8 \
    && cd /app \
    && java -jar skydefender-business-webapp.jar --spring.profiles.active=test -Dfile.encoding=UTF-8



```