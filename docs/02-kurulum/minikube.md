---
layout: default
title:  minikube
parent: Kurulum
nav_order: 5
---

# kurulum

# network policy için

```bash
minikube start --cni=calico
```

kubectl ve kube_config için

```bash

# aşağıdaki host makinada yapılmalı 
# curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"


# minikube mount ./:/home/docker/host & 


# minikube ssh

sudo cp host/kubectl /usr/bin/kubectl

sudo chmod +x /usr/bin/kubectl

sudo mkdir -p .kube

sudo cp /etc/kubernetes/admin.conf .kube/config

sudo chown docker. .kube/config

kubectl get nod

```

## additional minikube



```bash
eval $(minikube docker-env)

docker build -t my-local-image .

kubectl run mypod --image=my-local-image --image-pull-policy=Never
```

## addons

```bash
minikube addons enable metrics-server
minikube addons enable dashboard

minikube dashboard --url
```