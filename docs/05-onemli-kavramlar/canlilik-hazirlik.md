---
layout: default
title: Canlılık ve Hazırlık
nav_order: 5
parent: Önemli Kaynaklar
---

# Canlılık ve Hazırlık

## Liveness
* Kubernetes’e uygulamanın canlı olup/olmadığının bilgisini verir.
* Eğer Readiness Probe'u fail olursa kubernetes uygulamaya trafik yollamayı keser.
  
## Readiness
* Kubernetes'e uygulamanın ne zaman trafik almaya hazır olduğunun bilgisini verir.
* Eğer uygulama sağlıksız ise kubernetes Pod'u siler ve Pod'un yerine yeni bir tane başlatır.

## Startup
* Herhangi bir sebeple yavaş ayağa kalkan Pod'ların başlama durumunu kontrol için Startup propları kullanılır. 
* Startup proplarının kontrol yaptığı sürede liveness ve readiness propları çalışmaz.


**Probe'lar konfigüre edilebilirdirler:**

- **initialDelaySeconds:** Ne kadar süre sonra health check probe'u test edilmeye başlanacak.
- **periodSeconds:** Ne kadar süre aralıklarıyla test için bu istekler iletilecek.
- **timeoutSeconds:** Ne kadar süre isteğin cevabı beklenecek.
- **successThreshold:** Kaç tane başarılı istekten sonra başarılı sayılacak.
- **failureThreshold:** Kaç tane fail istekten sonra hatalı sayılacak.


```sh
kubectl config set-context --current --namespace=myspace
```

Uygulamayı kur
```sh
kubectl apply -f https://raw.githubusercontent.com/redhat-scholars/kubernetes-tutorial/master/apps/kubefiles/myboot-deployment-startup-live-ready.yml
```

Bekleyip duruma bakalım

```sh
kubectl describe deployment myboot

...
    Image:      quay.io/rhdevelopers/myboot:v1
    Port:       8080/TCP
    Host Port:  0/TCP
    Limits:
      cpu:     1
      memory:  400Mi
    Requests:
      cpu:        250m
      memory:     300Mi
    Liveness:     http-get http://:8080/ delay=10s timeout=2s period=5s #success=1 #failure=3
    Readiness:    http-get http://:8080/health delay=10s timeout=1s period=3s #success=1 #failure=3
...
```

Bozalım
```sh

kubectl exec -it <podadı> /bin/bash
curl $ip:8080/misbehave


kubectl get pod | grep myboot

myboot-654df6dd8f-nps2t               0/1     Running   0          45m
myboot-654df6dd8f-rmddr               1/1     Running   0          36m
myboot-654df6dd8f-c6sk5               1/1     Running   0          36m


kubectl get endpoints myboot -o json | jq '.subsets[].addresses[].ip'

"10.1.166.158"
"10.1.166.159"

```

yeni sürüm kuralım

```sh
kubectl set image deployment/myboot myboot=quay.io/rhdevelopers/myboot:v3


watch kubectl get pods
NAME                      READY   STATUS    RESTARTS   AGE
myboot-56659c9d69-6sglj   1/1     Running   0          2m2s
myboot-56659c9d69-mdllq   1/1     Running   0          97s
myboot-56659c9d69-zjt6q   1/1     Running   0          72s
```

/shot endpointten erişiyoruz
```sh
curl localhost:8080/shot
```





