---
layout: default
title: kubectl
parent: Temel Kavramlar
nav_order: 5
---

# kubectl: Asıl Kubernetes İstemcisi

```sh
kubectlecho $KUBECONFIG

/home/user/.kube/config

kubectl config view


apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://apiserver:6443
  name: cluster.local

```
* Nodları incele

```sh
kubectl get nodes

# etiketleriyle göster (show-labels'ın çok faydalı olduğu yerler var)
kubectl get nodes --show-labels

# ad alanlarını gör
kubectl get namespaces

```

* Herşeyi gör

```sh
kubectl get pods [--all-namespaces|-A]
kubectl get pods [--all-namespaces|-A] --show-labels
kubectl get pods [--all-namespaces|-A] -o wide

```

* Birşeyler kur

```sh
kubectl create namespace mystuff
kubectl config set-context --current --namespace=mystuff

kubectl create deployment myapp --image=quay.io/rhdevelopers/quarkus-demo:v1

```

* Birşeyler kurulumunu izleyerek bekle

```sh
watch kubectl get events --sort-by=.metadata.creationTimestamp

```

### Nesneler oluştur

#### Deployments


```sh
kubectl get deployments
```

#### Replicasets

```sh
kubectl get replicasets

```

#### Pods

```sh
kubectl get pods --show-labels

```

#### Logs 

```sh
kubectl logs -l app=myapp

```

#### Expose: Bir servisi dışarı aç

```sh
kubectl expose deployment myapp --port=8080 --type=LoadBalancer

watch kubectl get services

```

#### Uygulamayı ölçeklendir

```sh
watch kubectl get pods

kubectl scale deployment myapp --replicas=3

#v1
kubectl set image deployment/myapp quarkus-demo=quay.io/rhdevelopers/myboot:v1
#v2
kubectl set image deployment/myapp quarkus-demo=quay.io/rhdevelopers/myboot:v2
#v3
kubectl set image deployment/myapp quarkus-demo=quay.io/rhdevelopers/quarkus-demo:v1


kubectl exec -it quarkus-demo-deployment-5979886fb7-c888m -- curl localhost:8080
```

#### Temizlik

```sh
kubectl delete namespace mystuff
kubectl config set-context --current --namespace=default

```





