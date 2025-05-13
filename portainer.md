[portainer/portainer: Making Docker and Kubernetes management easy.](https://github.com/portainer/portainer)

```
docker volume create portainer_data
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:lts
```

https://localhost:9443