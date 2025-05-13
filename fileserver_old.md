若檔案太大，建議將檔案放置在此

1. 建立資料夾 `file-server`
2. 在資料夾內建立資料夾 `file`、`ssl`
3. 在 `file-server` 中建立檔案 `default.conf`、`docker-compose.yaml`
4. 在 `file-server` 資料夾中建立憑證
```
mkdir -p certs
openssl req -x509 -newkey rsa:2048 -nodes \
  -keyout certs/key.key -out certs/cert.pem \
  -days 365 -subj "/CN=localhost"
```
5. `docker compose up -d`
6. https://ip:8080

**要存取的檔案放在 `file` 資料夾內**

---

基本資料可以參考下面
```
Country Name (2 letter code) [AU]:TW
State or Province Name (full name) [Some-State]:Taiwan
Locality Name (eg, city) []:Taipei
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []: server ip
Email Address []:
```


docker-compose.yaml
```
version: '2.7'

services:
    nginx:
      image: nginx:stable
      restart: always
      volumes:
        - ./ssl:/etc/ssl
        - ./file:/etc/file
        - ./default.conf:/etc/nginx/conf.d/default.conf
      ports:
        - "8080:443"
```

default.conf
```
server {
    listen       443 ssl default_server;
    listen  [::]:443 ssl default_server;
    server_name  _;

    root /etc/file;

    ssl_certificate /etc/ssl/cert.crt;
    ssl_certificate_key /etc/ssl/cert.key;

    location / {
        autoindex on;
        try_files $uri $uri/ =404;
    }
}
```
