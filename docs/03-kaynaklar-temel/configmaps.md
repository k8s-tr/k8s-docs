---
layout: default
title:  ConfigMaps
parent: Temel Kaynaklar
nav_order: 3
---

# configmap

**ConfigMap**, uygulamanızın yapılandırmasını harici hale getirmenize izin veren Kubernetes kaynağıdır.

* https://12factor.net/config


Uygulamayı ve servisi kuralım


```sh
kubectl apply -f https://raw.githubusercontent.com/redhat-scholars/kubernetes-tutorial/master/apps/kubefiles/myboot-deployment.yml

kubectl apply -f https://raw.githubusercontent.com/redhat-scholars/kubernetes-tutorial/master/apps/kubefiles/myboot-service.yml
```

configure tanımına bakalım
```sh
curl $IP:$PORT/configure

```

çevresel değişkenleri elle girelim (amele yöntem)

```sh
kubectl set env deployment/myboot GREETING="namaste"
kubectl set env deployment/myboot LOVE="Aloha"
kubectl set env deployment/myboot DBCONN="jdbc:sqlserver://45.91.12.123:1443;user=MyUserName;password=*****;"

# podlar yeni çdleri almak için yeniden oluşacaklardır.

NAME                      READY   STATUS        RESTARTS   AGE
myboot-66d7d57687-jsbz7   1/1     Terminating   0          5m
myboot-785ff6bddc-ghwpc   1/1     Running       0          13s
```
yeni çdleri görebiliriz.
```sh
kubectl describe deployment myboot

...
  Containers:
   myboot:
    Image:      quay.io/burrsutter/myboot:v1
    Port:       8080/TCP
    Host Port:  0/TCP
    Environment:
      GREETING:  namaste
      LOVE:      Aloha
      DBCONN:    jdbc:sqlserver://45.91.12.123:1443;user=MyUserName;password=*****;
    Mounts:      <none>
  Volumes:       <none>
...
```

çdleri kaldıralım

```sh

kubectl set env deployment/myboot GREETING-
kubectl set env deployment/myboot LOVE-
kubectl set env deployment/myboot DBCONN-

```
curl ile bakalım
```sh
curl $IP:$PORT/configure

Configuration for : myboot-66d7d57687-xkgw6
databaseConn=Default
msgBroker=Default
greeting=Default
love=Default
```

## ConfigMap

https://github.com/redhat-scholars/kubernetes-tutorial/blob/master/apps/config/some.properties


```sh
kubectl create cm my-config --from-env-file=apps/config/some.properties

kubectl get cm
kubectl get cm my-config
kubectl get cm my-config -o json

...
    "data": {
        "GREETING": "jambo",
        "LOVE": "Amour"
    },
    "kind": "ConfigMap",
...

kubectl describe cm my-config

Name:         my-config
Namespace:    myspace
Labels:       <none>
Annotations:  <none>

Data
====
GREETING:
====
jambo
LOVE:
====
Amour
Events:  <none>
```

Bu configmap'i kullanan uygulamayı kuralım

```sh
kubectl apply -f https://raw.githubusercontent.com/redhat-scholars/kubernetes-tutorial/master/apps/kubefiles/myboot-deployment-configuration.yml
```

İşe yaramış mı?
```sh
curl $IP:$PORT/configure

Configuration for : myboot-84bfcff474-x6xnt
databaseConn=Default
msgBroker=Default
greeting=jambo
love=Amour
```

Başka bir configmap oluşturup ona geçiyoruz

```sh

kubectl delete cm my-config
kubectl create cm my-config --from-env-file=apps/config/other.properties
kubectl delete pod -l app=myboot

curl $IP:$PORT/configure


Configuration for : myboot-694954fc6d-nzdvx
databaseConn=jdbc:sqlserver://123.123.123.123:1443;user=MyUserName;password=*****;
msgBroker=tcp://localhost:61616?jms.useAsyncSend=true
hello=Default
love=Default

```

config map
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: game-demo
data:
  # özellik tipi anahtarlar; her anahtar bir değeri adresler
  player_initial_lives: "3"
  ui_properties_file_name: "user-interface.properties"
  #
  # dosya tipi anahtarlar
  game.properties: |
    enemy.types=aliens,monsters
    player.maximum-lives=5
  user-interface.properties: |
    color.good=purple
    color.bad=yellow
    allow.textmode=true

```

deployment

```yaml

apiVersion: v1
kind: Pod
metadata:
  name: configmap-demo-pod
spec:
  containers:
    - name: demo
      image: game.example/demo-game
      env:
        # Bir ortam değişkeni tanımla
        - name: PLAYER_INITIAL_LIVES # burada isimlendirme büyük harf diğerinden farklı
          valueFrom:
            configMapKeyRef:
              name: game-demo           # configmap adı 
              key: player_initial_lives # değeri alacağı anahtar
        - name: UI_PROPERTIES_FILE_NAME
          valueFrom:
            configMapKeyRef:
              name: game-demo
              key: ui_properties_file_name
      volumeMounts:
      - name: config
        mountPath: "/config"
        readOnly: true
  volumes:
    # Önce pod seviyesinde dizini tanımlıyoruz ve yukarda container'ın içine bağlıyoruz. 
    - name: config
      configMap:
        # Bağlayacağınız configMap'in adı
        name: game-demo
        # Dosya olarak oluşturacağımız ConfigMapteki anahtarlar dizini. 
        items:
        - key: "game.properties"
          path: "game.properties"
        - key: "user-interface.properties"
          path: "user-interface.properties"

```

## Configmapten script çalıştırma

```bash

#!/bin/sh
echo "Hello from the script baked into the container"
echo "Sleeping for eternity!"
sleep infinity

```

```sh
kubectl create configmap cf-echoscript from-file run.sh
```

Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: echoscript
  labels:
    app: echoscript
spec:
  replicas: 1
  selector:
    matchLabels:
      app: echoscript
  template:
    metadata:
      labels:
        app: echoscript
    spec:
      containers:
      - name: echoscript
        image: busybox
        command: ["/bin/sh"]
        args: ["/scripts-dir/run.sh"]
        volumeMounts:
        - name: scripts-vol
          mountPath: /scripts-dir
      volumes:
      - name: scripts-vol
        configMap:
          name: cf-echoscript
```

```sh
kubectl apply -f echoscript-deployment.yaml
```


Referans
https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/


Temizlik
```sh
kubectl delete deployment myboot
kubectl delete cm my-config
kubectl delete service myboot
```