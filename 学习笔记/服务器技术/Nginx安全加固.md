# Nginx安全加固
## 1. 禁止版本信息获取
禁止 nginx 在响应中报文中包含版本信息。因为具体的版本可能会存在未知 bug
```lua
server_tokens off;
```
## 2. 避免点击劫持
该响应头用于是否允许浏览器加载 frame、 iframe、 object 等属性。
该指令用三个可用的配置
* X-Frame-Options: DENY   站点禁止任何页面被嵌入
* X-Frame-Options: SAMEORIGIN   只允许加载同源的 fram/iframe/object
* X-Frame-Options: ALLOW-FROM https://example.com/  只允许加载指定的源
```lua
add_header X-Frame-Options SAMEORIGIN;
```
 
## 3. XSS过滤
防范及过滤 XSS 的。可用的几个指令如下：
防范及过滤 XSS 的。可用的几个指令如下：
* X-XSS-Protection: 0    禁用 XSS 过滤
* X-XSS-Protection: 1    开启 XSS 过滤
* X-XSS-Protection: 1; mode=block     开启 XSS 过滤，并且若检查到 XSS 攻击，停止渲染页面
* X-XSS-Protection: 1; report=<reporting-uri>   开启 XSS 过滤，并且若检查到 XSS 攻击，将使用指导的 url 来发送报告
```lua
add_header X-XSS-Protection "1; mode=block";
```
 
 
## 4.  封杀user-agent
user-agent 也即浏览器标识，每个正常的web请求都包含用户的浏览器信息，除非经过伪装，恶意扫描工具一般都会在user-agent里留下某些特征字眼，比如scan，nmap等。我们可以用正则匹配这些字眼，从而达到过滤的目的
```lua
if ($http_user_agent ~* "java|python|perl|ruby|curl|bash|echo|uname|base64|decode|md5sum|select|concat|httprequest|httpclient|nmap|scan" ) {
    return 403;
}
if ($http_user_agent ~* "" ) {
    return 403;
}
```
 
阻止用户代理，如扫描器，机器人和垃圾邮件
```lua
if ($http_user_agent ~* LWP::Simple|BBBike|wget) {        
        return 403;    
}
if ($http_user_agent ~* msnbot|scrapbot) {          
        return 403;    
}
```
 
## 5. 防止目录篡改
将网站目录设置为只读
 
 
## 6. 防止缓冲区溢出攻击
设置所有客户端可用的缓冲区大小限制
* client_body_buffer_size 1k：默认是8k或16k，指定客户端请求主体缓冲区大小。
 
* client_header_buffer_size 1k：指定来自客户端请求头的headerbuffer大小，对于大多数请求，1k的缓冲区大小已经足够，如果你自定义了消息头或有更大的Cookie，你可以增加其大小。
 
* client_max_body_size 1k：客户端请求中可接受的主体最大大小，由请求头中的Content-Length表示，如果大小大于给定的尺寸，客户端将会收到一条“Request Entity Too Large（413）”的错误，如果你要通过POST方法上传文件，可以将该值设大一些。
 
* large_client_header_buffers 2 1k：为客户端请求中较大的消息头指定的缓存最大数量和大小，默认情况下，一个缓冲区的大小等于页面的大小，根据平台的不同可能是4K或8K，如果在请求连接的末尾状态转换为保持活动（keep-alive），这些缓冲区就被释放，2x1K将可以接收2KB数据的URI，这样有助于打击机器人攻击和DoS攻击
 
## 7. Audited 审计
将Web目录加入审计规则
 
 
 
## 8. Timeout 防御DDOS
```lua
client_body_timeout   10;
client_header_timeout  30;
keepalive_timeout     30  30;
send_timeout          10;
```
 
## 9.禁用autoindex模块
不知道什么软用
```lua
autoindex off;
```