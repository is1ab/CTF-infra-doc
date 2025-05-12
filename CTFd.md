穩定運作平台，新生盃結束後會將題目轉移到這裡，[dlut-sss/CTFd-Public: 基于CTFd 3.5.3 版本二次开发,整合CTFd-Whale插件,CTFd-Owl插件,比赛计分板插件,自动备份插件,验证码插件,题解收集插件,SMTPViaHTTP插件并进行修复和重构的一键部署版。](https://github.com/dlut-sss/CTFd-Public)

whale 為使用 dockerfile 建立單個 container，選擇 dymaic_docker 建立題目  
owl 為使用 docker compose 建立多個 container，選擇 dymaic_docker_compose 建立題目

## 1. `git clone` 後將資料夾重新命名為 `CTFd`
## 2. 設定 frps
```
cd
wget https://github.com/fatedier/frp/releases/download/v0.56.0/frp_0.56.0_linux_amd64.tar.gz
tar -zxvf frp_0.56.0_linux_amd64.tar.gz
cd frp_0.56.0_linux_amd64
sudo mkdir /etc/frp
sudo cp frpc.toml frps.toml /etc/frp/
sudo cp frpc frps /usr/bin/
sudo chmod a+x /usr/bin/frpc /usr/bin/frps
```
/etc/frp/frps.toml
```
bindAddr = "0.0.0.0"
bindPort = 7897
auth.method = "token"
auth.token = "token"
```
/etc/systemd/system/CTFd-Frps.service
```
# /etc/systemd/system/CTFd-Frps.service
[Unit]
Description=CTFd Dedicated Frp Server
After=network.target

[Service]
Type=simple
User=nobody
Restart=on-failure
RestartSec=5s
ExecStart=/usr/bin/frps -c /etc/frp/frps.toml

[Install]
WantedBy=multi-user.target
```
啟動 frps
```
sudo systemctl enable CTFd-Frps
sudo systemctl start CTFd-Frps
```
## 3. 建立 docker swarm
```
docker swarm init
docker node update --label-add "name=linux-1" $(docker node ls -q)
```

啟動 CTFd
```
chmod +x docker-entrypoint.sh
sudo docker-compose build
sudo docker-compose up -d
```
## 4. 設定 CTFd
### whale
登入後到管理頁面 > Whale > 路由

API网址: http://172.1.0.4:7400
直连IP地址: server ip
直连最小端口: 10000 可自行設定
直连最大端口: 1099 可自行設定
Frpc 配置模板:  
conf/frp/frpc.ini
```
[common]
token = token
server_addr = 172.17.0.1
server_port = 7897
admin_addr = 172.1.0.4
admin_port = 7400


```
### owl
登入後到管理頁面 > Owl > frp 設定

直连最小端口: 10100 可自行設定
直连最大端口: 10200 可自行設定
Frpc 配置模板:  
conf/compose-frp/frpc.ini
```
[common]
token = token
server_addr = 172.17.0.1
server_port = 7897
admin_addr = 172.1.0.5
admin_port = 7400

```