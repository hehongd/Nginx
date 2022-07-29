<!-- NGINX 官网地址 -->
http://nginx.org/en/download.html

<!-- 下载.zip -->
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