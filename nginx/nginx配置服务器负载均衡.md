# nginx配置服务器负载均衡
## 1、注意点
多个服务器后端共同使用时，需要配置nginx负载均衡来合理分配各个服务器的访问量达到平衡。

###1.服务器之间防火墙端口确保打开，否则负载均衡无作用
## 1、配置文件
```
#user  nginx;
worker_processes auto;
worker_cpu_affinity auto;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

worker_rlimit_nofile 65535;


events {
use epoll;
worker_connections  10240;
multi_accept on;
}


http {
include       mime.types;
default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    proxy_cache_path  /data/cache  levels=1:2 keys_zone=STATIC:10m inactive=96h max_size=40g;

    sendfile        on;
    client_max_body_size    100m;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout 120;

    reset_timedout_connection on;
    
    client_header_buffer_size 256k;

    fastcgi_buffer_size 512k;

    fastcgi_buffers 8 512k;
	
	limit_req_zone $clientRealIp  zone=myLimit:10m rate=10r/s;

    limit_conn_zone $clientRealIp zone=addr:10m;

	map $http_x_forwarded_for  $clientRealIp {
	"" $remote_addr;
	~^(?P<firstAddr>[0-9\.]+),?.*$ $firstAddr;
	}
	
	
	
	

    #gzip  on;
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
server 192.168.1.1:8002 weight=5 max_fails=5 fail_timeout=600s ;
server 192.168.1.2.83:8002 weight=4 max_fails=5 fail_timeout=600s ;
}

    server {
        listen       8001 ssl;
        server_name  www.ylighter.com;
      ssl_certificate   cert.pem;
    		ssl_certificate_key  cert.key;
    		ssl_session_timeout 5m;
    		ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
   		ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    		ssl_prefer_server_ciphers on;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   /home/jnfy/vue;
	try_files $uri $uri/ /index.html;
            index  index.html index.htm;
        }
	location /prod-api/{
			proxy_http_version	1.1;
            proxy_set_header	Connection "";
			proxy_set_header Host $http_host;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header REMOTE-HOST $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_next_upstream off;
			proxy_pass http://tomcats;


			proxy_connect_timeout    600;
			proxy_read_timeout       600;
			proxy_send_timeout       600;
            proxy_buffer_size  128k;
            proxy_buffers 100  128k; 
		    client_max_body_size 100m;
			proxy_cache            STATIC;

			proxy_cache_valid      200  10d;
            proxy_cache_use_stale  error timeout invalid_header updating http_500 http_502 http_503 http_504;
						
		    proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";


		}

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
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
