1.NGINX 官网地址：http://nginx.org/ <br/>
http://nginx.org/en/download.html

2.下载.zip
下载下的压缩文件解压为文件夹

# 前端项目打包dist文件
# 先把html文件夹里文件都删除， 把前端打包好的dist文件复制到html文件夹里

# 找到文件夹下的nginx.conf文件，打开此文件进行服务器的配

 server {
        # 例如本机ip地址 10.12.43.11 其它机器访问 10.12.43.11:9008 或者 10.12.43.11:9008/prod-api/
        listen  9008;//自定义端口号
        server_name  localhost; //此处可以自定义ip地址例如 127.168.101.2

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;//根目录的地址例如 html/dist
            index  index.html index.htm;
        }
        # 配置后端服务器地址映射
        location ^~/prod-api/ {
            proxy_pass   http://127.168.2.10:5050/;
        }
}
# 配置工作都完成之后 重启NGINX,鼠标双击 nginx.exe 文件
# 关闭NGINX服务器，到进程里管道nginx开头的文件

3.Nginx 配置详解
地址：https://www.runoob.com/w3cnote/nginx-setup-intro.html

 nginx 配置文件 nginx.conf 内容详解如下：
 ########### 每个指令必须有分号结束。#################
#user administrator administrators;  #配置用户或者组，默认为nobody nobody。
#worker_processes 2;  #允许生成的进程数，默认为1
#pid /nginx/pid/nginx.pid;   #指定nginx进程运行文件存放地址
error_log log/error.log debug;  #制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
events {
    accept_mutex on;   #设置网路连接序列化，防止惊群现象发生，默认为on
    multi_accept on;  #设置一个进程是否同时接受多个网络连接，默认为off
    #use epoll;      #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
    worker_connections  1024;    #最大连接数，默认为512
}
http {
    include       mime.types;   #文件扩展名与文件类型映射表
    default_type  application/octet-stream; #默认文件类型，默认为text/plain
    #access_log off; #取消服务日志    
    log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; #自定义格式
    access_log log/access.log myFormat;  #combined为日志格式的默认值
    sendfile on;   #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
    sendfile_max_chunk 100k;  #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
    keepalive_timeout 65;  #连接超时时间，默认为75s，可以在http，server，location块。

    upstream mysvr {   
      server 127.0.0.1:7878;
      server 192.168.10.121:3333 backup;  #热备
    }
    error_page 404 https://www.baidu.com; #错误页
    server {
        keepalive_requests 120; #单连接请求上限次数。
        listen       4545;   #监听端口
        server_name  127.0.0.1;   #监听地址       
        location  ~*^.+$ {       #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
           #root path;  #根目录
           #index vv.txt;  #设置默认页
           proxy_pass  http://mysvr;  #请求转向mysvr 定义的服务器列表
           deny 127.0.0.1;  #拒绝的ip
           allow 172.18.5.54; #允许的ip           
        } 
    }
}
