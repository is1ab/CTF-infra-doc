[crocofied/PortNote: Keep track of used ports](https://github.com/crocofied/PortNote)

建立資料夾並執行

docker-compose.yaml
```
services:
  web:
    image: haedlessdev/portnote:latest
    ports:
      - "3030:3000"
    environment:
      JWT_SECRET:  # Replace with a secure random string
      USER_SECRET:  # Replace with a secure random string
      LOGIN_USERNAME:  # Replace with a username
      LOGIN_PASSWORD:  # Replace with a custom password
      DATABASE_URL: "postgresql://postgres:postgres@db:5432/postgres"
    depends_on:
      agent:
        condition: service_started
      db:
        condition: service_started
  agent:
    image: haedlessdev/portnote-agent:latest
    environment:
      DATABASE_URL: "postgresql://postgres:postgres@db:5432/postgres"
    depends_on:
      db:
        condition: service_started
  db:
    image: postgres:17
    restart: always
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: postgres
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

## ctf server 140.124.181.153
22 ssh  
80 CTFd  
443 CTFd with https  
2377 docker swarm TCP  
3000 zipline fileserver  
3030 portnote  
3443 zipline with https  
5000 docker registry  
6443 k3s server  
7897 frps  
7946 docker swarm TCP  
8080 GZCTF  
8443 GZCTF with https  
9000 portainer.io  
9993 zerotier  
10250 k3s API  