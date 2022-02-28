---
layout: default
title: Docker
parent: Temel Kavramlar
nav_order: 2
---

# Konteynerler

[devops mimarisi](../kaynaklar/docker-mimari-1.png)  
[docker işlemesi](../kaynaklar/docker-mimari-2.png)

## Önemli Kavramlar

### Imajlar

[imaj katmanları](../kaynaklar/docker-layers-1.png)  
[imaj katmanları 2](../kaynaklar/docker-layers-2.png)  

### Konteynerler

### Kayıt Depoları (registries)

# İstemci Kullanımı

```sh

docker run -d 
docker run -it
docker exec
docker rmi 

docker ps command
docker container name
docker inspect command

```

### İmaj üretme yöntemleri

#### Aktif imajı kaydetmek

```sh

docker commit 

```

#### Dockerfile

Her komut satırı bir katman üretir.

```
docker build -t imaj-adı <path>
docker push 

```

[Örnek Dockerfile](https://hub.docker.com/_/nginx)
