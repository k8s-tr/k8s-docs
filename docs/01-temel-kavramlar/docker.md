---
layout: default
title: Docker
parent: Temel Kavramlar
nav_order: 2
---

# Konteynerler

* [devops mimarisi](../kaynaklar/docker-mimari-1.png)  
* [docker işlemesi](../kaynaklar/docker-mimari-2.png)

## Önemli Kavramlar

### Imajlar

* [imaj katmanları](../kaynaklar/docker-layers-1.png)  
* [imaj katmanları 2](../kaynaklar/docker-layers-2.png)  

### Konteynerler

### Kayıt Depoları (registries)

# İstemci Kullanımı

```sh

# ayağa kaldır
docker run -d 
docker run -it

# 
docker ps [-a]
docker inspect <container_name>
docker top <container_name>


# ayağa konsola eriş
docker exec 

# imaj sil
docker rmi
 

```

### docker build



#### Aktif imajı kaydetmek

```sh

docker commit 

```

#### Dockerfile

* Her komut satırı bir katman üretir.

Örnek-1

```
FROM nginx
COPY . /usr/share/nginx/html

```

Örnek-2

* Basit bir nginx config dosyası
   
```
server {

    listen 80 default_server;


    server_name _;


    return 301 https://google.com;

}

```
* Dockerfile

```

```

Örnek-3

* ENV parametrelerini değiştiren 

nginx config file

```
server {

    listen ${NGINX_PORT} default_server;


    server_name _;

    location /${APP_LOCATION} { 
            return 301 https://google.com;
    }    

    location / { 
            return 301 https://tubitak.gov.tr;
    }    
    
}

```
* Dockerfile

```
FROM nginx

ENV APP_LOCATION google
ENV NGINX_PORT 8080

COPY config/orig.conf /etc/nginx/conf.d/orig.conf

run envsubst < /etc/nginx/conf.d/orig.conf > /etc/nginx/conf.d/default.conf

run rm /etc/nginx/conf.d/orig.conf


```


```
docker build -t imaj-etiket:sürüm <path>

docker build -t best-engineks:1 .

# taglemek de işe yarar bir özelliktir. 
docker tag eski-imaj:sürüm yeni-imaj:yeni-sürüm 

docker login 

docker push 




```

[Örnek Dockerfile](https://hub.docker.com/_/nginx)

