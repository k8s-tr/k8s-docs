---
layout: default
title: Sürekli Güncellemeler
nav_order: 2
parent: Önemli Kaynaklar
---

# Sürekli Güncellemeler (Rolling Updates)

```sh
kubectl config set-context --current --namespace=myspace
```


```sh
kubectl apply -f https://raw.githubusercontent.com/redhat-scholars/kubernetes-tutorial/master/apps/kubefiles/myboot-deployment-resources-limits.yml

kubectl apply -f https://raw.githubusercontent.com/redhat-scholars/kubernetes-tutorial/master/apps/kubefiles/myboot-service.yml

watch kubectl get pods



# Strateji tipi 

kubectl describe deployment myboot
...
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
...

```

Diğer terminal

```sh

# ip port servisten alıyoruz 

while true
do curl $IP:$PORT
sleep .3
done
```

ilk terminal

```sh
# replica sayısını artır

kubectl edit deployment myboot

# yada 
kubectl scale --replicas=2 deploy/myboot
```

```sh
# yenisinin nasıl ayağa kalktığına bakalım
kubectl get pods
```

İmajı değiştirelim. 

```sh
kubectl edit deployment myboot

# eski imaj
- image: quay.io/rhdevelopers/myboot:v1

# yeni imaj
- image: quay.io/rhdevelopers/myboot:v2


# ya da 
kubectl set image deployment myboot myboot=quay.io/rhdevelopers/myboot:v2

# izleyelim
kubectl get pods
```
Diğer terminaldeki çıktıya da bakalım.


kurulumun durumu

```sh
kubectl rollout status deployment myboot

deployment "myboot" successfully rolled out

# durumu
kubectl describe deployment myboot
```

Başka örnek
https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/


