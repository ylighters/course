# nginx配置ssl
## 1、检查是否安装ssl模块
在配置ssl证书之前，要确保你的nginx已经安装了ssl模块，一般情况下自己安装的nginx都是不存在ssl模块的。

这里先检查下自己是否存在ssl模块：

进入到你的nginx安装目录下面，我的目录是在（/usr/local/nginx），如果你的nginx安装步骤和上面的文章一致的话，那你的目录和我应该是一致的

进入到目录的sbin目录下，输入

>注意这里是大写的V，小写的只显示版本号 
> ./nginx -V  

如果出现 (configure arguments: --with-http_ssl_module), 则已安装

一般情况下都是不存在ssl模块的，接下来进入到你的解压缩后的nginx目录，注意这里不是nginx安装目录，是解压缩后的目录，我的是在（/usr/local/java/nginx），进入目录后，输入

>./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module

接下来执行

>make

切记不要执行make install，否则会重新安装nginx

上述操作执行完成以后，你的目录下会出现objs文件夹，文件夹内存在nginx文件

接下来使用新的nginx文件替换掉之前安装目录sbin下的nginx，注意这里的替换的时候可以先将之前的文件备份下，停掉nginx服务

>./nginx -s stop  #停止nginx服务

**替换之前的nginx**

>cp /usr/local/java/nginx/objs/nginx /usr/local/nginx/sbin

成功之后，进入到nginx安装目录下，查看ssl时候成功

注意这里是大写的V，小写的只显示版本号
>./nginx -V

可以看到这里出现了configure arguments: --with-http_ssl_module   证明已经安装成功

## 2、配置ssl证书
在阿里云生产域名证书
或者证书免费网站:[freessl](https://freessl.cn/)

## 3、nginx配置文件
```

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
worker_connections  1024;
}


http {
include       mime.types;
default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
		listen       8080;
		server_name  xxx.xxxx.com;
		ssl_certificate   cert.pem;
    		ssl_certificate_key  cert.key;
    		ssl_session_timeout 5m;
    		ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
   		ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    		ssl_prefer_server_ciphers on;

		#后台服务配置，配置了这个location便可以通过http://域名/jeecg-boot/xxxx 访问		
		location ^~ /jeecg-boot {
			proxy_pass              http://127.0.0.1:8081/jeecg-boot/;
			proxy_set_header        Host 127.0.0.1;
			proxy_set_header        X-Real-IP $remote_addr;
			proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
		}
		#解决Router(mode: 'history')模式下，刷新路由地址不能找到页面的问题
		location / {
			root   html;
			index  index.html index.htm;
			if (!-e $request_filename) {
				rewrite ^(.*)$ /index.html?s=$1 last;
				break;
			}
		}
	}

	
	server {
		listen       8082 ssl;
		server_name  xxx.xxxxx.com;
		ssl_certificate   cert.pem;
    		ssl_certificate_key  cert.key;
    		ssl_session_timeout 5m;
    		ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
   		ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    		ssl_prefer_server_ciphers on;


		location ^~ /jeecg-boot {
			proxy_pass              http://127.0.0.1:8081/jeecg-boot/;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header Host $host;
			proxy_set_header Referer $http_referer;
			proxy_set_header X-Real-Port $remote_port;
			proxy_set_header X-Real-User $remote_user;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

		}
		location / {
			index  index.html index.htm;
			if (!-e $request_filename) {
				rewrite ^(.*)$ /index.html?s=$1 last;
				break;
			}
		}
		location /aishandong {
			alias   /usr/local/nginx/html-aishandong/;
			try_files $uri $uri/ /index.html;
                                                index  index.html index.htm;
		}
	}
	
    server {
		listen       8083;
		server_name  88.88.88.88;
		location / {
			root   /usr/local/nginx/html-aishandong/;
			index index.html index.htm; 
		}
	}


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```





