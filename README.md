# CTF-infra-doc
建立 CTF 的相關文件、網路設定與教學

## 系統設定
1. docker
2. docker compose
3. k3s
4. docker registry

## 平台介紹
- 新生盃使用平台(比賽用): GZCTF
- 日常使用平台(練習/上課用): CTFd
- 檔案伺服器: zipline

## 建置步驟
1. 安裝 docker、k3s
2. 建立 GZCTF、CTFd、zipline
    - GZCTF 8080/8443
    - CTFd 80/443
    - zipline 3000/3443
4. 安裝 nginx，建立 https
5. 建立 docker registry
    - `docker run -d -p 5000:5000 --restart always --name registry registry:2`

## 額外管理工具
- 管理 Docker: portainer.io
- 管理 Port: PortNote