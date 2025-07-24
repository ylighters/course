# 若依nginx配置

## 一、正常配置

### 1、nginx.conf配置
+ 配置参考一
```
    server {
        listen       8088;
        server_name  localhost;
		client_max_body_size 1000M;

		location / {
            root   D:/java/nginxhtml;
			try_files $uri $uri/ /index.html;
            index  index.html index.htm;
        }
		
		location /prod-api/{
			proxy_set_header Host $http_host;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header REMOTE-HOST $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_pass http://localhost:8089/prod-api/;
		}

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
```
+ 配置参考二
```aiignore
server {
       listen       8002 ;
       server_name  localhost;
       client_max_body_size 1000M;

		location / {
            root   D:/java/apphtml;
			try_files $uri $uri/ /index.html;
            index  index.html index.htm;
        }
		location /prod-api/{
			proxy_set_header Host $http_host;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header REMOTE-HOST $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_pass http://127.0.0.1:8003/prod-api/;
		    proxy_connect_timeout    600;
		    proxy_read_timeout       600;
		    proxy_send_timeout       600;
            proxy_buffer_size  128k;
            proxy_buffers 100  128k; 
		    client_max_body_size 100m;
		    proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
		}

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
```
+ 跨域问题在后端服务设置 CORS 头，或在 Nginx 中添加：
+ add_header 'Access-Control-Allow-Origin' '*';
### 2、nginx.conf配置同一端口不通项目
```
server {
        listen       80;
		server_name  localhost;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
		# 处理根路径
		location / {
			# 可以在这里添加其他处理逻辑
			# 或者直接返回重定向
			return 301 /care;
		}
		#陪诊陪护管理后台
		location /care {
			alias /opt/website/care/dist/;
			charset utf-8;
			try_files $uri $uri/  /care/index.html;
		}
		
		#泰盈api ----陪诊陪护服务API接口
		location /careapi{
			proxy_headers_hash_max_size 512;    #设置头部哈希表的最大值
			proxy_headers_hash_bucket_size 128;    #设置头部哈希表大小
			proxy_redirect off;    #关闭代理重定向
			proxy_set_header Host $host:$server_port;    #将代理服务WSDL地址转换为代理服务器本机地址
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header REMOTE-HOST $remote_addr;  #获取客户端浏览器的主机名
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			client_max_body_size 300m;    #允许客户端请求的最大单文件字节数
			client_body_buffer_size 1024k;    #缓冲区代理缓冲用户端请求的最大字节数
			proxy_connect_timeout 300;    #nginx跟后端服务器连接超时时间
			proxy_read_timeout 600;    #连接成功后，后端服务器响应时间
			proxy_send_timeout 600;    #后端服务器数据回传时间
			proxy_pass http://127.0.0.1:8004;
			rewrite ^/careapi/(.*)$ /$1 break; 
		}

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
```
### 3、nginx.conf配置ssl证书
+ 需要key证书
+ 处理根路径进行了/care跳转
```aiignore
server {
		listen       443 ssl;
		server_name  test.com;
		ssl_certificate   test.com.pem;
    		 ssl_certificate_key test.com.key;
    		ssl_session_timeout 5m;
    		ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
   		ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    		ssl_prefer_server_ciphers on;

		root         /usr/share/nginx/html;
		# 处理根路径
		location / {
			# 可以在这里添加其他处理逻辑
			# 或者直接返回重定向
			return 301 /care;
		}
		location /care {
			alias /opt/website/care/dist/;
			charset utf-8;
			try_files $uri $uri/  /care/index.html;
		}

		 #-陪诊陪护服务API接口
		location /careapi{
			proxy_headers_hash_max_size 512;    #设置头部哈希表的最大值
			proxy_headers_hash_bucket_size 128;    #设置头部哈希表大小
			proxy_redirect off;    #关闭代理重定向
			proxy_set_header Host $host:$server_port;    #将代理服务WSDL地址转换为代理服务器本机地址
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header REMOTE-HOST $remote_addr;  #获取客户端浏览器的主机名
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			client_max_body_size 300m;    #允许客户端请求的最大单文件字节数
			client_body_buffer_size 1024k;    #缓冲区代理缓冲用户端请求的最大字节数
			proxy_connect_timeout 300;    #nginx跟后端服务器连接超时时间
			proxy_read_timeout 600;    #连接成功后，后端服务器响应时间
			proxy_send_timeout 600;    #后端服务器数据回传时间
			proxy_pass http://127.0.0.1:8004;
			rewrite ^/careapi/(.*)$ /$1 break; 
		}
		
 		error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
        }
		
		
	}
```
	
### 4、http配置
```aiignore
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
# 开启gzip压缩
    gzip on;
    # 不压缩临界值，大于1K的才压缩，一般不用改
    gzip_min_length 1k;
    # 压缩缓冲区
    gzip_buffers 16 64K;
    # 压缩版本（默认1.1，前端如果是squid2.5请使用1.0）
    gzip_http_version 1.1;
    # 压缩级别，1-10，数字越大压缩的越好，时间也越长
    gzip_comp_level 9;
    # 进行压缩的文件类型
    gzip_types text/plain application/x-javascript text/css application/xml application/javascript application/json image/jpeg image/gif image/png;
    # 跟Squid等缓存服务有关，on的话会在Header里增加"Vary: Accept-Encoding"
    gzip_vary on;
    # IE6对Gzip不怎么友好，不给它Gzip了
    gzip_disable "MSIE [1-6]\.";

    client_max_body_size    100m;
    #tcp_nodelay on;
    reset_timedout_connection on;
    client_header_buffer_size 256k;
    fastcgi_buffer_size 512k;
    fastcgi_buffers 8 512k;
}
```

## 二、负载均衡
### 三台服务器配置负载均衡
+ 采用ip_hash
```aiignore


gzip on;
gzip_min_length 1k;
gzip_buffers 4 16k;
gzip_http_version 1.1;
gzip_comp_level 2;
gzip_types text/plain application/x-javascript text/css application/xml application/javascript;
gzip_vary on;
gzip_proxied off;

upstream tomcats {
	ip_hash;
	server 10.2.17.8:8080 weight=1 max_fails=3 fail_timeout=600s ;
	server 10.2.17.9:8080 weight=1 max_fails=3 fail_timeout=600s ;
	server 10.2.17.10:8080 weight=1 max_fails=3 fail_timeout=600s ;
}

server {
    listen       81 reuseport  backlog=2048 ;
    server_name  localhost;
    location / {
        root   /usr/share/nginx/html/pc;
		try_files $uri $uri/ /index.html;
        index  index.html index.htm;
    }
    location /app {
        alias   /usr/share/nginx/html/app/;
		try_files $uri $uri/ /index.html;
        index  index.html index.htm;
    }

    location /status {
      stub_status on;
    }

	location /prod-api/{
        proxy_http_version	1.1;
        proxy_set_header	Connection "";
		proxy_set_header Host $http_host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header REMOTE-HOST $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_next_upstream off;
		proxy_connect_timeout    600;
		proxy_read_timeout       600;
		proxy_send_timeout       600;
		proxy_pass http://tomcats;
		client_max_body_size 10m;
	}

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}

```

## 三、转发代理
```aiignore
server {
    listen 443 ssl;
    server_name your-nginx-server.com;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    location /api/user {
        proxy_pass http://backend-server:8080/user;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Real-IP $remote_addr;
    }

    location /api/orders {
        rewrite ^/api/orders/(.*) /$1 break;
        proxy_pass http://order-service:8000;
    }
}
```