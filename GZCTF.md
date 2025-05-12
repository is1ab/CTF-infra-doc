新生盃平台，使用 [GZTimeWalker/GZCTF: The GZ::CTF project, an open source CTF platform.](https://github.com/GZTimeWalker/GZCTF)

1. 建立資料夾 `gzctf`
2. 建立檔案 `appsettings.json`、`docker-compose.yaml`、`kube-config.yaml`
3. `docker compose up -d`





appsettings.json
```
{
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "Database": "Host=db:5432;Database=gzctf;Username=postgres;Password=這裡要修改"
  },
  "EmailConfig": {
    "SendMailAddress": "a@a.com",
    "UserName": "",
    "Password": "",
    "Smtp": {
      "Host": "localhost",
      "Port": 587
    }
  },
  "XorKey": "這裡要修改",
  "ContainerProvider": {
    "Type": "Kubernetes", // or "Kubernetes"
    "PortMappingType": "PlatformProxy",//"Default", // or "PlatformProxy"
    "EnableTrafficCapture": true,
    "PublicEntry": "這裡要修改", // or "xxx.xxx.xxx.xxx"
    // optional
    "DockerConfig": {
      "SwarmMode": false,
      "Uri": "unix:///var/run/docker.sock"
    }
  },
  "RequestLogging": true,
  "DisableRateLimit": true,
  "RegistryConfig": {
    "UserName": "",
    "Password": "",
    "ServerAddress": ""
  },
  "ForwardedOptions": {
    "ForwardedHeaders": 5,
    "ForwardLimit": 1,
    "TrustedNetworks": ["0.0.0.0/32"]
  }
}
```
docker-compose.yaml
```
services:
  gzctf:
    image: gztime/gzctf:latest
    restart: always
    environment:
      - "GZCTF_ADMIN_PASSWORD=這裡要修改"
      - "LC_ALL=zh_TW.UTF-8"
    ports:
      - "8088:8080" //這裡可以修改
    volumes:
      - "./data/files:/app/files"
      - "./appsettings.json:/app/appsettings.json:ro"
      - "./kube-config.yaml:/app/kube-config.yaml:ro" # this is required for k8s deployment
      # "/var/run/docker.sock:/var/run/docker.sock" # this is required for docker deployment
    depends_on:
      - db
  db:
    image: postgres:alpine
    restart: always
    environment:
      - "POSTGRES_PASSWORD=這裡跟 docker-compose 裡的設定要一樣"
    volumes:
      - "./data/db:/var/lib/postgresql/data"
```
kube-config.yaml
```
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: 這裡要修改
    server: https://server ip:6443
  name: default
contexts:
- context:
    cluster: default
    user: default
  name: default
current-context: default
kind: Config
preferences: {}
users:
- name: default
  user:
    client-certificate-data: 這裡要修改
    client-key-data: 這裡要修改
```