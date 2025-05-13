安裝 nginx
建立 ssl 憑證
```
mkdir -p ssl
openssl req -x509 -newkey rsa:2048 -nodes \
  -keyout ssl/ssl.key -out ssl/ssl.crt \
  -days 365 -subj "/CN=localhost"
```
若需要新增 port 使用此樣板
```
server {
    listen <port> ssl;
    server_name _;

    ssl_certificate /home/is1ab/ssl/ssl.crt;
    ssl_certificate_key /home/is1ab/ssl/ssl.key;

    location / {
        proxy_pass http://0.0.0.0:<port>;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

/etc/nginx/nginx.conf
```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
#include /etc/nginx/modules-enabled/*.conf;

events {
        worker_connections 768;
        # multi_accept on;
}

http {

        ##
        # Basic Settings
        ##

        sendfile on;
        tcp_nopush on;
        types_hash_max_size 2048;
        # server_tokens off;

        # server_names_hash_bucket_size 64;
        # server_name_in_redirect off;

        include /etc/nginx/mime.types;
        default_type application/octet-stream;

        ##
        # SSL Settings
        ##

        ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
        ssl_prefer_server_ciphers on;

        ##
        # Logging Settings
        ##

        access_log /var/log/nginx/access.log;
        error_log /var/log/nginx/error.log;

        ##
        # Gzip Settings
        ##

        gzip on;

        # gzip_vary on;
        # gzip_proxied any;
        gzip_comp_level 6;
        # gzip_buffers 16 8k;
        # gzip_http_version 1.1;
        gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

        ##
        # Virtual Host Configs
        ##

#       include /etc/nginx/conf.d/*.conf;
#       include /etc/nginx/sites-enabled/*;

        client_max_body_size 100m;

    # CTFd
    server {
        # 加入 SSL 設定
        listen 443 ssl;
        server_name _;
        #listen [::]:443 ssl default_server;

        # 憑證與金鑰的路徑
        ssl_certificate /home/is1ab/ssl/ssl.crt;
        ssl_certificate_key /home/is1ab/ssl/ssl.key;

        location / {
            proxy_pass http://0.0.0.0:80;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
    # GZCTF
    server {
        listen 8443 ssl;
        server_name _;

        ssl_certificate /home/is1ab/ssl/ssl.crt;
        ssl_certificate_key /home/is1ab/ssl/ssl.key;

        location / {
            proxy_pass http://0.0.0.0:8080;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
    # zipline
    server {
        listen 3443 ssl;
        server_name _;

        ssl_certificate /home/is1ab/ssl/ssl.crt;
        ssl_certificate_key /home/is1ab/ssl/ssl.key;

        location / {
            proxy_pass http://0.0.0.0:3000;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}

```