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

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

chmod +x /usr/bin/kubectl

mv kubectl /usr/bin/

sudo cp /etc/kubernetes/admin.conf .kube/config

kubectl get node

```

## additional minikube



```bash
eval $(minikube docker-env)

docker build -t my-local-image .

kubectl run mypod --image=my-local-image --image-pull-policy=Never
```